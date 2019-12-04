---
title: Identité managée dans une tâche ACR
description: Activez une identité managée pour les ressources Azure dans une tâche Azure Container Registry pour permettre à la tâche d’accéder à d’autres ressources Azure, notamment d’autres registres de conteneur privé.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/11/2019
ms.author: danlep
ms.openlocfilehash: c86553d7658e57032393c682628d4b12d6945381
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454738"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Utiliser une identité managée Azure dans ACR Tasks 

Activez une [identité managée pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) dans une [tâche ACR](container-registry-tasks-overview.md) de sorte que la tâche puisse accéder à d’autres ressources Azure sans devoir fournir ou gérer des informations d’identification. Par exemple, utilisez une identité managée pour activer une étape de tâche afin d’extraire (pull) ou d’envoyer (push) des images de conteneur à un autre registre.

Dans cet article, vous allez apprendre à utiliser l’interface de ligne de commande Azure pour activer une identité managée attribuée par l’utilisateur ou par le système sur une tâche ACR. Vous pouvez utiliser le service Azure Cloud Shell ou une installation locale d’Azure CLI. Si vous souhaitez l’utiliser en local, la version 2.0.68 ou ultérieure est requise. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

Pour obtenir des scénarios d’accès aux ressources sécurisées à partir d’une tâche ACR à l’aide d’une identité managée, consultez :

* [Authentification multiregistre](container-registry-tasks-cross-registry-authentication.md)
* [Accéder à des ressources externes avec des secrets stockés dans Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Pourquoi utiliser une identité managée ?

Une identité managée pour ressources Azure fournit aux services Azure sélectionnés une identité automatiquement managée dans Azure Active Directory (Azure AD). Vous pouvez configurer une tâche ACR avec une identité managée afin que la tâche puisse accéder à d’autres ressources Azure sécurisées, sans fournir d’informations d’identification dans les étapes de la tâche.

Les identités managées sont de deux types :

* Les *identités attribuées par l’utilisateur*, que vous pouvez attribuer à plusieurs ressources et conserver aussi longtemps que vous le souhaitez. Les identités attribuées par l’utilisateur sont actuellement en préversion.

* Une *identité attribuée par le système*, propre à une ressource spécifique, telle qu’une tâche ACR, qui possède la même durée de vie que cette ressource.

Vous pouvez activer l’un ou les deux types d’identités dans une tâche ACR. Accordez l’accès à une autre ressource, comme n’importe quel principal de sécurité. Lorsque la tâche s’exécute, elle utilise l’identité pour accéder à la ressource dans toutes les étapes de la tâche qui nécessitent un accès.

## <a name="steps-to-use-a-managed-identity"></a>Étapes d’utilisation d’une identité managée

Suivez ces étapes de haut niveau pour utiliser une identité managée avec une tâche ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Facultatif) Créer une identité attribuée par l’utilisateur

Si vous envisagez d’utiliser une identité attribuée par l’utilisateur, vous pouvez utiliser une identité existante. Vous pouvez également créer l’identité à l’aide de l’interface de ligne de commande Azure ou d’autres outils Azure. Par exemple, utilisez la commande [az identity create][az-identity-create]. 

Si vous envisagez d’utiliser une identité attribuée par le système uniquement, ignorez cette étape. Vous pouvez créer une identité attribuée par le système lors de la création de la tâche ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Activer l’identité sur une tâche ACR

Lorsque vous créez une tâche ACR, vous pouvez éventuellement activer une identité attribuée par l’utilisateur, une identité attribuée par le système, ou les deux. Par exemple, transmettez le paramètre `--assign-identity` lorsque vous exécutez la commande [az acr task create][az-acr-task-create] dans l’interface de ligne de commande Azure.

Pour activer une identité attribuée par le système, transmettez `--assign-identity` sans valeur ou `assign-identity [system]`. La commande suivante crée une tâche Linux à partir d’un référentiel GitHub public qui génère l’image `hello-world` avec un déclencheur de validation Git et avec une identité managée attribuée par le système :

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity
```

Pour activer une identité attribuée par l’utilisateur, transmettez `--assign-identity` avec une valeur d’*ID de la ressource* de l’identité. La commande suivante crée une tâche Linux à partir d’un référentiel GitHub public qui génère l’image `hello-world` avec un déclencheur de validation Git et avec une identité managée attribuée par l’utilisateur :

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --assign-identity <resourceID>
```

Vous pouvez récupérer l’ID de la ressource de l’identité en exécutant la commande [az identity show][az-identity-show]. L’ID de la ressource pour l’ID *myUserAssignedIdentity* dans le groupe de ressources *myResourceGroup* se présente sous cette forme. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Accorder des autorisations d’identité pour accéder à d’autres ressources Azure

En fonction des exigences de votre tâche, accordez les autorisations d’identité d’accès à d’autres ressources Azure. Voici quelques exemples :

* Attribuez à l’identité managée un rôle doté des autorisations pull (tirage), push/pull (tirage/envoi) ou d’autres autorisations sur un registre de conteneurs cible dans Azure. Pour connaître la liste complète des rôles de registre, consultez [Rôles et autorisations Azure Container Registry](container-registry-roles.md). 
* Attribuez à l’identité managée un rôle pour lire les secrets dans un coffre de clés Azure.

Utilisez l’[interface de ligne de commande Azure](../role-based-access-control/role-assignments-cli.md) ou d’autres outils Azure pour gérer l’accès en fonction du rôle aux ressources. Par exemple, exécutez la commande [az role assignment create][az-role-assignment-create] pour attribuer à l’identité un rôle. 

L’exemple suivant attribue à une identité managée les autorisations de tirage (pull) à partir d’un registre de conteneurs. La commande spécifie l’*ID du principal du service* de l’identité et l’*ID de la ressource* du registre cible.


```azurecli
az role assignment create --assignee <servicePrincipalID> --scope <registryID> --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Facultatif) Ajouter des informations d’identification à la tâche

Si votre tâche extrait ou envoie des images vers un autre registre de conteneurs Azure, ajoutez des informations d’identification à la tâche pour l’identité à authentifier. Exécutez la commande [az acr task credential add][az-acr-task-credential-add] et transmettez le paramètre `--use-identity` pour ajouter les informations d’identification de l’identité à la tâche. 

Par exemple, pour ajouter des informations d’identification pour une identité attribuée par le système à authentifier auprès du registre *targetregistry*, transmettez `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Pour ajouter des informations d’identification pour une identité attribuée par l’utilisateur à authentifier auprès du registre *targetregistry*, transmettez `use-identity` avec une valeur de l’*ID client* de l’identité. Par exemple :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Vous pouvez récupérer l’ID client de l’identité en exécutant la commande [az identity show][az-identity-show]. L’ID client est un GUID sous la forme `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à activer et utiliser une identité managée attribuée par l’utilisateur ou par le système sur une tâche ACR. Pour obtenir des scénarios d’accès aux ressources sécurisées à partir d’une tâche ACR à l’aide d’une identité managée, consultez :

* [Authentification multiregistre](container-registry-tasks-cross-registry-authentication.md)
* [Accéder à des ressources externes avec des secrets stockés dans Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
