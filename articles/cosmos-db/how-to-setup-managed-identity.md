---
title: Configurer des identités managées avec Azure AD pour votre compte Azure Cosmos DB
description: Découvrez comment configurer des identités managées avec Azure Active Directory pour votre compte Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230853"
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

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md)
- En savoir plus sur les [clés gérées par le client sur Azure Cosmos DB](how-to-setup-cmk.md)
