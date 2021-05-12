# Azure DevOps baseimage (../base-image/Dockerfile)
# More info: https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops#create-and-build-the-dockerfile-1
FROM leansoftx/ads-agent-base:latest

ENV category init-agent

RUN apt-get update && apt-get install -y \
    wget \
    unzip \
    openssh-client \
    && rm -rf /var/lib/apt/lists/*

ENV KUBE_LATEST_VERSION="v1.21.0"
ENV HELM_VERSION="v3.5.4"

RUN wget -q https://storage.googleapis.com/kubernetes-release/release/${KUBE_LATEST_VERSION}/bin/linux/amd64/kubectl -O /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl \
    && wget -q https://get.helm.sh/helm-${HELM_VERSION}-linux-amd64.tar.gz -O - | tar -xzO linux-amd64/helm > /usr/local/bin/helm \
    && chmod +x /usr/local/bin/helm \
    && chmod g+rwx /root \
    && mkdir /config \
    && chmod g+rwx /config \
    && helm repo add zeiss https://zeisscharts.blob.core.windows.net/on-demand-agents