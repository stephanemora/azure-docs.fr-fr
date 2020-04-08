---
title: Configurer votre cluster Azure Dev Spaces pour utiliser Helm 3 (préversion)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Découvrez comment configurer votre cluster Dev Spaces pour utiliser Helm 3.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: dbccb2618fd5a27805261d60e7891d920e0bc372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454293"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Configurer votre cluster Azure Dev Spaces pour utiliser Helm 3 (préversion)

Azure Dev Spaces utilise Helm 2 par défaut pour installer les services utilisateur dans les espaces de développement sur votre cluster AKS. Vous pouvez autoriser Azure Dev Spaces à utiliser Helm 3 au lieu de Helm 2 en installant les services utilisateur dans les espaces de développement. Quelle que soit la version de Helm utilisée par Azure Dev Spaces pour installer les services utilisateur, vous pouvez continuer à utiliser le client Helm 2 ou 3 pour gérer vos propres versions sur le même cluster.

Quand vous activez Helm 3, Azure Dev Spaces se comporte différemment lors de l’installation des services utilisateur dans les espaces de développement :

* Tiller n’est plus déployé sur votre cluster dans l’espace de noms *azds*.
* Helm stocke les informations de version dans l’espace de noms où un service est installé, plutôt que dans l’espace de noms *azds*.
* Les informations de version Helm 3 restent dans l’espace de noms où un service est installé après la suppression d’un contrôleur.
* Vous pouvez interagir directement avec une version managée par Azure Dev Spaces sur votre cluster à l’aide du client Helm 3.

Dans ce guide, vous allez découvrir comment activer Helm 3 pour Azure Dev Spaces afin d’installer des services utilisateur dans des espaces de développement.

> [!IMPORTANT]
> Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Les préversions sont à votre disposition, à condition que vous acceptiez les [conditions d’utilisation supplémentaires](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Certains aspects de cette fonctionnalité sont susceptibles d’être modifiés avant la mise à disposition générale.

## <a name="before-you-begin"></a>Avant de commencer

### <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* [Azure CLI][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Inscrire la fonctionnalité en préversion Helm3Preview

Pour permettre à Azure Dev Spaces d’utiliser Helm 3 pour l’installation des services utilisateur dans des espaces de développement, activez d’abord l’indicateur de fonctionnalité *Helm3Preview* sur votre abonnement à l’aide de la commande *az feature register* :

> [!WARNING]
> Tout cluster AKS sur lequel vous activez Azure Dev Spaces avec l’indicateur de fonctionnalité *Helm3Preview* utilisera cette expérience en préversion. Pour continuer à activer la prise en charge complète d’Azure Dev Spaces sur les clusters AKS, n’activez pas les fonctionnalités en préversion sur les abonnements de production. Utilisez abonnement Azure de test ou de développement pour tester les fonctionnalités en préversion.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

L’inscription peut prendre quelques minutes. Vérifiez l’état de l’inscription à l’aide de la commande *az feature show* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

Quand l’*état* est *Inscrit*, actualisez l’inscription de *Microsoft.DevSpaces* à l’aide de *az provider register* :

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent pendant que cette fonctionnalité est en préversion :

* Vous ne pouvez pas utiliser cette fonctionnalité sur des clusters AKS avec des charges de travail existantes. Vous devez créer un nouveau cluster AKS.

## <a name="create-your-cluster"></a>Création de votre cluster

Créez un cluster AKS dans une région qui dispose de cette fonctionnalité en préversion. Les commandes ci-dessous créent un groupe de ressources nommé *MyResourceGroup* et un cluster AKS nommé *MyAKS* :

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Activer Azure Dev Spaces

Utilisez la commande *use-dev-spaces* pour activer Dev Spaces sur votre cluster AKS et suivez les invites. La commande ci-dessous active Dev Spaces sur le cluster *MyAKS* dans le groupe *MyResourceGroup* et crée un espace de développement par défaut.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Vérifier que Dev Spaces exécute Helm 3

Vérifiez que le tiller n’est pas en cours d’exécution en listant les déploiements dans l’espace de noms *azds* :

```cmd
kubectl get deployment -n azds
```

Confirmez que *tiller-deploy* n’est pas en cours d’exécution dans l’espace de noms azds. Par exemple :

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment Azure Dev Spaces vous aide à développer des applications plus complexes sur plusieurs conteneurs, et comment vous pouvez simplifier le développement collaboratif en utilisant différentes versions ou branches de votre code dans différents espaces.

> [!div class="nextstepaction"]
> [Développement en équipe dans Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md