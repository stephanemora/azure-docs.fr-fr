---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a6fcbc0e8adac75f17d7379ff512ba650d0bb118
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203308"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Appeler l’API Microsoft Graph à partir d’une application à page unique (SPA) JavaScript

Ce guide explique comment une application à page unique JavaScript peut se connecter à des comptes personnels, scolaires et professionnels, obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.0.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Montre comment l’exemple d’application généré par cette fonctionne didacticiels](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro-updated.png)

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

L’exemple d’application créé dans ce guide permet à une application SPA JavaScript d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons provenant d’un point de terminaison Azure Active Directory v2.0. Dans ce scénario, une fois l’utilisateur authentifié, un jeton d’accès est demandé et ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliothèques

Ce guide utilise la bibliothèque suivante :

|Bibliothèque|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Bibliothèque d’authentification Microsoft pour JavaScript Preview|

> [!NOTE]
> *msal.js* cible le *point de terminaison Azure Active Directory v2.0*, ce qui permet aux comptes personnels, scolaires et professionnels de se connecter et d’acquérir des jetons. Le *point de terminaison Azure Active Directory v2.0* a [certaines limitations](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Pour comprendre les différences entre les points de terminaison v1.0 et v2.0, consultez [Comparer le point de terminaison Azure AD v2.0 avec le point de terminaison v1.0](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
