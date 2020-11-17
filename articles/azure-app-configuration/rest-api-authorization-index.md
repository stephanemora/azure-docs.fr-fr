---
title: API REST Azure App Configuration – Autorisation
description: Pages de référence pour l’autorisation à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423655"
---
# <a name="authorization"></a>Autorisation

L’autorisation fait référence à la procédure utilisée pour déterminer les autorisations dont dispose un appelant lors de l’établissement d’une demande. Il existe plusieurs modèles d’autorisation. Le modèle d’autorisation utilisé pour une demande dépend de la méthode d’[authentification](./rest-api-authentication-index.md) utilisée. Les modèles d’autorisation sont répertoriés ci-dessous.

## <a name="hmac"></a>HMAC

Le [modèle d’autorisation](./rest-api-authorization-hmac.md) associé à l’authentification HMAC fractionne les autorisations en lecture seule ou en lecture-écriture. Pour plus d’informations, consultez la page [Autorisation HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Le [modèle d’autorisation](./rest-api-authorization-azure-ad.md) associé à l’authentification Azure Active Directory (Azure AD) utilise un RBAC Azure pour contrôler les autorisations. Pour plus d’informations, consultez la page [Autorisation Azure AD](./rest-api-authorization-azure-ad.md).
