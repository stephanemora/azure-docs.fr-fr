---
title: Considérations de Xamarin Android (bibliothèque d’authentification Microsoft pour .NET) | Azure
description: En savoir plus sur les considérations spécifiques lorsque vous utilisez Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
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
ms.openlocfilehash: cb0cfb06e95cadbb549f669e5d59bdb0d795c896
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545880"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Considérations spécifiques à Xamarin Android avec MSAL.NET
Cet article traite des considérations spécifiques lorsque vous utilisez Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).

Cet article s’applique à MSAL.NET 3.x. Si vous êtes intéressé par MSAL.NET 2.x, consultez [caractéristiques de Xamarin Android dans MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Définissez l’activité parent

Dans Xamarin.Android, vous devez définir l’activité parente afin que le jeton Obtient une fois l’interaction a eu lieu.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Assurer un contrôle revient à la bibliothèque MSAL une fois la partie interactive de la fin du flux d’authentification
Sur Android, vous devez remplacer le `OnActivityResult` méthode de la `Activity` et appelez la méthode SetAuthenticationContinuationEventArgs de la classe AuthenticationContinuationHelper MSAL.

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```
Cette ligne garantit que le contrôle revient à la bibliothèque MSAL une fois que la partie interactive du flux d’authentification s’est terminée.

## <a name="update-the-android-manifest"></a>Mettre à jour le manifeste Android
Le `AndroidManifest.xml` doit contenir les valeurs suivantes :
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Utilisez la vue web incorporées (facultative)

Par défaut MSAL.NET utilise le navigateur web du système, qui vous permet d’obtenir l’authentification unique avec les applications Web et autres applications. Dans certains cas rares, vous souhaiterez sans doute spécifier que vous souhaitez utiliser la vue web intégré. Pour plus d’informations, consultez [MSAL.NET utilise un navigateur Web](msal-net-web-browsers.md) et [navigateur du système Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Résolution de problèmes
Si vous créez une application Xamarin.Forms et que vous ajoutez une référence au package NuGet de MSAL.Net, cela fonctionnera.
Toutefois, si vous souhaitez mettre à niveau une application Xamarin.Forms pour MSAL.NET afficher un aperçu 1.1.2 ou plus tard vous rencontrerez peut-être des problèmes de build.

Pour résoudre ces problèmes, vous devez :
- Mettre à jour le package NuGet de MSAL.NET existant à la version préliminaire MSAL.NET 1.1.2 ou version ultérieure
- Vérifiez que Xamarin.Forms est automatiquement mis à jour vers la version 2.5.0.122203 (si ce n’est pas, la mise à jour vers cette version)
- Vérifiez que Xamarin.Android.Support.v4 est automatiquement mis à jour vers la version 25.4.0.2 (si ce n’est pas, la mise à jour vers cette version)
- Tous les packages Xamarin.Android.Support doivent cibler la version 25.4.0.2
- Nettoyer/régénérer
- Essayez d’affecter le projet parallèle max s’appuie sur 1 dans Visual Studio (Options -> projets et Solutions -> Générer et exécuter -> nombre maximal de générations de projets parallèles)
- Vous pouvez également, si vous générez à partir de la ligne de commande, essayez de le supprimer /m à partir de votre commande si vous l’utilisez.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Error: Le nom 'AuthenticationContinuationHelper' n’existe pas dans le contexte actuel

Il s’agit probablement, car Visual Studio ne pas correctement à jour le fichier Android.csproj*. Parfois le **<HintPath>** filepath contient incorrectement netstandard13 au lieu de **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Étapes suivantes

Plus de détails et d’exemples sont fournis dans le [considérations spécifiques pour Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) paragraphe du fichier readme.md de l’exemple suivant :

| Exemple | Plateforme | Description  |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Une application Xamarin Forms simple expliquant comment utiliser MSAL pour authentifier le compte de service administré et Azure AD via le point de terminaison v2.0 ajoutez et accéder à Microsoft Graph avec le jeton résultant. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |