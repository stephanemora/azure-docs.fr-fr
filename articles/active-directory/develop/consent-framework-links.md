---
title: Fonctionnement du consentement d’application
description: En savoir plus sur le fonctionnement de l’infrastructure de consentement Azure AD pour voir comment vous pouvez l’utiliser lors du développement d’applications sur Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2021
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 2b68be9da11bc1f868b6bf30d3794ee6a3bc9ab6
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154201"
---
# <a name="how-application-consent-works"></a>Fonctionnement du consentement d’application

Cet article présente le fonctionnement de l’infrastructure de consentement Azure AD afin de développer des applications plus efficacement.

## <a name="recommended-documents"></a>Documents recommandés

- Consultez une présentation générale de [la façon dont le consentement permet au propriétaire de ressources de gérer l’accès aux ressources d’une application](./developer-glossary.md#consent).
- Obtenez une présentation détaillée de [la façon dont l’infrastructure de consentement Azure AD implémente le consentement](./quickstart-register-app.md).
- Pour plus de détails, découvrez [la façon dont une application mutualisée peut utiliser l’infrastructure de consentement](./howto-convert-app-to-be-multi-tenant.md) pour implémenter un consentement de type « utilisateur » et « admin », en prenant en charge des modèles d’applications mutualisées plus avancés.
- Pour plus de détails, découvrez [la façon dont le consentement est pris en charge au niveau de la couche du protocole OAuth 2.0 pendant le flux d’octroi de code d’autorisation.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Étapes suivantes
[Questions et réponses Microsoft Azure AD](/answers/topics/azure-active-directory.html)
