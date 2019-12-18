---
title: Considérations relatives à UWP (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: En savoir plus sur les considérations spécifiques lors de l’utilisation de la plateforme Windows universelle avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d14546a63db952119303428140c65ee402010816
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921792"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Considérations relatives à la plateforme Windows universelle avec MSAL.NET
Sur UWP, il existe plusieurs considérations à prendre en compte lors de l’utilisation de MSAL.NET.

## <a name="the-usecorporatenetwork-property"></a>La propriété UseCorporateNetwork
Dans la plateforme WinRT, `PublicClientApplication` a la propriété booléenne suivante ``UseCorporateNetwork``. Cette propriété permet aux applications Win8.1 et UWP de bénéficier de l’authentification Windows intégrée (et par conséquent, de l’authentification unique avec l’utilisateur connecté à l’aide du système d’exploitation) si l’utilisateur est connecté avec un compte dans un locataire Azure AD fédéré. Lorsque vous définissez cette propriété, MSAL.NET s’appuie sur WAB (Web Authentication Broker).

> [!IMPORTANT]
> La définition de cette propriété sur true suppose que le développeur d’applications a activé l’authentification Windows intégrée (IWA) dans l’application. Pour ce faire, procédez comme suit :
> - Dans ``Package.appxmanifest`` de votre application UWP, cliquez sur l’onglet **Fonctionnalités** et activez les fonctionnalités suivantes :
>   - Authentification en entreprise
>   - Réseaux privés (client et serveur)
>   - Certificat utilisateur partagé

L’Authentification Windows intégrée n’est pas activée par défaut parce que les applications demandant les capacités Authentification entreprise ou Certificats utilisateur partagés requièrent un niveau supérieur de vérification par le Windows Store, et que certains développeurs ne souhaitent pas utiliser le niveau supérieur de vérification.

L’implémentation sous-jacente sur la plateforme UWP (WAB) ne fonctionne pas correctement dans les scénarios d’entreprise où l’accès conditionnel a été activé. L’utilisateur tente de se connecter à l’aide de Windows Hello, est invité à choisir un certificat, mais :

- le certificat correspondant au code confidentiel est introuvable,
- ou l’utilisateur le sélectionne, mais n’est jamais invité à entrer le code confidentiel.

Une solution de contournement consiste à utiliser une autre méthode (nom d’utilisateur/mot de passe + authentification par téléphone), mais l’expérience n’est pas satisfaisante.

## <a name="troubleshooting"></a>Résolution de problèmes

Certains clients ont signalé que dans certains environnements d’entreprise spécifiques, l’erreur de connexion suivante s’est produite :

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

alors qu’ils savent qu’ils disposent d’une connexion Internet et que celle-ci fonctionne avec un réseau public.

Une solution de contournement consiste à s’assurer que WAB (le composant Windows sous-jacent) autorise le réseau privé. Pour ce faire, vous pouvez définir une clé de Registre :

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Pour plus d’informations, consultez [Web Authentication Broker - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Étapes suivantes
Les exemples suivants fournissent plus d’informations :

Exemple | Plateforme | Description 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Une application cliente de plateforme Windows universelle utilisant msal.net et ayant accès à Microsoft Graph pour un utilisateur s’authentifiant avec un point de terminaison Azure AD v2.0. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier MSA et Azure AD via le point de terminaison AAD v2.0, et comment accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
