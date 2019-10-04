---
title: Installer Hashicorp Consul dans Azure Kubernetes Service (AKS)
description: Découvrez comment installer et utiliser Consul pour créer un maillage de services (service mesh) dans un cluster AKS (Azure Kubernetes Service)
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189078"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Installer et utiliser Consul Connect dans AKS (Azure Kubernetes Service)

[Consul][consul-github] est un maillage de services open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la détection des services, la vérification de l’intégrité, la segmentation des services et l’observabilité. Pour plus d’informations sur Consul, consultez la documentation officielle [What is Consul?][consul-docs-concepts].

Cet article vous montre comment installer Consul. Les composants Consul sont installés dans un cluster Kubernetes sur AKS.

> [!NOTE]
> Ces instructions référencent la version `1.5` de Consul.
>
> Les versions Consul `1.5.x` ont été testées par l’équipe Hashicorp par rapport aux versions Kubernetes `1.12`, `1.14` et `1.14`. Vous trouverez d’autres versions de Consul dans la [page GitHub dédiée aux versions de Consul][consul-github-releases] et des informations sur chaque version sur le site [Consul - Release Notes][consul-release-notes].

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Installer les composants Consul sur AKS
> * Valider l’installation de Consul
> * Désinstaller Consul à partir d’AKS

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes version `1.11` ou supérieure, avec RBAC activé) et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin d’aide avec l’un quelconque de ces éléments, consultez le [guide de démarrage rapide d’AKS][aks-quickstart].

Vous avez besoin de [Helm][helm] pour suivre ces instructions et installer Consul. Il est recommandé d’avoir correctement installé et configuré la version `2.12.2` ou ultérieure dans votre cluster. Si vous avez besoin d’aide pour installer Helm, consultez les [consignes d’installation de Helm dans AKS][helm-install]. Tous les pods Consul doivent également être planifiés pour s’exécuter sur des nœuds Linux.

Cet article répartit les instructions d’installation de Consul en plusieurs étapes discrètes. Le résultat final est, par sa structure, identique à celui obtenu avec le [guide][consul-install-k8] officiel d’installation de Consul.

### <a name="install-the-consul-components-on-aks"></a>Installer les composants Consul sur AKS

Nous allons tout d’abord cloner le produit Hashicorp Consul officiel sur le dépôt GitHub Kubernetes.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Nous devons ensuite créer un fichier de valeurs Helm personnalisé pour l’installation de Consul. Créez le fichier de valeurs personnalisé suivant avant d’installer Consul.

```bash
vim consul-values.yaml
```

Ensuite, copiez et collez les valeurs suivantes dans le fichier consul-values.yaml.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Maintenant que nous disposons du fichier de valeurs personnalisé, nous pouvons installer Consul dans notre cluster AKS

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
Le chart Helm `Consul` déploie plusieurs objets. Vous pouvez voir la liste à partir de la sortie de votre commande `helm install` ci-dessus. Le déploiement des composants Consul peut prendre 4 à 5 minutes, selon votre environnement de cluster.

> [!NOTE]
> Tous les pods Consul doivent être planifiés pour s’exécuter sur des nœuds Linux. Si vous avez des pools de nœuds Windows Server en plus des pools de nœuds Linux sur votre cluster, vérifiez que tous les pods Consul ont été planifiés pour s’exécuter sur les nœuds Linux.

À ce stade, vous avez déployé Consul sur votre cluster AKS. Pour nous assurer que nous avons un déploiement réussi de Consul, nous allons passer à la section suivante pour valider l’installation de Consul.

## <a name="validate-the-consul-installation"></a>Valider l’installation de Consul

Tout d’abord, vérifiez que les services attendus ont été créés. Utilisez la commande [kubectl get svc][kubectl-get] pour afficher les services en cours d’exécution. Interrogez l’espace de noms `consul`, où les composants Consul ont été installés par le chart Helm `consul` :

```console
kubectl get svc --namespace consul
```

L’exemple de sortie suivant montre les services qui doivent maintenant être en cours d’exécution :

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Ensuite, vérifiez que les pods requis ont été créés. Utilisez la commande [kubectl get pods][kubectl-get] et réinterrogez l’espace de noms `consul` :

```console
kubectl get pods --namespace consul
```

L’exemple de sortie suivant montre les pods en cours d’exécution :

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Tous les pods doivent indiquer l’état `Running`. Si vos pods n’ont pas cet état, patientez une minute ou deux. Si des pods signalent un problème, utilisez la commande [kubectl describe pod][kubectl-describe] pour examiner leur sortie et leur état.

## <a name="accessing-the-consul-ui"></a>Accès à l’interface utilisateur Consul

L’interface utilisateur Consul a été mise en place dans notre installation ci-dessus qui fournit une configuration basée sur l’interface utilisateur pour Consul. L’interface utilisateur pour Consul n’est pas exposée publiquement via une adresse IP externe. Pour accéder aux interfaces utilisateur des modules complémentaires, utilisez la commande [kubectl port-forward][kubectl-port-forward]. Cette commande crée une connexion sécurisée entre votre machine cliente et le pod approprié dans votre cluster AKS.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Vous pouvez maintenant ouvrir un navigateur et le faire pointer vers `http://localhost:8080/ui` pour ouvrir l’interface utilisateur Consul. Les éléments suivants doivent s’afficher quand vous ouvrez l’interface utilisateur :

![Interface utilisateur Consul](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Désinstaller Consul à partir d’AKS

> [!WARNING]
> La suppression de Consul à partir d’un système en cours d’exécution peut entraîner des problèmes de trafic entre vos services. Vérifiez que vous avez mis en place des dispositions pour que votre système continue de fonctionner correctement sans Consul avant de continuer.

### <a name="remove-consul-components-and-namespace"></a>Supprimer les composants et l’espace de noms Consul

Pour supprimer Consul de votre cluster AKS, utilisez les commandes suivantes. Les commandes `helm delete` suppriment le chart `consul`, et la commande `kubectl delete ns` supprime l’espace de noms `consul`.

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage d’options de configuration et d’installation pour Consul, consultez les articles Consul officiels suivants :

- [Consul - Helm installation guide][consul-install-k8]
- [Consul - Helm installation options][consul-install-helm-options]

Vous pouvez également suivre des scénarios supplémentaires à l’aide de l’[exemple d’application Consul][consul-app-example].

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
