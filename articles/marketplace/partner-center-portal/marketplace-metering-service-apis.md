---
title: API de service de mesure de la Place de marché | Place de marché Azure
description: Événement d’utilisation pour les offres SaaS dans la place de marché Azure.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: dea950ff72eff2372fc10f989d4ce77fa746c4bf
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933586"
---
# <a name="marketplace-metering-service-apis"></a>API de service de mesure de la Place de marché

L’API d’événement d’utilisation vous permet d’émettre des événements d’utilisation pour une entité achetée en particulier. La requête d’événement d’utilisation fait référence à la dimension des services de contrôle définie par le serveur de publication lors de la publication de l’offre.

## <a name="usage-event"></a>Événement d’utilisation

**POST**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Paramètres de requête :*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | Version de l’opération à utiliser pour cette requête. La version la plus récente de l’API est « 2018-08-31 ». |

*En-têtes de requête :*

| Content-Type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `x-ms-correlationid` | Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `authorization`   | [Obtenir le jeton du porteur web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Remarque : Lors de la création de la requête HTTP, ajoutez le préfixe `Bearer` au jeton obtenu à partir du lien référencé. |

*Requête :*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Réponses

Code : 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Code : 400 <br>
Requête incorrecte, données manquantes ou non valides fournies ou expirées

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Code : 403<br>
Requête incorrecte, données manquantes ou non valides fournies ou expirées

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Code : 409<br>
Conflit, lorsque nous recevons l’appel d’utilisation pour l’ID de ressource d’utilisation et l’utilisation effective qui existe déjà. La réponse contient un champ `additionalInfo` qui contient des informations sur le message accepté.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Événement d’utilisation par lot

L’API d’événement d’utilisation par lot vous permet d’émettre des événements d’utilisation pour plusieurs entités achetées en même temps. La requête d’événement d’utilisation par lot fait référence à la dimension des services de contrôle définie par le serveur de publication lors de la publication de l’offre.

>[!Note]
>Vous pouvez inscrire plusieurs offres SaaS dans la Place de marché commerciale de Microsoft. Chaque offre SaaS inscrite a une application Azure AD unique qui est inscrite pour authentification et autorisation. Les événements émis par lot doivent appartenir à des offres avec la même application Azure AD qu’au moment de l’inscription de l’offre.

**POST :** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Paramètres de requête :*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | Version de l’opération à utiliser pour cette requête. La version la plus récente de l’API est « 2018-08-31 ». |

*En-têtes de requête :*

| Content-Type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valeur de chaîne unique pour le suivi de la requête du client, de préférence un GUID. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `x-ms-correlationid` | Valeur de chaîne unique pour l’opération sur le client. Ce paramètre sert à corréler tous les événements de l’opération client avec les événements côté serveur. Si cette valeur n’est pas fournie, une valeur sera générée et fournie dans les en-têtes de réponse. |
| `authorization`      | [Obtenir le jeton du porteur web JSON (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Remarque : Lors de la création de la requête HTTP, ajoutez le préfixe `Bearer` au jeton obtenu à partir du lien référencé.  |

*Requête :*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Réponses

Code : 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Description du code d’état référencé dans la réponse de l’API `BatchUsageEvent` :

| Code d’état  | Description |
| ---------- | -------------------- |
| `Accepted` | Code accepté. |
| `Expired` | Utilisation expirée. |
| `Duplicate` | Utilisation dupliquée fournie. |
| `Error` | Code d’erreur. |
| `ResourceNotFound` | La ressource d’utilisation fournie n’est pas valide. |
| `ResourceNotAuthorized` | Vous n’êtes pas autorisé à fournir l’utilisation pour cette ressource. |
| `InvalidDimension` | La dimension pour laquelle l’utilisation est transmise n’est pas valide pour cette offre/ce plan. |
| `InvalidQuantity` | La quantité passée est <0. |
| `BadArgument` | L’entrée est manquante ou incorrecte. |

Code : 400<br>
Requête incorrecte, données manquantes ou non valides fournies ou expirées

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Code : 403<br>
L’utilisateur n’est pas autorisé à effectuer cet appel

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez [Facturation à l’usage SaaS](./saas-metered-billing.md).
