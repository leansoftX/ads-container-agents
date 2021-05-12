# Azure DevOps Containerized Agents on Demand

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

```

## Deploy Init Agents

```shell
## grant service account enough permission
kubectl create clusterrolebinding add-on-cluster-admin \
  --clusterrole=cluster-admin \
  --serviceaccount=ads-agents:default

cd helm-charts/src/ads-agent-init

export PAT_TOKEN=$(echo -n 'YOUR_ADS_PAT' | base64)
## install from local path
helm install --namespace ads-agents --set adsToken=${PAT_TOKEN} ads-agent-init  .

## install from hosted charts
helm repo add leansoftx https://raw.githubusercontent.com/ups216/ads-container-agents/main/helm-charts/_package/
helm install --namespace ads-agents --set adsToken=${PAT_TOKEN} ads-agent-init leansoftx/ads-agent-init 

## uninstall
helm uninstall ads-agent-init -n ads-agents


```

## Deploy On-demand Agents

```shell
## normally this is deployed by azure-pipeline.yaml, this is just to test if the chart is working fine 
cd helm-charts/src/ads-agent-on-demand
helm install --namespace ads-agents ads-agent-on-demand  .
helm install --namespace ads-agents ads-agent-on-demand leansoftx/ads-agent-on-demand
```