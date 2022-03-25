FROM debian:buster-slim

ENV KUBECTL_VERSION 1.18.0
ENV AWS_IAM_AUTHENTICATOR_VERSION 1.15.10
ENV ISTIO_VERSION 1.13.2

RUN apt -qq -y update && \
	apt install -y -qq curl unzip make wget vim python python-pip wget

WORKDIR /tools/

### Istioctl ###

RUN curl --silent --location https://istio.io/downloadIstio | ISTIO_VERSION=$ISTIO_VERSION TARGET_ARCH=x86_64 sh - && \
	export PATH=/tools/$ISTIO_VERSION/bin:$PATH 


### CHAOS TOOLKIT ###
RUN pip install chaostoolkit-kubernetes

### KUBECTL ###
RUN curl --silent -LO https://storage.googleapis.com/kubernetes-release/release/v1.16.15/bin/linux/amd64/kubectl && \
     chmod +x ./kubectl && \
     mv ./kubectl /usr/local/bin/kubectl

RUN  curl --silent https://amazon-eks.s3.us-west-2.amazonaws.com/$AWS_IAM_AUTHENTICATOR_VERSION/2020-02-22/bin/linux/amd64/aws-iam-authenticator --output /usr/local/bin/aws-iam-authenticator && \
         chmod +x /usr/local/bin/aws-iam-authenticator

RUN  curl --silent https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip --output awscliv2.zip

RUN  unzip awscliv2.zip  > /dev/null && \
         /bin/bash aws/install && \
         rm awscliv2.zip

RUN curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp && \
     mv /tmp/eksctl /usr/local/bin && \
     chmod +x /usr/local/bin/eksctl

RUN useradd -ms /bin/bash appuser

RUN mkdir /home/appuser/.kube && \
	mkdir -p /home/appuser/.minikube/profiles/minikube

COPY kube/config /home/appuser/.kube/config

COPY kube/ca.crt /home/appuser/.minikube/.

COPY kube/client.crt /home/appuser/.minikube/profiles/minikube/.

COPY kube/client.key /home/appuser/.minikube/profiles/minikube/.

RUN chown -R appuser:appuser /home/appuser/.kube && \
	chown -R appuser:appuser /home/appuser/.minikube

USER appuser

RUN mkdir ~/.aws/ && cd ~/.aws && touch config && touch credentials

RUN export AWS_SHARED_CREDENTIALS_FILE=~/.aws/credentials && export AWS_PROFILE=da-engineer && export AWS_REGION=us-east-1
