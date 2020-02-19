---
title: Considérations relatives à Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les considérations à prendre en compte lors de l’utilisation de Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132511"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Considérations relatives à l’utilisation de Xamarin Android avec MSAL.NET
Cet article traite de ce que vous devez prendre en compte lorsque vous utilisez Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Définir l’activité parente

Sur Xamarin Android, définissez l’activité parente de façon à ce que le jeton soit retourné après l’interaction. Voici un exemple de code :

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

Dans MSAL 4.2 et versions ultérieures, vous pouvez également définir cette fonctionnalité au niveau de `PublicClientApplication`. Pour ce faire, utilisez un rappel :

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Si vous utilisez [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), votre code de générateur `PublicClientApplication` ressemble à l’exemple suivant.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>S’assurer que le contrôle retourne à MSAL 
Lorsque la partie interactive du flux d’authentification se termine, assurez-vous que le contrôle revient à MSAL. Sur Android, remplacez la méthode `OnActivityResult` de `Activity`. Appelez ensuite la méthode `SetAuthenticationContinuationEventArgs` de la classe MSAL `AuthenticationContinuationHelper`. 

Voici un exemple :

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

Cette ligne garantit que le contrôle revient à la bibliothèque MSAL à la fin de la partie interactive du flux d’authentification.

## <a name="update-the-android-manifest"></a>Mettre à jour le manifeste Android
Le fichier *AndroidManifest.xml* doit contenir les valeurs suivantes :

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Remplacez le nom de package que vous avez inscrit dans le Portail Azure par la valeur `android:host=`. Remplacez le hachage de clé que vous avez inscrit dans le Portail Azure par la valeur `android:path=`. Le hachage de signature *ne doit pas* être encodé par URL. Assurez-vous qu’une barre oblique (`/`) précède votre hachage de signature.

Vous pouvez également [créer l’activité dans le code](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) plutôt que de modifier manuellement le fichier *AndroidManifest.xml*. Pour créer l’activité dans le code, commencez par créer une classe qui comprend les attributs `Activity` et `IntentFilter`. 

Voici un exemple de classe qui représente les valeurs du fichier XML :

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Manifeste Xamarin.Forms 4.3.X

Xamarin.Forms 4.3.x génère du code qui définit l’attribut `package` sur `com.companyname.{appName}` dans le fichier *AndroidManifest.xml*. Si vous utilisez `DataScheme` comme `msal{client_id}`, vous souhaiterez peut-être modifier la valeur pour qu’elle corresponde à la valeur de l’espace de noms `MainActivity.cs`.

## <a name="use-the-embedded-web-view-optional"></a>Utilisez la vue web incorporée (facultatif)

Par défaut, MSAL.NET utilise le navigateur web du système. Ce navigateur vous permet d’accéder à l’authentification unique (SSO) à l’aide d’applications web et d’autres applications. Dans certains cas rares, vous souhaiterez peut-être que votre système utilise une vue web incorporée. 

Cet exemple de code montre comment configurer une vue web incorporée :

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Pour plus d’informations, consultez [Utiliser des navigateurs web pour MSAL.NET](msal-net-web-browsers.md) et [Considérations relatives au navigateur système Xamarin Android](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Dépanner
Vous pouvez créer une application Xamarin.Forms et ajouter une référence au package NuGet de MSAL.NET.
Toutefois, vous pouvez rencontrer des problèmes de build si vous mettez à niveau une application Xamarin.Forms existante vers MSAL.NET (préversion) version 1.1.2 ou ultérieure.

Pour détecter des problèmes de build :

- Mettez à jour le package NuGet existant de MSAL.NET vers MSAL.NET (préversion) version 1.1.2 ou ultérieure.
- Vérifiez que Xamarin.Forms est automatiquement mis à jour vers la version 2.5.0.122203. Si nécessaire, mettez à jour Xamarin.Forms vers cette version.
- Vérifiez que Xamarin.Android.Support.v4 est automatiquement mis à jour vers la version 25.4.0.2. Si nécessaire, mettez-le à jour vers la version 25.4.0.2.
- Assurez-vous que tous les packages Xamarin.Android.Support ciblent la version 25.4.0.2.
- Nettoyez ou régénérez l’application.
- Dans Visual Studio, essayez de définir le nombre maximal de builds de projets en parallèle sur 1. Pour ce faire, sélectionnez **Options** > **Projets et solutions** > **Générer et exécuter** > **Nombre maximal de builds de projets parallèles**.
- Si vous générez à partir de la ligne de commande et que votre commande utilise `/m`, essayez de supprimer cet élément de la commande.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erreur : Le nom AuthenticationContinuationHelper n’existe pas dans le contexte actuel

Si une erreur indique que `AuthenticationContinuationHelper` n’existe pas dans le contexte actuel, Visual Studio a peut-être pas mis à jour le fichier Android.csproj* de manière incorrecte. Parfois le chemin d’accès au fichier *\<HintPath>* contient la valeur *netstandard13* au lieu de *monoandroid90*, ce qui est incorrect.

Cet exemple contient un chemin d’accès au fichier correct :

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez l’exemple d’une [application mobile Xamarin utilisant la Plateforme d’identités Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Le tableau suivant récapitule les informations pertinentes dans le fichier LISEZMOI.

| Exemple | Plateforme | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Application Xamarin.Forms simple qui montre comment utiliser MSAL pour authentifier les comptes personnels Microsoft et Azure AD via le point de terminaison Azure AD 2.0. L’application montre également comment accéder à Microsoft Graph et affiche le jeton résultant. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |
