---
title: Créer une API web .NET Azure AD pour l’authentification et l’autorisation | Microsoft Docs
description: Création d’une API web MVC .NET qui s’intègre à Azure AD pour l’authentification et l’autorisation.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abc4c05aab6efcfc55d5e4dc812ab41a3e3fc8aa
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963271"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Démarrage rapide : Générer une API web .NET qui s’intègre à Azure AD pour l’authentification et l’autorisation

La [plateforme des identités Microsoft](v2-overview.md) est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler des API Microsoft comme Microsoft Graph ou des API que des développeurs ont créées.

[Microsoft Authentication Library (MSAL)](msal-overview.md) permet aux développeurs d’acquérir des jetons à partir du point de terminaison de la plateforme des identités Microsoft afin d’accéder aux API web sécurisées. La Bibliothèque d’authentification Active Directory (ADAL) s’intègre avec le point de terminaison d’Azure AD pour les développeurs (v1.0), tandis que la bibliothèque MSAL s’intègre avec le point de terminaison de la plateforme d’identités Microsoft (v2.0).

Pour les nouvelles API web, nous vous recommandons d’utiliser la plateforme des identités Microsoft (v 2.0) et MSAL pour acquérir des jetons et accéder aux API web sécurisées : [Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
