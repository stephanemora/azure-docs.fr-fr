---
title: API REST Azure App Configuration - Autorisation HMAC
description: Utiliser HMAC pour l’autorisation auprès d’Azure App Configuration à l’aide de l’API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423651"
---
# <a name="hmac-authorization---rest-api-reference"></a>Autorisation HMAC - Référence à l’API REST

Lorsque l’authentification HMAC est utilisée, les opérations se répartissent dans l’une des deux catégories, lecture ou écriture. Les clés d’accès en lecture-écriture accordent l’autorisation d’appeler toutes les opérations. Les clés d’accès en lecture uniquement accordent l’autorisation d’appeler uniquement les opérations de lecture. Le fait qu’une clé d’accès soit en lecture seule ou en lecture-écriture est déterminé par sa propriété `readOnly`. Toute tentative de création d’une requête d’écriture avec une clé d’accès en lecture seule entraîne la non-autorisation de la requête.

## <a name="obtaining-access-keys"></a>Obtention de clés d'accès

La spécification décrivant les clés d’accès et l’API utilisées pour les obtenir est détaillée [ici](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json), dans les caractéristiques du fournisseur de ressources Azure App Configuration. Les clés d’accès sont obtenues par le biais de l’opération « ConfigurationStores_ListKeys ».

## <a name="errors"></a>Erreurs

```http
HTTP/1.1 403 Forbidden
```

**Raison :** La clé d’accès utilisée pour authentifier la requête ne fournit pas les autorisations requises pour effectuer l’opération demandée.
**Solution :** Obtenez une clé d’accès qui fournit l’autorisation d’effectuer l’opération demandée et de l’utiliser pour authentifier la requête.
