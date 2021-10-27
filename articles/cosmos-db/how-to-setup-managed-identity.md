---
title: Configurer des identités managées avec Azure AD pour votre compte Azure Cosmos DB
description: Découvrez comment configurer des identités managées avec Azure Active Directory pour votre compte Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2021
ms.author: thweiss
ms.openlocfilehash: 826afef5d637278628146af8a35f78232e5b3531
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132918"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Configurer des identités managées avec Azure Active Directory pour votre compte Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Les identités managées pour ressources Azure fournissent automatiquement aux services Azure une identité managée dans Azure Active Directory. Cet article explique comment créer une identité managée pour les comptes Azure Cosmos DB.

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé avec les identités managées pour les ressources Azure, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md) Pour en savoir plus sur les types d’identités managées, consultez [Types d’identités managées](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).
- Pour configurer des identités managées, votre compte doit avoir le [rôle de contributeur de compte DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour activer une identité managée affectée par le système sur un compte Azure Cosmos DB existant, accédez à votre compte dans le portail Azure, puis sélectionnez **Identité** dans le menu de gauche.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="Entrée du menu Identité" border="true":::

Dans la section **Affecté(e) par le système**, définissez **État** sur Activé, puis sélectionnez **Enregistrer**. Vous serez invité à confirmer la création de l’identité gérée affectée par le système.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-system-assigned.png" alt-text="Activation d’une identité affectée par le système" border="true":::

Une fois l’identité créée et affectée, vous pouvez récupérer son ID d’objet (principal).

:::image type="content" source="./media/how-to-setup-managed-identity/system-identity-enabled.png" alt-text="Récupération de l’ID d’objet d’une identité affectée par le système" border="true":::

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

## <a name="add-a-user-assigned-identity"></a>Ajouter une identité attribuée par l’utilisateur

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour activer une identité managée affectée par l’utilisateur sur un compte Azure Cosmos DB existant, accédez à votre compte dans le portail Azure, puis sélectionnez **Identité** dans le menu de gauche.

:::image type="content" source="./media/how-to-setup-managed-identity/identity-tab.png" alt-text="Entrée du menu Identité" border="true":::

Sous la section **Utilisateur affecté** , sélectionnez **+ Ajouter**.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-1.png" alt-text="Activation d’une identité affectée par l’utilisateur" border="true":::

Recherchez et sélectionnez toutes les identités que vous souhaitez affecter à votre compte Azure Cosmos DB, puis sélectionnez **Ajouter**.

:::image type="content" source="./media/how-to-setup-managed-identity/enable-user-assigned-2.png" alt-text="Sélection de toutes les identités à affecter" border="true":::

### <a name="using-an-azure-resource-manager-arm-template"></a>Utilisation d’un modèle Azure Resource Manager (ARM)

> [!IMPORTANT]
> Veillez à utiliser une `apiVersion` de `2021-03-15` ou version ultérieure lorsque vous utilisez des identités managées.

Pour activer une identité affectée par l’utilisateur sur un compte Azure Cosmos DB nouveau ou existant, indiquez la propriété suivante dans la définition de la ressource :

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<identity-resource-id>": {}
    }
}
```

La section `resources` de votre modèle ARM doit ressembler à ceci :

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "<identity-resource-id>": {}
            }
        },
        // ...
    },
    // ...
]
```

Une fois votre compte Azure Cosmos DB créé ou mis à jour, la propriété suivante s’affiche :

```json
"identity": {
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

### <a name="using-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure (CLI)

Pour activer une identité affectée par l’utilisateur lors de la création d’un compte Azure Cosmos DB, ajoutez l’option `--assign-identity` et transmettez l’ID de ressource de l’identité que vous souhaitez affecter :

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --assign-identity <identity-resource-id>
```

Vous pouvez également ajouter une identité affectée par l’utilisateur sur un compte existant à l’aide de la commande `az cosmosdb identity assign` :

```azurecli
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

az cosmosdb identity assign \
    -n $accountName \
    -g $resourceGroupName
    --identities <identity-resource-id>
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
    "type": "UserAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="remove-a-system-assigned-or-user-assigned-identity"></a>Supprimer une identité affectée par le système ou par l’utilisateur

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

Pour supprimer toutes les identités managées de votre compte Azure Cosmos DB, utilisez la commande `az cosmosdb identity remove` :

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
