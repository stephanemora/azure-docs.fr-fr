---
title: Déployer des conteneurs avec Helm dans Kubernetes sur Azure
description: Utilisez l’outil d’empaquetage Helm pour déployer des conteneurs sur un cluster Kubernetes dans AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101545"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Utiliser Helm avec Azure Kubernetes Service (AKS)

[Helm][helm] est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l’instar de gestionnaires de package Linux tels que *APT* et *Yum*, Helm sert à gérer les graphiques Kubernetes, qui sont des packages de ressources Kubernetes préconfigurés.

Ce document explique comment configurer et utiliser Helm dans un cluster Kubernetes sur AKS.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous ayez créé un cluster AKS et que vous ayez établi une connexion kubectl avec le cluster. Si vous avez besoin de ces éléments, reportez-vous au [guide de démarrage rapide d’AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Installer l’interface de ligne de commande (CLI) Helm

L’interface CLI Helm est un client qui s’exécute sur votre système de développement et vous permet de démarrer, d’arrêter et de gérer des applications avec Helm.

Si vous utilisez Azure CloudShell, l’interface CLI Helm est déjà installée. Pour installer l’interface CLI Helm sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez [Installation de Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Sortie :

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="create-service-account"></a>Créer le compte de service

Avant la configuration de Helm dans un cluster où RBAC est activé, vous avez besoin d’un compte de service et d’une liaison de rôle pour le service Tiller. Pour plus d’informations sur la sécurisation de Helm / Tiller dans un cluster où RBAC est activé, consultez [Tiller, Namespaces, and RBAC][tiller-rbac] (Tiller, espaces de noms et RBAC). Remarque : si RBAC n’est pas activé sur votre cluster, passez cette étape.

Créez un fichier nommé `helm-rbac.yaml` et copiez-y le YAML suivant.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Créez le compte de service et la liaison de rôle avec la commande `kubectl create`.

```
kubectl create -f helm-rbac.yaml
```

Lorsque vous utilisez un cluster où RBAC est activé, vous disposez d’options concernant le niveau d’accès au cluster de Tiller. Consultez [Helm: role-based access controls][helm-rbac] (Helm : contrôles d’accès en fonction du rôle) pour plus d’informations sur les options de configuration.

## <a name="configure-helm"></a>Configurer Helm

Installez maintenant tiller à l’aide de la commande [helm init][helm-init]. Si RBAC n’est pas activé sur votre cluster, supprimez l’argument et la valeur `--service-account`.

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Rechercher des graphiques Helm

Les graphiques Helm servent à déployer des applications dans un cluster Kubernetes. Pour rechercher des graphiques Helm créés au préalable, vous devez utiliser la commande [helm search][helm-search].

```azurecli-interactive
helm search
```

La sortie ressemblera à ce qui suit, avec toutefois de nombreux autres graphiques.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Pour mettre à jour la liste des graphiques, utilisez la commande [helm repo update][helm-repo-update].

```azurecli-interactive
helm repo update
```

Sortie :

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Exécuter des graphiques Helm

Pour déployer Wordpress à l’aide d’un graphique Helm, utilisez la commande [helm install][helm-install].

```azurecli-interactive
helm install stable/wordpress
```

La sortie ressemblera à ce qui suit, mais inclura des informations supplémentaires, telles que des instructions sur l’utilisation du déploiement Kubernetes.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Liste des publications de Helm

Pour afficher la liste des publications installées sur votre cluster, utilisez la commande [helm list][helm-list].

```azurecli-interactive
helm list
```

Sortie :

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des graphiques Kubernetes, consultez la documentation de Helm.

> [!div class="nextstepaction"]
> [Documentation de Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
