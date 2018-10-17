---
title: Principaux de service pour Azure Kubernetes Services (AKS)
description: Créer et gérer un principal de service Azure Active Directory pour un cluster dans Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394588"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Principaux de service avec Azure Kubernetes Service (AKS)

Un cluster AKS nécessite un [principal de service Azure Active Directory (AD)][aad-service-principal] pour interagir avec des API Azure. Le principal de service est nécessaire pour la création et la gestion dynamiques d’autres ressources Azure, comme un équilibreur de charge ou un Registre de conteneurs Azure (ACR).

Cet article montre comment créer et utiliser un principal de service pour vos clusters AKS.

## <a name="before-you-begin"></a>Avant de commencer

Pour créer un principal de service Azure AD, vous devez disposer des autorisations suffisantes pour inscrire une application auprès de votre client Azure AD et l’affecter à un rôle dans votre abonnement. Si vous n’avez pas les privilèges nécessaires, vous devriez demander à votre administrateur Azure AD ou administrateur d’abonnement de vous attribuer les privilèges nécessaires, de ou créer au préalable un principal de service que vous pourrez utiliser avec le cluster AKS.

Vous devez également avoir installé et configuré Azure CLI version 2.0.46 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Créer et utiliser un principal de service automatiquement

Lorsque vous créez un cluster AKS dans le portail Azure ou à l’aide de la commande [az aks create][az-aks-create], Azure peut générer automatiquement un principal de service.

Dans l’exemple d’Azure CLI suivant, un principal de service n’est pas spécifié. Dans ce scénario, Azure CLI crée un principal de service pour le cluster AKS. Pour effectuer cette opération, votre compte Azure doit disposer des droits nécessaires pour la création d’un principal de service.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>Créer manuellement un principal de service

Pour créer manuellement un principal de service avec Azure CLI, utilisez la commande [az ad sp create-for-rbac][az-ad-sp-create]. Dans l’exemple suivant, le paramètre `--skip-assignment` empêche toute affectation par défaut supplémentaire :

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Le résultat ressemble à l’exemple qui suit. Prenez note de vos propres valeurs pour `appId` et `password`. Ces valeurs sont utilisées lorsque vous créez un cluster AKS dans la section suivante.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Spécifier un principal de service pour un cluster AKS

Pour utiliser un principal de service existant lorsque vous créez un cluster AKS à l’aide la commande [az aks create][az-aks-create], utilisez les paramètres `--service-principal` et `--client-secret` pour spécifier `appId` et `password` à partir de la sortie de la commande [az ad sp create-for-rbac][az-ad-sp-create] :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Si vous déployez un cluster AKS à l’aide du portail Azure, sur la page *Authentification* de la boîte de dialogue **Créer un cluster Kubernetes**, choisissez **Configurer un principal de service**. Sélectionnez **Utiliser existant** et spécifiez les valeurs suivantes :

- **ID client du principal de service** correspond à votre *appId*
- **Secret client du principal de service** correspond à la valeur *password*

![Image de la navigation vers Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="additional-considerations"></a>Considérations supplémentaires

Lorsque vous travaillez avec des principaux de service AKS et Azure AD, gardez les points suivants à l’esprit.

- Le principal de service pour Kubernetes fait partie de la configuration du cluster. Toutefois, n’utilisez pas l’identité pour déployer le cluster.
- Chaque principal de service est associé à une application Azure AD. Le principal de service pour un cluster Kubernetes peut être associé à tout nom d’application Azure AD valide (par exemple : *https://www.contoso.org/example*). L’URL de l’application ne doit pas être un point de terminaison réel.
- Lorsque vous spécifiez **l’ID client** du principal de service, utilisez la valeur de `appId`.
- Sur les machines virtuelles principales et de nœud du cluster Kubernetes, les informations d’identification du principal de service sont stockées dans le fichier `/etc/kubernetes/azure.json`
- Si vous utilisez la commande [az aks create][az-aks-create] pour générer automatiquement le principal de service, les informations d’identification du principal de service sont écrites dans le fichier `~/.azure/aksServicePrincipal.json` sur la machine utilisée pour exécuter la commande.
- Lors de la suppression d’un cluster AKS qui a été créé par [az aks create][az-aks-create], le principal de service qui a été créé automatiquement ne sera pas supprimé.
    - Pour supprimer le principal du service, récupérer tout d’abord son ID avec la [liste d’applications az ad][az-ad-app-list]. L’exemple suivant recherche le cluster nommé *myAKSCluster* , puis supprime l’ID d’application à l’aide de [delete de az ad app][az-ad-app-delete]. Remplacez ces noms par vos propres valeurs :

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les principaux de service Azure Active Directory, consultez [Objets application et principal de service][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
