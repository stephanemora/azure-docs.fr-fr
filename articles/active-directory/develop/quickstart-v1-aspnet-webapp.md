---
title: Ajouter la connexion avec Microsoft à une application web ASP.NET | Microsoft Docs
description: Découvrez comment ajouter la connexion Microsoft dans une solution ASP.NET avec une application basée sur un navigateur web traditionnel utilisant le standard OpenID Connect.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: quickstart
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc68db841b900ca96899e6fda8f8f3e07f41256a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921090"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Démarrage rapide : Ajouter la connexion avec Microsoft à une application ASP.NET

La [plateforme des identités Microsoft](v2-overview.md) est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler des API Microsoft comme Microsoft Graph ou des API que des développeurs ont créées.

[Microsoft Authentication Library (MSAL)](msal-overview.md) permet aux développeurs d’acquérir des jetons à partir du point de terminaison de la plateforme des identités Microsoft afin d’accéder aux API web sécurisées. La Bibliothèque d’authentification Active Directory (ADAL) s’intègre avec le point de terminaison d’Azure AD pour les développeurs (v1.0), tandis que la bibliothèque MSAL s’intègre avec le point de terminaison de la plateforme d’identités Microsoft (v2.0).

## <a name="next-steps"></a>Étapes suivantes

Pour les nouvelles applications web, nous vous recommandons d’utiliser la Plateforme d’identités Microsoft (v2.0) et la bibliothèque MSAL pour acquérir des jetons et accéder aux API web sécurisées. Consultez [Démarrage rapide : Ajouter la connexion avec Microsoft à une application web ASP.NET](quickstart-v2-aspnet-webapp.md) pour commencer.
