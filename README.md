# Azure DevOps Containerized Agents on Demand

Azure DevOps on demand agents will help you to leverage Kubernetes as your build cluster, it will automatically provision new build agents based on build demand.

## Content

This repo provides the following resources

- agent-images: Dockerfiles to create the docker images for use in your cluster
- helm-charts: Helm charts for automating the pod orchestration
- azure-pipeline-sample: a pre-build azure-pipeline.yaml to help you get started

## Build Agents Images

```shell
## build base image
cd agent-images/base-image
docker build -t leansoftx/ads-agent-base:latest .

## build init image
cd agent-images/init-image
docker build -t leansoftx/ads-agent-init:latest .

## build init image
## Todo: build more, version specific images
cd agent-images/on-demand-image
docker build -t leansoftx/ads-agent-on-demand:nodejs -f Dockerfile-nodejs .
docker build -t leansoftx/ads-agent-on-demand:dotnet -f Dockerfile-dotnet .

## publish images
docker push leansoftx/ads-agent-base:latest
docker push leansoftx/ads-agent-init:latest
docker push leansoftx/ads-agent-on-demand:nodejs
docker push leansoftx/ads-agent-on-demand:dotnet
```

## Prepare Helm Charts

```shell
/helm-charts/src$ helm package ads-agent-init
/helm-charts/src$ helm package ads-agent-on-demand
/helm-charts/src$ mv *.tgz ../_package/
/helm-charts/_package$ helm repo index .
```

## Deploy Init Agents

```shell
## grant service account enough permission
kubectl create clusterrolebinding add-on-cluster-admin \
  --clusterrole=cluster-admin \
  --serviceaccount=ads-agents:default

cd helm-charts/src/ads-agent-init

## Create a PAT on Azure DevOps and replace YOUR_ADS_PAT with it
export PAT_TOKEN=$(echo -n 'YOUR_ADS_PAT' | base64)

## install from local helm chart files
helm install --namespace ads-agents --set adsToken=${PAT_TOKEN} ads-agent-init  .

## install from hosted helm chart
helm repo add leansoftx https://raw.githubusercontent.com/ups216/ads-container-agents/main/helm-charts/_package/
helm repo update
helm install --namespace ads-agents --set adsToken=${PAT_TOKEN} ads-agent-init leansoftx/ads-agent-init 

## uninstall
helm uninstall ads-agent-init -n ads-agents

```

## Deploy On-demand Agents

```shell
## normally this is deployed by triggering a new build
## this is just to test if the helm chart is working fine 
cd helm-charts/src/ads-agent-on-demand
## install from local
helm install --namespace ads-agents ads-agent-on-demand  .
## install from hosted
helm repo add leansoftx https://raw.githubusercontent.com/ups216/ads-container-agents/main/helm-charts/_package/
helm repo update
helm install --namespace ads-agents ads-agent-on-demand leansoftx/ads-agent-on-demand
```