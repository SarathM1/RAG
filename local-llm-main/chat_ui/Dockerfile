# app/Dockerfile
FROM python:3.9

ARG PROXY=aaz2kor:DEMO%26BOT1234568@rb-proxy-unix-apac.bosch.com:8080
WORKDIR /app
ARG PROXY
ENV http_proxy "http://$PROXY"
ENV https_proxy "http://$PROXY"
ENV ftp_proxy "ftp://$PROXY"

# RUN apt-get update && apt-get install -y && apt-get install iputils-ping -y \
#     build-essential \
#     curl \
#     software-properties-common \
#     git \
#     && rm -rf /var/lib/apt/lists/*

COPY requirements.txt /app/requirements.txt
COPY gradio_app.py /app/gradio_app.py

RUN pip3 install -r requirements.txt

ENTRYPOINT ["python3", "gradio_app.py"]