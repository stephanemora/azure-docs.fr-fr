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
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117360"
---
# <a name="how-application-consent-works"></a>Fonctionnement du consentement d’application

Cet article présente le fonctionnement de l’infrastructure de consentement Azure AD afin de développer des applications plus efficacement.

## <a name="recommended-documents"></a>Documents recommandés

- Consultez une présentation générale de [la façon dont le consentement permet au propriétaire de ressources de gérer l’accès aux ressources d’une application](./developer-glossary.md#consent).
- Obtenez une présentation détaillée de [la façon dont l’infrastructure de consentement Azure AD implémente le consentement](./quickstart-register-app.md).
- Pour plus de détails, découvrez [la façon dont une application mutualisée peut utiliser l’infrastructure de consentement](./howto-convert-app-to-be-multi-tenant.md) pour implémenter un consentement de type « utilisateur » et « admin », en prenant en charge des modèles d’applications mutualisées plus avancés.
- Pour plus de détails, découvrez [la façon dont le consentement est pris en charge au niveau de la couche du protocole OAuth 2.0 pendant le flux d’octroi de code d’autorisation.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Étapes suivantes
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)