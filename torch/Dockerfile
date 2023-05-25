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

RUN pip install --upgrade pip \
 && pip install --no-cache-dir \
        --extra-index-url https://download.pytorch.org/whl/cu113 \
        torch==1.12.1 \
        torchvision==0.13.1 \
        albumentations==0.4.3 \
        opencv-python==4.5.5.64 \
        pudb==2019.2 \
        imageio==2.9.0 \
        imageio-ffmpeg==0.4.2 \
        pytorch-lightning==1.4.2 \
        omegaconf==2.1.1 \
        test-tube>=0.7.5 \
        streamlit>=0.73.1 \
        einops==0.3.0 \
        torch-fidelity==0.3.0 \
        transformers==4.22.2 \
        kornia==0.6 \
        webdataset==0.2.5 \
        torchmetrics==0.6.0 \
        fire==0.4.0 \
        gradio==3.1.4 \
        diffusers==0.3.0 \
        datasets[vision]==2.4.0 \
        -e git+https://github.com/CompVis/taming-transformers.git@master#egg=taming-transformers \
        -e git+https://github.com/openai/CLIP.git@main#egg=clip \
        -e git+https://github.com/justinpinkney/nomi.git@e9ded23b7e2269cc64d39683e1bf3c0319f552ab#egg=nomi \


ENTRYPOINT ["python"]

RUN pip install -U jupyterlab ipywidgets jupyter-archive
RUN jupyter nbextension enable --py widgetsnbextension

RUN apt-get clean && rm -rf /var/lib/apt/lists/* && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen

ADD start.sh /

RUN chmod +x /start.sh

CMD [ "/start.sh" ]

# Clone the repository
RUN git clone https://github.com/geniusfoever/stable-diffusion-lambda.git

WORKDIR /stable-diffusion-lambda

RUN pip install --no-cache-dir -e .
