---
title: Installer des applications existantes avec Helm dans AKS
description: Apprendre à utiliser l’outil d’empaquetage Helm pour déployer des conteneurs dans un cluster AKS (Azure Kubernetes Service)
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: 6ee99eee02e874208106d39c6442f54f59f95dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361606"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installer des applications existantes avec Helm dans Azure Kubernetes service (AKS)

[Helm][helm] est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l’instar de gestionnaires de package Linux tels que *APT* et *Yum*, Helm sert à gérer les graphiques Kubernetes, qui sont des packages de ressources Kubernetes préconfigurés.

Cet article vous montre comment configurer et utiliser Helm dans un cluster Kubernetes AKS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli]ou avec le [Portail Azure][aks-quickstart-portal].

Vous devez aussi avoir installé l’interface CLI Helm, qui est le client s’exécutant sur votre système de développement. Il vous permet de démarrer, arrêter et gérer les applications avec Helm. Si vous utilisez Azure Cloud Shell, l’interface CLI Helm est déjà installée. Pour obtenir les instructions d’installation sur votre plateforme locale, consultez [Installing Helm][helm-install] (Installation de Helm).

> [!IMPORTANT]
> Helm est prévu pour s’exécuter sur des nœuds Linux. Si vous avez des nœuds Windows Server dans votre cluster, vous devez veiller à ce que l’exécution des pods Helm soit planifiée uniquement sur des nœuds Linux. Vous devez aussi veiller à ce que les graphiques Helm que vous installez s’exécutent sur les bons nœuds. Les commandes figurant dans cet article utilisent [node-selectors][k8s-node-selector] pour garantir que les pods sont planifiés sur les nœuds adéquats, mais il se peut que certains graphiques Helm n’exposent pas de sélecteur de nœud. Vous pouvez aussi envisager d’utiliser d’autres options sur votre cluster, comme des [teintes][taints] (« taints »).

## <a name="verify-your-version-of-helm"></a>Vérifier la version de Helm

Utilisez la commande `helm version` pour vérifier que Helm 3 est installé :

```console
helm version
```

L’exemple suivant montre la version 3.0.0 de Helm installée :

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>Installer une application avec Helm v3

### <a name="add-the-official-helm-stable-charts-repository"></a>Ajouter le référentiel Helm officiel de charts stables

Utilisez la commande [helm repo][helm-repo-add] pour ajouter le référentiel Helm officiel de charts stables.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Rechercher des graphiques Helm

Les graphiques Helm servent à déployer des applications dans un cluster Kubernetes. Pour rechercher des graphiques Helm précréés, vous devez utiliser la commande [helm search][helm-search] :

```console
helm search repo stable
```

La sortie condensée suivante montre certains des graphiques Helm disponibles :

```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

Pour mettre à jour la liste des graphiques, utilisez la commande [helm repo update][helm-repo-update].

```console
helm repo update
```

L’exemple suivant montre une mise à jour de référentiel réussie :

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Exécuter des graphiques Helm

Pour installer des charts avec Helm, utilisez la commande [helm install][helm-install-command] et spécifiez un nom de version et le nom du chart à installer. Pour vous montrer comment se déroule l’installation d’un graphique Helm, nous allons effectuer un déploiement nginx de base à l’aide d’un graphique Helm.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

La sortie condensée suivante montre l’état du déploiement des ressources Kubernetes créées par le graphique Helm :

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

Utilisez la commande `kubectl get services` pour accéder à l’*EXTERNAL-IP* de votre service.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-controller
```

Par exemple, la commande ci-dessous affiche l’*EXTERNAL-IP* pour le service *my-nginx-ingress-controller* :

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Répertorier les versions

Pour afficher la liste des versions installées sur votre cluster, utilisez la commande `helm list`.

```console
helm list
```

L’exemple suivant montre la version *my-nginx-ingress* déployée à l’étape précédente :

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, utilisez la commande [helm uninstall][helm-cleanup] et spécifiez le nom de votre version, tel que fourni par la commande `helm list` précédente.

```console
helm uninstall my-nginx-ingress
```

L’exemple suivant illustre la version nommée *my-nginx-ingress* qui a été désinstallé :

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des déploiements d’applications Kubernetes, consultez la documentation Helm.

> [!div class="nextstepaction"]
> [Documentation de Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
