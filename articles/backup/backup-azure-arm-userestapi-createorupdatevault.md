---
title: 'Sauvegarde Azure : Créer des coffres Recovery Services à l’aide de l’API REST'
description: gérer les opérations de sauvegarde et de restauration des machines virtuelles Azure avec une API REST
services: backup
author: pvrk
manager: shivamg
keywords: API REST ; sauvegarde des machines virtuelles Azure ; restauration des machines virtuelles Azure ;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646705"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Créer des coffres Recovery Services à l’aide de l’API REST

Les étapes de création d’un coffre Azure Recovery Services à l’aide d’une API REST sont décrites dans la documentation [API REST create vault](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Nous allons utiliser ce document comme référence pour créer un coffre appelé « testVault » dans la région « USA Ouest ».

Pour créer ou mettre à jour un coffre Azure Recovery Services, utilisez l’opération *PUT* suivante.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Créer une demande

Pour créer la demande *PUT*, vous devez impérativement utiliser le paramètre `{subscription-id}`. Si vous avez plusieurs abonnements, consultez [Utilisation de plusieurs abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Vous devez définir les paramètres `{resourceGroupName}` et `{vaultName}` pour vos ressources, ainsi que le paramètre `api-version`. Cet article utilise `api-version=2016-06-01`.

Les en-têtes suivants sont requis :

| En-tête de requête   | Description |
|------------------|-----------------|
| *Content-Type :*  | Requis. Défini sur `application/json`. |
| *Authorization :* | Requis. Défini sur un [jeton d’accès](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valide. |

Pour plus d’informations sur la création de la demande, consultez [Components of a REST API request/response](/rest/api/azure/#components-of-a-rest-api-requestresponse) (Composants d’une demande/réponse de l’API REST).

## <a name="create-the-request-body"></a>Créer le corps de la demande

Les définitions courantes suivantes permettent de générer un corps de demande :

|Nom  |Obligatoire  |Type  |Description  |
|---------|---------|---------|---------|
|etag     |         |   Chaîne      |  eTag facultatif       |
|location     |  true       |Chaîne         |   Emplacement des ressources      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propriétés du coffre       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifie l’identificateur système unique de chaque ressource Azure.     |
|tags     |         | Object        |     Balises de ressource    |

Notez que le nom de l’espace de stockage et le nom du groupe de ressources sont fournis dans l’URI PUT. Le corps de la requête définit l’emplacement.

## <a name="example-request-body"></a>Exemple de corps de demande

Le corps de l’exemple suivant est utilisé pour créer un coffre dans la région « USA Ouest ». Spécifiez l’emplacement. La référence SKU est toujours « Standard ».

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Réponses

Il existe deux réponses de réussite pour l’opération de création ou de mise à jour d’un coffre Recovery Services :

|Nom  |type  |Description  |
|---------|---------|---------|
|200 OK     |   [Coffre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Créé     | [Coffre](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Date de création      |

Pour plus d’informations sur les réponses des API REST, consultez [Process the response message](/rest/api/azure/#process-the-response-message) (Traiter le message de réponse).

### <a name="example-response"></a>Exemple de réponse

Une réponse *201 Créé* condensée à partir de l’exemple de corps de demande précédent affiche un *ID* assigné et l’état *provisioningState* *Succeeded* :

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

[Créez une stratégie de sauvegarde pour sauvegarder une machine virtuelle Azure dans ce coffre](backup-azure-arm-userestapi-createorupdatepolicy.md).

Pour plus d’informations sur l’envoi de travaux à l’aide des API REST Azure, consultez les documents suivants :

- [API REST du fournisseur Azure Recovery Services](/rest/api/recoveryservices/)
- [Bien démarrer avec l’API REST Azure](/rest/api/azure/)
