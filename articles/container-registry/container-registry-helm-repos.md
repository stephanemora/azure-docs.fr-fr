---
title: Stocker des charts Helm
description: Découvrez comment utiliser un référentiel Helm avec Azure Container Registry pour stocker des graphiques pour vos applications
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 0c5e66d5f2fc3dd3c2d8c0a975c3e9d1c813732d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456342"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Utiliser Azure Container Registry comme référentiel Helm pour les graphiques de votre application

Pour gérer et déployer rapidement des applications pour Kubernetes, vous pouvez utiliser le [Gestionnaire de package Helm open source][helm]. Avec Helm, les applications sont définies en tant que *graphiques* qui sont stockés dans un référentiel de graphiques Helm. Ces graphiques définissent des configurations et des dépendances, et leurs versions peuvent être gérées tout au long du cycle de vie de l’application. Azure Container Registry peut être utilisé comme hôte pour les référentiels de graphiques Helm.

Avec Azure Container Registry, vous disposez d’un référentiel de graphiques Helm privé et sécurisé, qui peut s’intégrer aux pipelines de build ou d’autres services Azure. Les référentiels de graphiques Helm dans Azure Container Registry incluent des fonctionnalités de géoréplication pour maintenir vos graphiques proches des déploiements et pour assurer la redondance. Vous payez uniquement pour le stockage utilisé par les graphiques, et celui-ci est disponible à tous les niveaux de prix Azure Container Registry.

Cet article vous montre comment utiliser un référentiel de graphiques Helm stocké dans Azure Container Registry.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires][terms-of-use]. Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes indiquées dans cet article, les prérequis suivants doivent être respectés :

- **Azure Container Registry** : créez un registre de conteneurs dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md).
- **Client Helm version 2.11.0 (pas une version RC) ou ultérieure** : exécutez `helm version` pour trouver votre version actuelle. Vous avez également besoin d’un serveur Helm (Tiller) initialisé dans un cluster Kubernetes. Si nécessaire, vous pouvez [créer un cluster Azure Kubernetes Service][aks-quickstart]. Pour plus d’informations sur l’installation et la mise à niveau de Helm, consultez [Installation de Helm][helm-install].
- **Azure CLI version 2.0.46 ou ultérieure** : exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Ajouter un référentiel au client Helm

Un référentiel Helm est un serveur HTTP pouvant stocker des graphiques Helm. Azure Container Registry peut fournir ce stockage pour les graphiques Helm, et gérer la définition d’index quand vous ajoutez et supprimez des graphiques dans le référentiel.

Pour ajouter Azure Container Registry comme référentiel de graphiques Helm, vous utilisez l’interface de ligne de commande Azure. Avec cette approche, votre client Helm est mis à jour avec l’URI et les informations d’identification du référentiel pris en charge par Azure Container Registry. Comme vous n’avez pas besoin de spécifier manuellement les détails de ce référentiel, les informations d’identification ne sont pas exposées dans l’historique des commandes, par exemple.

Si nécessaire, connectez-vous à l’interface de ligne de commande Azure et suivez les invites :

```azurecli
az login
```

Configurez les valeurs par défaut de l’interface de ligne de commande Azure avec le nom de votre registre Azure Container Registry à l’aide de la commande [az configure][az-configure]. Dans l’exemple suivant, remplacez `<acrName>` par le nom de votre registre :

```azurecli
az configure --defaults acr=<acrName>
```

Ajoutez maintenant votre référentiel de graphiques Helm Azure Container Registry à votre client Helm à l’aide de la commande [az acr helm repo add][az-acr-helm-repo-add]. Cette commande obtient un jeton d’authentification pour votre registre Azure Container Registry qui est utilisé par le client Helm. Le jeton d’authentification est valide pendant 1 heure. Comme pour `docker login`, vous pouvez exécuter cette commande dans les futures sessions CLI pour authentifier votre client Helm auprès de votre référentiel de graphiques Helm Azure Container Registry :

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Ajouter un graphique au référentiel

Pour cet article, procurons-nous un graphique Helm existant dans le référentiel *stable* Helm public. Le référentiel *stable* est un référentiel organisé et public qui inclut des graphiques d’application courants. Les chargés de maintenance des packages peuvent envoyer leurs graphiques au référentiel *stable*, de la même façon que Docker Hub fournit un registre public pour les images de conteneur courantes. Le graphique téléchargé à partir du référentiel *stable* public peut ensuite être envoyé à votre référentiel Azure Container Registry privé. Dans la plupart des scénarios, vous devez générer et charger vos propres graphiques pour les applications que vous développez. Pour plus d’informations sur la génération de vos propres graphiques Helm, consultez [Développement de graphiques Helm][develop-helm-charts].

Tout d’abord, créez un répertoire sur *~/acr-helm*, puis téléchargez le graphique *stable/wordpress* existant :

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Répertoriez le graphique téléchargé et notez la version de Wordpress incluse dans le nom de fichier. Comme la commande `helm fetch stable/wordpress` n’a pas spécifié de version particulière, la *dernière* version a été extraite. Tous les graphiques Helm incluent un numéro de version dans le nom de fichier qui suit la norme [SemVer 2][semver2]. Dans l’exemple de sortie suivant, la version du graphique Wordpress est *2.1.10* :

