---
title: Utiliser une identité managée attribuée par le système d’une application App Service pour accéder à Azure Key Vault
description: Découvrez comment créer une identité managée pour les applications App Service et comment l’utiliser pour accéder à Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8ac6f9be80d31804089ae2589998079dc7df66b3
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004178"
---
# <a name="use-an-app-service-managed-identity-to-access-azure-key-vault"></a>Utiliser une identité managée App Service pour accéder à Azure Key Vault 

Cet article montre comment créer une identité managée pour les applications App Service et comment l’utiliser pour accéder à Azure Key Vault. Pour les applications hébergées sur des machines virtuelles Azure, consultez [Utiliser une identité managée de machine virtuelle Windows attribuée par le système pour accéder à Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md). 

Une identité managée issue d’Azure Active Directory permet à votre application d’accéder facilement aux autres ressources protégées par Azure AD. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Pour plus d’informations sur les identités managées dans Azure AD, consultez [Identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis 

Pour suivre ce guide, vous avez besoin des ressources suivantes. 

- Un coffre de clés. Vous pouvez utiliser un coffre de clés existant ou en créer un nouveau en suivant les étapes d’un des guides de démarrage rapide suivants :
   - [Créer un coffre de clés avec Azure CLI](quick-create-cli.md)
   - [Créer un coffre de clés avec Azure PowerShell](quick-create-powershell.md)
   - [Créer un coffre de clés avec le portail Azure](quick-create-portal.md).
- Une application App Service existante à laquelle accorder l’accès au coffre de clés. Vous pouvez en créer une rapidement en suivant les étapes décrites dans la [documentation App Service](../app-service/overview.md)/


## <a name="adding-a-system-assigned-identity"></a>Ajout d’une identité attribuée par le système 

Tout d’abord, vous devez ajouter une identité affectée par le système à une application. 
 
### <a name="azure-portal"></a>Portail Azure 

Pour configurer une identité managée dans le portail, vous créez une application selon la procédure habituelle, puis vous activez la fonctionnalité. 

1. Si vous utilisez une application de fonction, accédez à **Fonctionnalités de la plateforme**. Pour les autres types d’application, faites défiler jusqu’au groupe **Paramètres** dans le volet de navigation de gauche. 

1. Sélectionnez **Identité managée**. 

1. Dans l’onglet **Attribuée par le système**, définissez **État** sur **Activé**. Cliquez sur **Enregistrer**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>D’Azure CLI

Ce guide de démarrage rapide requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour rechercher votre version actuelle. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Pour vous connecter avec Azure CLI, utilisez la commande [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Pour plus d’informations sur les options de connexion avec Azure CLI, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Pour créer l’identité pour cette application, utilisez la commande Azure CLI [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) ou la commande [az functionapp identity assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) :


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Prenez note de `PrincipalId`, qui sera nécessaire dans la section suivante.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Autoriser votre application à accéder à Key Vault 

### <a name="azure-portal"></a>Portail Azure

1.  Accédez à une ressource Key Vault. 

1.  Sélectionnez **Stratégies d’accès** et cliquez sur **Ajouter une stratégie d’accès**. 

1.  Dans **Autorisations du secret**, sélectionnez **Get, List**. 

1.  Choisissez **Sélectionner le principal** et, dans la zone de recherche, entrez le nom de l’application.  Sélectionnez l’application dans la liste des résultats et cliquez sur **Sélectionner**. 

1.  Cliquez sur **Ajouter** pour terminer l’ajout de la nouvelle stratégie d’accès.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>D’Azure CLI

Pour autoriser votre application à accéder à votre coffre de clés, utilisez la commande Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy), en fournissant le paramètre **ObjectID** avec le **principalId* noté ci-dessus.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Étapes suivantes

- Lire la [vue d’ensemble Azure Key Vault](key-vault-overview.md)
- Consulter le [Guide du développeur Azure Key Vault](key-vault-developers-guide.md)
- Découvrir les [clés, secrets et certificats](about-keys-secrets-and-certificates.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](key-vault-best-practices.md)
