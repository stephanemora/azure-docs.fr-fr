---
title: API REST Azure App Configuration – Authentification
description: Pages de référence pour l’authentification à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 21a43a005b78c8916d06e97ca9d2ba21d5a585a3
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93423696"
---
# <a name="authentication"></a>Authentification

Toutes les requêtes HTTP doivent être authentifiées. Les schémas d’authentification suivants sont pris en charge.

## <a name="hmac"></a>HMAC

[L’authentification HMAC](./rest-api-authentication-hmac.md) utilise un secret généré de manière aléatoire pour signer les charges utiles de la requête. Vous trouverez des détails sur le mode d’autorisation des requêtes utilisant cette méthode d’authentification dans la section [Autorisation HMAC](./rest-api-authorization-hmac.md).

## <a name="azure-active-directory"></a>Azure Active Directory

L’[authentification Azure Active Directory (Azure AD)](/azure/active-directory/authentication/overview-authentication) utilise un porteur provenant d’Azure Active Directory pour authentifier les requêtes. Vous trouverez des détails sur le mode d’autorisation des requêtes utilisant cette méthode d’authentification dans la section [Autorisation Azure AD](./rest-api-authorization-azure-ad.md).
