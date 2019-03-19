---
title: Stocker des informations d’identification dans Azure Key Vault | Microsoft Docs
description: Découvrez comment stocker les informations d’identification des magasins de données utilisées par un coffre de clés Azure, qui peuvent être récupérées automatiquement par Azure Data Factory au moment de l’exécution.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: jingwang
ms.openlocfilehash: a7d440509e2b823400cde83c1ac2ec054c37eb74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896209"
---
# <a name="store-credential-in-azure-key-vault"></a>Stocker des informations d’identification dans Azure Key Vault

Vous pouvez stocker les informations d’identification des magasins de données et calculs dans un coffre de clés [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory récupère les informations d’identification lors de l’exécution d’une activité qui utilise le magasin de données/calcul.

Actuellement, tous les types d’activité, à l’exception des activités personnalisées, prennent en charge cette fonctionnalité. Particulièrement pour la configuration du connecteur, vérifiez la section « Propriétés du service lié » dans [chaque rubrique de connecteur](copy-activity-overview.md#supported-data-stores-and-formats) pour obtenir des informations.

## <a name="prerequisites"></a>Conditions préalables

Cette fonctionnalité repose sur l’identité de géré de fabrique de données. Découvrez comment il fonctionne à partir de [identité managée pour Data factory](data-factory-service-identity.md) et assurez-vous que votre fabrique de données est associée à une.

## <a name="steps"></a>Étapes

Pour référencer des informations d’identification stockées dans Azure Key Vault, vous devez :

1. **Récupérez l’identité de données managé factory** en copiant la valeur « ID identité du SERVICE APPLICATION » générée en même temps que votre fabrique. Si vous utilisez ADF de création de l’interface utilisateur, l’ID d’application identité gérée n’apparaît dans la fenêtre de création du service lié Azure Key Vault ; Vous pouvez également le récupérer à partir du portail Azure, reportez-vous à [identité gérée de fabrique de données de récupération](data-factory-service-identity.md#retrieve-managed-identity).
2. **Accorder l’accès de l’identité gérée dans votre Azure Key Vault.** Dans votre coffre de clés -> accès stratégies -> Ajouter nouveau -> recherche cette gérée identité ID d’application pour accorder **obtenir** autorisation dans la liste déroulante autorisations du Secret. Cela permet à la fabrique désignée d’accéder au secret du coffre de clés.
3. **Créez un service lié pointant vers votre coffre de clés Azure Key Vault.** Reportez-vous à la section [Service lié Azure Key Vault](#azure-key-vault-linked-service).
4. **Créez un service lié de magasin de données, dans lequel référencer le secret correspondant qui est stocké dans le coffre de clés.** Reportez-vous à la section [Référencer le secret stocké dans le coffre de clés](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Service lié Azure Key Vault

Les propriétés suivantes sont prises en charge pour le service lié Azure Key Vault :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type doit être définie sur : **AzureKeyVault**. | Oui |
| baseUrl | Spécifiez l’URL d’Azure Key Vault. | Oui |

**Utilisation de l’interface utilisateur de création :**

Cliquez sur **Connexions** -> **Services liés** -> **+Nouveau** -> recherchez « Azure Key Vault » :

![Rechercher AKV](media/store-credentials-in-key-vault/search-akv.png)

Sélectionnez le coffre Azure Key Vault provisionné où sont stockées vos informations d’identification. Vous pouvez **tester la connexion** pour vous assurer que votre connexion AKV est valide. 

![Configurer AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Exemple JSON :**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Référencer le secret stocké dans le coffre de clés

Les propriétés suivantes sont prises en charge lorsque vous configurez un champ dans le service lié qui référence un secret de coffre de clés :

| Propriété | Description | Obligatoire |
|:--- |:--- |:--- |
| Type | La propriété type du champ doit être définie sur : **AzureKeyVaultSecret**. | Oui |
| secretName | Nom du secret dans le coffre de clés Azure. | Oui |
| secretVersion | Version du secret dans le coffre de clés Azure.<br/>Si elle n’est pas spécifiée, la version la plus récente du secret est utilisée.<br/>Si elle est spécifiée, elle utilise la version spécifiée.| Non  |
| store | Fait référence au service lié Azure Key Vault que vous utilisez pour stocker les informations d’identification. | Oui |

**Utilisation de l’interface utilisateur de création :**

Sélectionnez **Azure Key Vault** pour les champs secrets lors de la création de la connexion à votre magasin de données/compute. Sélectionnez le service lié Azure Key Vault provisionné et fournissez le **nom secret**. Vous pouvez éventuellement fournir également une version de secret. 

>[!TIP]
>Pour les connecteurs à l’aide de la chaîne de connexion dans le service lié comme SQL Server, stockage d’objets Blob, etc., vous pouvez choisir pour stocker uniquement le champ secret par exemple, le mot de passe dans AKV, ou pour stocker la chaîne de connexion complète dans Azure Key VAULT. Vous trouverez ces deux options dans l’interface utilisateur.

![Configurer le secret AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Exemple JSON : (voir la section « password »)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des banques de données prises en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [banques de données prises en charge](copy-activity-overview.md#supported-data-stores-and-formats).
