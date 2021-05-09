---
title: Configurer des identités managées avec Azure AD pour votre compte Azure Cosmos DB
description: Découvrez comment configurer des identités managées avec Azure Active Directory pour votre compte Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/23/2021
ms.author: thweiss
ms.openlocfilehash: 3f33cc08fcb9f3c43d9da312ce9ff12d9b20d722
ms.sourcegitcommit: 5f785599310d77a4edcf653d7d3d22466f7e05e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108065292"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Configurer des identités managées avec Azure Active Directory pour votre compte Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Les identités managées pour ressources Azure fournissent automatiquement aux services Azure une identité managée dans Azure Active Directory. Cet article explique comment créer une identité managée pour les comptes Azure Cosmos DB.

> [!NOTE]
> Seules les identités managées affectées par le système sont prises en charge par Azure Cosmos DB.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour les ressources Azure, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md) Pour en savoir plus sur les types d’identités managées, consultez [Types d’identités managées](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Pour configurer des identités managées, votre compte doit avoir le [rôle de contributeur de compte DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système

### <a name="using-an-azure-resource-manager-arm-template"></a>Utilisation d’un modèle Azure Resource Manager (ARM)

> [!IMPORTANT]
> Veillez à utiliser une `apiVersion` de `2021-03-15` ou version ultérieure lorsque vous utilisez des identités managées.

Pour activer une identité affectée par le système sur un compte Azure Cosmos DB nouveau ou existant, indiquez la propriété suivante dans la définition de la ressource :

```json
"identity": {
    "type": "SystemAssigned"
}
```

La section `resources` de votre modèle ARM doit ressembler à ceci :

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Une fois votre compte Azure Cosmos DB créé ou mis à jour, la propriété suivante s’affiche :

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Pour activer une identité affectée par le système lors de la création d’un compte Azure Cosmos DB, ajoutez l’option `--assign-identity` :

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity
```

Vous pouvez également ajouter une identité affectée par le système sur un compte existant à l’aide de la commande `az cosmosdb identity assign` :

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
```

Une fois votre compte Azure Cosmos DB créé ou mis à jour, vous pouvez extraire l’identité affectée avec la commande `az cosmosdb identity show` :

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity show \
    -n $accountName \
    -g $resourceGroupName
```

```json
{
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-identity"></a>Suppression d’une identité affectée par le système

### <a name="using-an-azure-resource-manager-arm-template"></a>Utilisation d’un modèle Azure Resource Manager (ARM)

> [!IMPORTANT]
> Veillez à utiliser une `apiVersion` de `2021-03-15` ou version ultérieure lorsque vous utilisez des identités managées.

Pour supprimer une identité affectée par le système de votre compte Azure Cosmos DB, définissez le `type` de la propriété `identity` sur `None` :

```json
"identity": {
    "type": "None"
}
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Pour supprimer une identité affectée par le système de votre compte Azure Cosmos DB, utilisez la commande `az cosmosdb identity remove` :

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity remove \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md)
- En savoir plus sur les [clés gérées par le client sur Azure Cosmos DB](how-to-setup-cmk.md)
