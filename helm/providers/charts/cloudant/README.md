<!--
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
-->

# Use Helm to deploy Cloudant provider and package

This chart is to deploy Cloudant provider and package to OpenWhisk on a Kubernetes using Helm.

## Preconditions

+ A database to save the event data is required by Cloudant provider and package. The current implementation is to use the same Cloudant instance that is being used by OpenWhisk itself. Later we will provide another choice to use a different database.
+ persistentvolumes (aka 'pv') is required by this charter. You can verify by `kubectl get pv`.
+ Action containers use Kubernetes DNS. The easiest way to do this is to use the `KubernetesContainerFactory` as the [Invoker Container Factory](https://github.com/apache/incubator-openwhisk-deploy-kube/blob/master/docs/configurationChoices.md#invoker-container-factory) in the Kubernetes cluster by adding below configuration in the `mycluster.yaml` when you deploy OpenWhisk with Helm:
```
# Invoker configurations
invoker:
  containerFactory:
    impl: "kubernetes"
```
If you want to use the `DockerContainerFactory` you can instead attempt to configure the invoker to use Kubernetes DNS. First you can get the IP address of Kubernetes DNS server by `echo $(kubectl get svc kube-dns -n kube-system -o 'jsonpath={.spec.clusterIP}')` and then add below configuration in the `mycluster.yaml`:
```
# Invoker configurations
invoker:
  kube_dns: "<IP_Address_Of_Kube_DNS>"
```

## Install

You may install this chart with command like
```
helm install ./helm/providers/charts/cloudant --namespace=openwhisk --name owdev-cloudant-provider
```

You can use `helm status owdev-cloudant-provider` to check the status. When you see pod is running and job is completed, you can check cloudant package by `wsk package get /whisk.system/cloudant -i --summary`
