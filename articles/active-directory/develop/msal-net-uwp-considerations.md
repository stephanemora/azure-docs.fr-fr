---
title: Considérations relatives à la plateforme de Windows Universal (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lorsque vous utilisez la plateforme Windows universelle avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: eb8076df5482c91942349e0a052794f3fe945a5f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076509"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considérations spécifiques à la plateforme Windows universelles avec MSAL.NET
Sur Xamarin iOS, il existe plusieurs règles que vous devez prendre en compte lors de l’utilisation de MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>La propriété UseCorporateNetwork
Dans la plateforme WinRT, `PublicClientApplication` a la propriété booléenne suivante ``UseCorporateNetwork``. Cette propriété permet aux applications Win8.1 et UWP à bénéficier de l’authentification Windows intégrée (et par conséquent l’authentification unique avec l’utilisateur connecté à l’aide du système d’exploitation) si l’utilisateur est connecté avec un compte dans un Azure fédéré client AD. Cela s’appuie sur WAB (Web Service Broker d’authentification). 

> [!IMPORTANT]
> Définition de cette propriété sur true suppose que le développeur d’applications a activé l’authentification de Windows intégrée (IWA) dans l’application. Pour cela :
> - Dans le ``Package.appxmanifest`` pour votre application UWP, dans le **fonctionnalités** onglet, activez les fonctionnalités suivantes :
>   - Authentification en entreprise
>   - Réseaux privés (client et serveur)
>   - Certificat utilisateur partagé

Authentification Windows intégrée n’est pas activée par défaut, car les applications demandant les fonctionnalités de l’authentification d’entreprise ou des certificats d’utilisateur partagé nécessitent un niveau supérieur de la vérification d’être acceptés dans le Windows Store, et pas tous les développeurs souhaiterez effectuer est élevé niveau de vérification. 

L’implémentation sous-jacente sur la plateforme UWP (WAB) ne fonctionne pas correctement dans les scénarios d’entreprise où l’accès conditionnel a été activé. Le symptôme est que l’utilisateur tente de se connecter à l’aide de hello de Windows et est proposée pour choisir un certificat, mais le certificat pour le code confidentiel est introuvable, ou l’utilisateur les sélectionne, mais jamais invité à entrer le code confidentiel. Une solution de contournement consiste à utiliser une autre méthode (nom d’utilisateur/mot de passe + téléphone authentification), mais l’expérience n’est pas bon. 

## <a name="next-steps"></a>Étapes suivantes
Plus de détails sont fournis dans les exemples suivants :

Exemple | Plateforme | Description 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Une application cliente de plateforme Windows universelle à l’aide de msal.net, l’accès à Microsoft Graph pour un utilisateur qui s’authentifie avec un point de terminaison v2.0 Azure AD. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier les MSA et Azure AD via le point de terminaison v2.0 AAD et accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|