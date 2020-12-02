---
title: API REST Azure App Configuration – Authentification
description: Pages de référence pour l’authentification à l’aide de l’API REST Azure App Configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183462"
---
# <a name="authentication"></a>Authentification

Toutes les requêtes HTTP doivent être authentifiées. Les schémas d’authentification suivants sont pris en charge.

## <a name="hmac"></a>HMAC

[L’authentification HMAC](./rest-api-authentication-hmac.md) utilise un secret généré de manière aléatoire pour signer les charges utiles de la requête. Vous trouverez des détails sur le mode d’autorisation des requêtes utilisant cette méthode d’authentification dans la section [Autorisation HMAC](./rest-api-authorization-hmac.md).

## <a name="azure-active-directory"></a>Azure Active Directory

L’[authentification Azure Active Directory (Azure AD)](../active-directory/authentication/overview-authentication.md) utilise un porteur provenant d’Azure Active Directory pour authentifier les requêtes. Vous trouverez des détails sur le mode d’autorisation des requêtes utilisant cette méthode d’authentification dans la section [Autorisation Azure AD](./rest-api-authorization-azure-ad.md).