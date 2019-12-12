---
title: Bien démarrer avec Azure AD Xamarin | Microsoft Docs
description: Créez des applications Xamarin qui s’intègrent avec Azure AD pour la connexion et appellent des API protégées par Azure AD en utilisant OAuth.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6197a27b9a94de0f9a10407215e5755689cd0b56
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920818"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Démarrage rapide : Créer une application Xamarin qui intègre la connexion Microsoft

La [plateforme des identités Microsoft](v2-overview.md) est une évolution de la plateforme de développement Azure Active Directory (Azure AD). Elle permet aux développeurs de générer des applications qui connectent toutes les identités Microsoft et obtiennent des jetons pour appeler des API Microsoft comme Microsoft Graph ou des API que des développeurs ont créées.

[Microsoft Authentication Library (MSAL)](msal-overview.md) permet aux développeurs d’acquérir des jetons à partir du point de terminaison de la plateforme des identités Microsoft afin d’accéder aux API web sécurisées. La Bibliothèque d’authentification Active Directory (ADAL) s’intègre avec le point de terminaison d’Azure AD pour les développeurs (v1.0), tandis que la bibliothèque MSAL s’intègre avec le point de terminaison de la plateforme d’identités Microsoft (v2.0).

## <a name="next-steps"></a>Étapes suivantes

Pour les nouvelles applications Xamarin, nous vous recommandons d’utiliser la Plateforme d’identités Microsoft (v2.0) et la bibliothèque MSAL pour acquérir des jetons et accéder aux API web sécurisées. Pour démarrer, consultez [Integrate Microsoft identity and the Microsoft Graph into a Xamarin forms app using MSAL](https://github.com/azure-samples/active-directory-xamarin-native-v2#integrate-microsoft-identity-and-the-microsoft-graph-into-a-xamarin-forms-app-using-msal) (sans les étapes facultatives).
