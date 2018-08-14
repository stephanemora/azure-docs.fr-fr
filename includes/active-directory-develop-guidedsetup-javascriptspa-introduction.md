---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f70e0bcb68f059618f9b398a00e23498a10df23e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582926"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Appeler l’API Microsoft Graph à partir d’une application à page unique (SPA) JavaScript

Ce guide explique comment une application à page unique JavaScript peut permettre l’identification de comptes personnels, scolaires et professionnels, peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison Azure Active Directory v2.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application de ce guide](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Informations complémentaires

L’exemple d’application créé dans le cadre de ce guide permet à une application SPA JavaScript d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons à partir d’un point de terminaison Azure Active Directory v2. Pour ce scénario, une fois l’utilisateur authentifié, un jeton d’accès est demandé et ajouté aux requêtes HTTP via l’en-tête d’autorisation. L’acquisition et le renouvellement de jetons sont gérés par la bibliothèque d’authentification Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliothèques

Ce guide utilise la bibliothèque suivante :

|Bibliothèque|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Bibliothèque d’authentification Microsoft pour JavaScript Preview|

> [!NOTE]
> *msal.js* cible le *point de terminaison Azure Active Directory v2*, ce qui permet aux comptes personnels, scolaires et professionnels de se connecter et d’acquérir des jetons. Le *point de terminaison Azure Active Directory v2* a [certaines limitations](..\articles\active-directory\develop\active-directory-v2-limitations.md). Si vous êtes uniquement intéressé par les comptes scolaires et professionnels, utilisez *adal.js* et le *point de terminaison v1*. Pour comprendre les différences entre les points de terminaison v1 et v2, lisez la [comparaison v1-v2](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
