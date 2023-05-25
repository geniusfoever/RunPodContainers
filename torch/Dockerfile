ARG BASE_IMAGE=nvidia/cuda:11.3.0-devel-ubuntu20.04

FROM ${BASE_IMAGE} as dev-base

WORKDIR /

RUN mkdir /workspace

SHELL ["/bin/bash", "-o", "pipefail", "-c"]
ENV DEBIAN_FRONTEND noninteractive\
    SHELL=/bin/bash
RUN apt-get update --yes && \
    # - apt-get upgrade is run to patch known vulnerabilities in apt-get packages as
    #   the ubuntu base image is rebuilt too seldom sometimes (less than once a month)
    apt-get upgrade --yes && \
    apt install --yes --no-install-recommends\
    git\
    wget\
    curl\
    bash\
    libgl1\
    software-properties-common\
    openssh-server
RUN add-apt-repository ppa:deadsnakes/ppa
RUN apt install python3.10-dev python3.10-venv -y --no-install-recommends && \
	ln -s /usr/bin/python3.10 /usr/bin/python && \
	rm /usr/bin/python3 && \
	ln -s /usr/bin/python3.10 /usr/bin/python3

RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
RUN python get-pip.py

ENV PYTHONUNBUFFERED True

# Install packages
RUN apt-get update \
 && apt-get install -y --no-install-recommends gcc git ffmpeg libsm6 libxext6 \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["python"]

RUN pip install -U jupyterlab ipywidgets jupyter-archive jupyter_contrib_nbextensions
RUN jupyter contrib nbextension install --user
RUN jupyter nbextension enable --py widgetsnbextension

RUN apt-get clean && rm -rf /var/lib/apt/lists/* && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen

ADD start.sh /

RUN chmod +x /start.sh

CMD [ "/start.sh" ]

# Clone the repository
RUN git clone https://github.com/geniusfoever/stable-diffusion-lambda.git

WORKDIR /stable-diffusion-lambda

RUN pip install -r requirements.txt

RUN pip install huggingface_hub
