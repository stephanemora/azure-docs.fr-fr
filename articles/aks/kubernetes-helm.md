---
title: Déployer des conteneurs avec Helm dans Kubernetes sur Azure
description: Utiliser l’outil d’empaquetage Helm pour déployer des conteneurs dans un cluster Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd2deba25615373765dd3492d03c1ba547c8ba8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055132"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Installer des applications avec Helm dans Azure Kubernetes Service (AKS)

[Helm][helm] est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l’instar de gestionnaires de package Linux tels que *APT* et *Yum*, Helm sert à gérer les graphiques Kubernetes, qui sont des packages de ressources Kubernetes préconfigurés.

Cet article vous montre comment configurer et utiliser Helm dans un cluster Kubernetes AKS.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous avez créé un cluster AKS et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin de ces éléments, reportez-vous au [guide de démarrage rapide d’AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Installer l’interface de ligne de commande (CLI) Helm

L’interface CLI Helm est un client qui s’exécute sur votre système de développement et vous permet de démarrer, d’arrêter et de gérer des applications avec Helm.

Si vous utilisez Azure Cloud Shell, l’interface CLI Helm est déjà installée. Pour installer l’interface CLI Helm sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez [Installation de Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Sortie :

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.9.1: 50 files, 66.2MB
```

## <a name="create-a-service-account"></a>Créer un compte de service

Avant de déployer Helm dans un cluster où RBAC est activé, vous avez besoin d’un compte de service et d’une liaison de rôle pour le service Tiller. Pour plus d’informations sur la sécurisation de Helm / Tiller dans un cluster où RBAC est activé, consultez [Tiller, Namespaces, and RBAC][tiller-rbac] (Tiller, espaces de noms et RBAC). Si RBAC n’est pas activé sur votre cluster, passez cette étape.

Créez un fichier nommé `helm-rbac.yaml`, et copiez-y le code YAML suivant :

```yaml
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

Créez le compte de service et la liaison de rôle avec la commande `kubectl create` :

```console
kubectl create -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Sécuriser Tiller et Helm

Le client Helm et le service Tiller s’authentifient et communiquent entre eux à l’aide des protocoles TLS/SSL. Cette méthode d’authentification permet de sécuriser le cluster Kubernetes et les services qui peuvent être déployés. Pour améliorer la sécurité, vous pouvez générer vos propres certificats signés. Chaque utilisateur Helm reçoit son propre certificat client et Tiller est initialisé dans le cluster Kubernetes avec les certificats appliqués. Pour plus d’informations, consultez [Using TLS/SSL between Helm and Tiller][helm-ssl].

Lorsque vous utilisez un cluster Kubernetes où RBAC est activé, vous pouvez contrôler le niveau d’accès au cluster de Tiller. Vous pouvez définir l’espace de noms Kubernetes dans lequel Tiller est déployé, et restreindre les espaces de noms dans lesquels Tiller peut ensuite déployer des ressources. Cette méthode vous permet de créer des instances de Tiller dans différents espaces de noms, de créer des limites de déploiement et de limiter les utilisateurs du client Helm à certains espaces de noms. Pour plus d’informations, consultez [Helm role-based access controls][helm-rbac].

## <a name="configure-helm"></a>Configurer Helm

Pour déployer une version basique de Tiller dans un cluster AKS, utilisez la commande [helm init][helm-init]. Si RBAC n’est pas activé sur votre cluster, supprimez l’argument et la valeur `--service-account`. Si vous avez configuré TLS/SSL pour Tiller et Helm, ignorez cette étape d’initialisation et fournissez le `--tiller-tls-` nécessaire, comme indiqué dans l’exemple suivant.

```console
helm init --service-account tiller
```

Si vous avez configuré TLS/SSL entre Helm et Tiller, fournissez les paramètres et les noms `--tiller-tls-` de vos propres certificats, comme indiqué dans l’exemple suivant :

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Rechercher des graphiques Helm

Les graphiques Helm servent à déployer des applications dans un cluster Kubernetes. Pour rechercher des graphiques Helm précréés, vous devez utiliser la commande [helm search][helm-search] :

```console
helm search
```

La sortie condensée suivante montre certains des graphiques Helm disponibles :

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Pour mettre à jour la liste des graphiques, utilisez la commande [helm repo update][helm-repo-update]. L’exemple suivant montre une mise à jour de référentiel réussie :

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Exécuter des graphiques Helm

Pour installer des graphiques avec Helm, utilisez la commande [helm install][helm-install] et spécifiez le nom du graphique à installer. Pour savoir à quoi cela ressemble, nous allons effectuer un déploiement Wordpress de base à l’aide d’un graphique Helm. Si vous avez configuré TLS/SSL, ajoutez le paramètre `--tls` pour utiliser le certificat client Helm.

```console
helm install stable/wordpress
```

La sortie condensée suivante montre l’état du déploiement des ressources Kubernetes créées par le graphique Helm :

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

Pour accéder au service Wordpress à l’aide d’un navigateur web, attendez une à deux minutes que son adresse *EXTERNAL-IP* soit renseignée.

## <a name="list-helm-releases"></a>Liste des publications de Helm

Pour afficher la liste des publications installées sur votre cluster, utilisez la commande [helm list][helm-list]. L’exemple suivant montre la version de Wordpress déployée à l’étape précédente. Si vous avez configuré TLS/SSL, ajoutez le paramètre `--tls` pour utiliser le certificat client Helm.

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des déploiements d’applications Kubernetes, consultez la documentation Helm.

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
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
