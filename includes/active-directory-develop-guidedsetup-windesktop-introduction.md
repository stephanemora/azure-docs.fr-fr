---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121583"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Appeler l’API Microsoft Graph à partir d’une application de bureau Windows

Ce guide explique comment une application de bureau Windows .NET (XAML) native peut obtenir un jeton d’accès et appeler l’API Microsoft Graph ou d’autres API qui nécessitent des jetons d’accès provenant d’un point de terminaison de la plateforme d’identité Microsoft pour les développeurs (auparavant appelée, Azure AD) v2.0.

À la fin de ce guide, votre application sera capable d’appeler une API protégée utilisant des comptes personnels (y compris outlook.com, live.com, etc.). L’application utilisera également des comptes professionnels et scolaires de toute entreprise ou organisation utilisant Azure Active Directory.  

> [!NOTE]
> Le guide requiert Visual Studio 2015 Update 3 ou Visual Studio 2017. Vous n’avez aucune de ces versions ? [Téléchargez gratuitement Visual Studio 2017](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

L’exemple d’application que vous créez avec ce guide permet à une application de bureau Windows d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons à partir d’un point de terminaison de la plateforme d’identité Microsoft. Pour ce scénario, vous ajoutez un jeton aux requêtes HTTP via l’en-tête d’autorisation. La bibliothèque d’authentification Microsoft (MSAL) gère l’acquisition et le renouvellement de jetons.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestion de l’acquisition de jetons pour accéder à des API web protégées

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un jeton qui peut être utilisé pour interroger l’API Microsoft Graph ou une API web sécurisée par la plateforme d’identité Microsoft pour les développeurs.

Les API comme Microsoft Graph nécessitent un jeton d’accès pour autoriser l’accès à des ressources spécifiques. Par exemple, un jeton est nécessaire pour lire le profil d’un utilisateur, accéder au calendrier d’un utilisateur ou envoyer un e-mail. Votre application peut demander un jeton d’accès à l’aide de MSAL pour accéder à ces ressources en spécifiant les étendues d’API. Ce jeton d’accès est ensuite ajouté à l’en-tête d’autorisation HTTP pour chaque appel effectué sur la ressource protégée.

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.

## <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise les packages NuGet suivants :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Bibliothèque d’authentification Microsoft (MSAL.NET)|
