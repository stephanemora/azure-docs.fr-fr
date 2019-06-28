---
title: Considérations relatives à la plateforme Windows universelle (Microsoft Authentication Library pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lors de l’utilisation de la plateforme Windows universelle avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544134"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considérations relatives à la plateforme Windows universelle avec MSAL.NET
Sur Xamarin iOS, il existe plusieurs considérations à prendre en compte lors de l’utilisation de MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>La propriété UseCorporateNetwork
Dans la plateforme WinRT, `PublicClientApplication` a la propriété booléenne suivante ``UseCorporateNetwork``. Cette propriété permet aux applications Win8.1 et UWP de bénéficier de l’authentification Windows intégrée (et par conséquent, de l’authentification unique avec l’utilisateur connecté à l’aide du système d’exploitation) si l’utilisateur est connecté avec un compte dans un locataire Azure AD fédéré. Elle s’appuie sur WAB (Web Authentication Broker). 

> [!IMPORTANT]
> La définition de cette propriété sur true suppose que le développeur d’applications a activé l’authentification Windows intégrée (IWA) dans l’application. Pour ce faire, procédez comme suit :
> - Dans ``Package.appxmanifest`` de votre application UWP, cliquez sur l’onglet **Fonctionnalités** et activez les fonctionnalités suivantes :
>   - Authentification en entreprise
>   - Réseaux privés (client et serveur)
>   - Certificat utilisateur partagé

L’Authentification Windows intégrée n’est pas activée par défaut parce que les applications demandant les capacités Authentification entreprise ou Certificats utilisateur partagés requièrent un niveau supérieur de vérification par le Windows Store, et que certains développeurs ne souhaitent pas utiliser le niveau supérieur de vérification. 

L’implémentation sous-jacente sur la plateforme UWP (WAB) ne fonctionne pas correctement dans les scénarios d’entreprise où l’accès conditionnel a été activé. L’utilisateur tente de se connecter à l’aide de Windows Hello, est invité à choisir un certificat, mais le certificat correspondant au code confidentiel est introuvable, ou l’utilisateur le sélectionne, mais n’ai jamais invité à entrer le code confidentiel. Une solution de contournement consiste à utiliser une autre méthode (nom d’utilisateur/mot de passe + authentification par téléphone), mais l’expérience n’est pas satisfaisante. 

## <a name="next-steps"></a>Étapes suivantes
Les exemples suivants fournissent plus d’informations :

Exemple | Plateforme | Description 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Une application cliente de plateforme Windows universelle utilisant msal.net et ayant accès à Microsoft Graph pour un utilisateur s’authentifiant avec un point de terminaison Azure AD v2.0. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier MSA et Azure AD via le point de terminaison AAD v2.0, et comment accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|