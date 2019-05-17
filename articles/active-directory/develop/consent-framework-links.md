---
title: Fonctionnement du consentement d’application | Microsoft Docs
description: En savoir plus sur le fonctionnement de l’infrastructure de consentement Azure AD pour voir comment vous pouvez l’utiliser lors du développement d’applications sur Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766b7572ed54cc194dc28fce1ad7e4979f1af5a5
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540130"
---
# <a name="how-application-consent-works"></a>Fonctionnement du consentement d’application

Cet article présente le fonctionnement de l’infrastructure de consentement Azure AD afin de développer des applications plus efficacement.

## <a name="recommended-documents"></a>Documents recommandés

- Consultez une présentation générale de [la façon dont le consentement permet au propriétaire de ressources de gérer l’accès aux ressources d’une application](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent).
- Obtenez une présentation détaillée de [la façon dont l’infrastructure de consentement Azure AD implémente le consentement](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Pour plus de détails, découvrez [la façon dont une application mutualisée peut utiliser l’infrastructure de consentement](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) pour implémenter un consentement de type « utilisateur » et « admin », en prenant en charge des modèles d’applications mutualisées plus avancés.
- Pour plus de détails, découvrez [la façon dont le consentement est pris en charge au niveau de la couche du protocole OAuth 2.0 pendant le flux d’octroi de code d’autorisation.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>Étapes suivantes
[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