```
$ ls

wordpress-2.1.10.tgz
```

Envoyez maintenant le graphique à votre référentiel de graphiques Helm dans Azure Container Registry à l’aide de la commande [az acr helm push][az-acr-helm-push] de l’interface de ligne de commande Azure. Spécifiez le nom de votre graphique Helm téléchargé à l’étape précédente, par exemple *wordpress-2.1.10.tgz* :

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Après quelques instants, l’interface de ligne de commande Azure signale que votre graphique a été enregistré, comme indiqué dans l’exemple de sortie suivant :

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Répertorier les graphiques dans le référentiel

Le client Helm conserve une copie locale mise en cache du contenu des référentiels distants. Les modifications apportées à un référentiel distant ne mettent pas automatiquement à jour la liste des graphiques disponibles connus localement par le client Helm. Quand vous recherchez des graphiques dans des référentiels, Helm utilise son index local mis en cache. Pour utiliser le graphique chargé à l’étape précédente, vous devez mettre à jour l’index de référentiel Helm local. Vous pouvez réindexer les référentiels dans le client Helm, ou utiliser l’interface de ligne de commande Azure pour mettre à jour l’index de référentiel. Chaque fois que vous ajoutez un graphique à votre référentiel, cette étape doit être effectuée :

```azurecli
az acr helm repo add
```

Avec un graphique stocké dans votre référentiel et l’index mis à jour disponible localement, vous pouvez utiliser les commandes du client Helm standard pour la recherche ou l’installation. Pour afficher tous les graphiques dans votre référentiel, utilisez `helm search <acrName>`. Fournissez votre propre nom Azure Container Registry :

```console
helm search <acrName>
```

Le graphique Wordpress envoyé à l’étape précédente est répertorié, comme illustré dans l’exemple de sortie suivant :

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Vous pouvez également répertorier les graphiques avec l’interface de ligne de commande Azure, en utilisant [az acr helm list][az-acr-helm-list] :

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Afficher des informations pour un graphique Helm

Pour afficher des informations pour un graphique spécifique dans le référentiel, vous pouvez à nouveau utiliser le client Helm standard. Pour afficher des informations pour le graphique nommé *wordpress*, utilisez `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Quand aucun numéro de version n’est fourni, la *dernière* version est utilisée. Helm retourne des informations détaillées sur votre graphique, comme indiqué dans l’exemple de sortie condensé suivant :

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Vous pouvez également afficher les informations pour un graphique à l’aide de la commande [az acr helm show][az-acr-helm-show] de l’interface de ligne de commande Azure. Là encore, la *dernière* version d’un graphique est retournée par défaut. Vous pouvez ajouter `--version` pour répertorier une version spécifique d’un graphique, telle que *2.1.10* :

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Installer un graphique Helm à partir du référentiel

Le graphique Helm dans votre référentiel est installé en spécifiant le nom du référentiel, puis le nom du graphique. Utilisez le client Helm pour installer le graphique Wordpress :

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Si vous procédez à l’envoi à votre référentiel de graphiques Helm Azure Container Registry et retournez ultérieurement dans une nouvelle session CLI, votre client Helm local a besoin d’un jeton d’authentification mis à jour. Pour obtenir un nouveau jeton d’authentification, utilisez la commande [az acr helm repo add][az-acr-helm-repo-add].

Les étapes suivantes sont effectuées pendant le processus d’installation :

- Le client Helm recherche dans l’index de référentiel local.
- Le graphique correspondant est téléchargé à partir du référentiel Azure Container Registry.
- Le graphique est déployé à l’aide de Tiller dans votre cluster Kubernetes.

L’exemple de sortie condensé suivant montre les ressources Kubernetes déployées via le graphique Helm :

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Supprimer un graphique Helm du référentiel

Pour supprimer un graphique du référentiel, utilisez la commande [az acr helm delete][az-acr-helm-delete]. Spécifiez le nom du graphique, tel que *wordpress* et la version à supprimer, telle que *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Si vous souhaitez supprimer toutes les versions du graphique nommé, omettez le paramètre `--version`.

Le graphique continue à être retourné dans `helm search <acrName>`. Là encore, le client Helm ne met pas automatiquement à jour la liste des graphiques disponibles dans un référentiel. Pour mettre à jour l’index de référentiel du client Helm, utilisez à nouveau la commande [az acr helm repo add][az-acr-helm-repo-add] :

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a utilisé un graphique Helm existant du référentiel *stable* public. Pour plus d’informations sur la création et le déploiement de graphiques Helm, consultez [Développement de graphiques Helm][develop-helm-charts].

Les graphiques Helm peuvent être utilisés dans le cadre du processus de génération de conteneur. Pour plus d’informations, consultez [Utiliser des tâches Azure Container Registry][acr-tasks].

Pour plus d’informations sur l’utilisation et la gestion d’Azure Container Registry, consultez les [bonnes pratiques][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/topics/charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
