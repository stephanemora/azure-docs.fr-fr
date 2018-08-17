---
title: Fichier Include
description: Fichier Include
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/03/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 2174ae44f8e78763c1939aee5e6b86c95a0924ce
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513903"
---
## <a name="create-a-service-principal"></a>Créer un principal du service

Pour créer un principal de service avec accès à votre registre de conteneurs, exécutez le script suivant dans [Azure Cloud Shell](../articles/cloud-shell/overview.md) ou dans une installation locale de l’interface de ligne de commande [Azure CLI](/cli/azure/install-azure-cli). Le script est mis en forme pour l’interpréteur de commandes Bash.

Avant d’exécuter le script, mettez à jour la variable `ACR_NAME` avec le nom de votre registre de conteneurs. La valeur `SERVICE_PRINCIPAL_NAME` doit être unique au sein de votre locataire Azure Active Directory. Si vous recevez une erreur « `'http://acr-service-principal' already exists.` », spécifiez un autre nom pour le principal du service.

Vous pouvez éventuellement modifier la valeur `--role` dans la commande [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] si vous souhaitez accorder des autorisations différentes.

Après avoir exécuté le script, notez l’**ID** et le **mot de passe** du principal de service. Une fois que vous avez noté les informations d’identification, vous pouvez configurer vos applications et services afin qu’ils s’authentifient auprès de votre registre de conteneurs en tant que principal du service.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Utiliser un principal de service existant

Pour accorder l’accès au registre à un principal de service existant, vous devez assigner un nouveau rôle au principal de service. Comme lorsque vous créez un principal de service, vous pouvez accorder un accès en extraction, en envoi et en extraction, ou un accès propriétaire.

Le script suivant utilise la commande [az role assignment create][az-role-assignment-create] pour accorder des autorisations en *extraction* à un principal de service que vous spécifiez dans la variable `SERVICE_PRINCIPAL_ID`. Ajustez la valeur `--role` si vous souhaitez accorder un niveau d’accès différent.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
