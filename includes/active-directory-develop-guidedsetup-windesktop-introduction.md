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
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509848"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Appeler l’API Microsoft Graph à partir d’une application de bureau Windows

Ce guide explique comment une application de bureau Windows .NET (XAML) native utilise un jeton d’accès pour appeler l’API Microsoft Graph. L’application peut également accéder aux autres API qui nécessitent des jetons d’accès d’un point de terminaison de la plateforme d’identité Microsoft v2.0 pour les développeurs. Cette plateforme était anciennement Azure AD.

À la fin de ce guide, votre application sera capable d’appeler une API protégée utilisant des comptes personnels (y compris outlook.com, live.com, etc.). L’application utilisera également des comptes professionnels et scolaires de toute entreprise ou organisation utilisant Azure Active Directory.  

> [!NOTE]
> Ce guide nécessite Visual Studio 2015 Update 3, Visual Studio 2017 ou Visual Studio 2019. Vous n’avez aucune de ces versions ? [Téléchargez Visual Studio 2019 gratuitement](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Fonctionnement de l’exemple d’application de ce guide

![Fonctionnement de l’exemple d’application généré par ce tutoriel](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

L’exemple d’application que vous créez avec ce guide permet à une application de bureau Windows d’interroger l’API Microsoft Graph ou une API web qui accepte les jetons d’un point de terminaison de la plateforme d’identité Microsoft. Pour ce scénario, vous ajoutez un jeton aux requêtes HTTP via l’en-tête d’autorisation. La bibliothèque d’authentification Microsoft (MSAL) gère l’acquisition et le renouvellement de jetons.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestion de l’acquisition de jetons pour accéder à des API web protégées

Une fois l’utilisateur authentifié, l’exemple d’application reçoit un jeton que vous pouvez utiliser pour interroger l’API Microsoft Graph ou une API web sécurisée par la plateforme d’identité Microsoft pour les développeurs.

Les API comme Microsoft Graph nécessitent un jeton d’accès pour autoriser l’accès à des ressources spécifiques. Par exemple, un jeton est nécessaire pour lire le profil d’un utilisateur, accéder au calendrier d’un utilisateur ou envoyer un e-mail. Votre application peut demander un jeton d’accès à l’aide de MSAL pour accéder à ces ressources en spécifiant les étendues d’API. Ce jeton d’accès est ensuite ajouté à l’en-tête d’autorisation HTTP pour chaque appel effectué sur la ressource protégée.

MSAL gère la mise en cache et l’actualisation des jetons d’accès pour vous, ce qui évite à votre application d’avoir à le faire.

## <a name="nuget-packages"></a>Packages NuGet

Ce guide utilise les packages NuGet suivants :

|Bibliothèque|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Bibliothèque d’authentification Microsoft (MSAL.NET)|
