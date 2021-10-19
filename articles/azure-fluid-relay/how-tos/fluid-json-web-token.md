---
title: Contrat de jeton Relais Azure Fluid
description: Mieux comprendre le jeton Web JSON utilisé dans Relais Azure Fluid
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: 3fe063a6d5b3c76d300a088372e9dee08d9d79c0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661880"
---
# <a name="azure-fluid-relay-token-contract"></a>Contrat de jeton Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Les requêtes envoyées au Relais Azure Fluid doivent contenir un jeton JWT dans l’en-tête d'autorisation. Ce jeton doit être [signé par la clé de locataire](../concepts/authentication-authorization.md).

## <a name="claims"></a>Revendications

Les jetons JWT (jetons Web JSON) comprennent trois parties : 

- **En-tête** : fournit des informations sur la façon de valider le jeton, notamment des informations sur le type du jeton et la façon dont il a été signé. 
- **Charge utile** : contient toutes les données importantes concernant l’utilisateur ou l’application qui tente d’appeler votre service. 
- **Signature** : ressource de base utilisée pour valider le jeton. 

Chaque partie est séparée par un point (.) et encodée séparément en base64. 

## <a name="header-claims"></a>Revendications de l’en-tête

| Revendication | Format | Description  |
|-------|--------|--------------|
| alg   | string | L’algorithme utilisé pour signer le jeton. Par exemple, « HS256 » |
| Type   | string | Cette valeur doit toujours être « JWT ». |

## <a name="payload-claims"></a>Revendications de la charge utile

| Revendication      | Format                   | Description |
|------------|--------------------------|-------------|
| documentId | string                   | Généré par FRS, identifie le document pour lequel le jeton est généré. |
| scope      | string[]                 | Identifie les autorisations requises par le client sur le document ou le résumé. Pour chaque étendue, vous pouvez définir les autorisations que vous souhaitez donner au client.  |
| tenantId   | string                   | Identifie le locataire. |
| utilisateur       | JSON                     | *Facultatif* `{ displayName: <display_name>, id: <user_id>, name: <user_name>, }` Identifie les utilisateurs de votre application. Il est renvoyé à votre application par Alfred, le service de tri.  Il peut être utilisé par votre application pour identifier les utilisateurs de la réponse renvoyée par Alfred. Relais Azure Fluid ne valide pas cette information. |
| iat        | nombre, un horodatage UNIX | « Issued At » (Délivré le) indique quand l'authentification de ce jeton a eu lieu. |
| exp        | nombre, un horodatage UNIX | La revendication « exp » (délai d’expiration) indique le délai d’expiration à partir duquel le JWT ne doit pas être accepté pour être traité. La durée de vie du jeton ne doit pas dépasser 1 heure. |
| ver        | string                   | Indique la version du jeton d’accès. Doit être `1.0`. |
| jti        | string                   | *Facultatif.*  La revendication « JTI » (ID JWT) fournit un identificateur unique pour le jeton JWT. La valeur de l’identificateur DOIT être attribuée de manière à garantir qu’il y a une probabilité négligeable que la même valeur soit affectée par accident à un objet de données différent. Nous vous encourageons à utiliser cette revendication pour éviter les échecs dus à l’utilisation du même jeton pour la création de documents.  |

## <a name="a-sample-azure-fluid-relay-token"></a>Un exemple de contrat de jeton Relais Azure Fluid

```typescript
{ 
  "alg": "HS256",  
  "typ": "JWT" 
}.{ 
  "documentId": "746c4a6f-f778-4970-83cd-9e21bf88326c", 
  "scopes": [ "doc:read", "doc:write", "summary:write" ],   
  "iat": 1599098963,  
  "exp": 1599098963,  
  "tenantId": "AzureFluidTenantId",  
  "ver": "1.0",
  "jti": "d7cd6602-2179-11ec-9621-0242ac130002"
}.[Signature] 
```

## <a name="how-can-you-generate-an-azure-fluid-relay-token"></a>Comment générer un jeton Relais Azure Fluid ? 

Vous pouvez utiliser le package npm [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) et signer votre jeton à l’aide de cette méthode.

```typescript
export function getSignedToken(
    tenantId: string,
    documentId: string,
    tokenLifetime: number = 60 * 60,
    ver: string = "1.0") {
        jwt.sign(
            {
                documentId, 
                user: {
                    displayName: "displayName", 
                    id: "userId", 
                    name: "userName" 
                }, 
                scopes: ["doc:read", "doc:write", "summary:write"], 
                iat: Math.round((new Date()).getTime() / 1000), 
                exp: Math.round((new Date()).getTime() / 1000) + tokenLifetime, //set the expiry date based on your needs but max-limit is one hour.
                tenantId, 
                ver,
                jti: uuid(), 
            },
            "<tenant_key>");
    }
```
