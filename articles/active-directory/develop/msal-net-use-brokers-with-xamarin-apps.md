---
title: Utiliser des répartiteurs avec Xamarin iOS et Android | Azure
titleSuffix: Microsoft identity platform
description: Découvrez comment configurer des applications Xamarin iOS capables d’utiliser Microsoft Authenticator et Microsoft Authentication Library pour .NET (MSAL.NET). Découvrez aussi comment migrer d’Azure AD Authentication Library pour .NET (ADAL.NET) vers Microsoft Authentication Library pour .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8e19677adf5fe0f64ad9e1c845f516f81ad89512
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166057"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Utiliser Microsoft Authenticator ou le Portail d’entreprise Intune sur des applications Xamarin

Sur Android et iOS, des répartiteurs tels que Microsoft Authenticator et le Portail d’entreprise Microsoft Intune pour Android permettent d’effectuer les opérations suivantes :

- **Authentification unique (SSO)**  : les utilisateurs n’ont pas besoin de se connecter à chaque application.
- **Identification de l’appareil** : le répartiteur accède au certificat de l’appareil. Ce certificat est créé sur l’appareil quand celui-ci est joint à l’espace de travail.
- **Vérification de l’identification de l’application** : quand une application appelle le répartiteur, elle transmet son URL de redirection. Le répartiteur vérifie alors l’URL.

Pour activer l’une de ces fonctionnalités, utilisez le paramètre `WithBroker()` lorsque vous appelez la méthode `PublicClientApplicationBuilder.CreateApplication`. Par défaut, le paramètre `.WithBroker()` est défini sur true. 

Aidez-vous également des instructions fournies dans les sections suivantes afin de configurer l’authentification répartie pour des applications [iOS](#brokered-authentication-for-ios) ou [Android](#brokered-authentication-for-android).

## <a name="brokered-authentication-for-ios"></a>Authentification répartie pour iOS

Effectuez ces étapes pour permettre à votre application Xamarin.iOS de communiquer avec l’application [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="step-1-enable-broker-support"></a>Étape 1 : Activer la prise en charge du répartiteur
Vous devez activer la prise en charge du répartiteur pour chaque instance de `PublicClientApplication`. La prise en charge est désactivée par défaut. Si vous créez `PublicClientApplication` avec `PublicClientApplicationBuilder`, utilisez le paramètre `WithBroker()` comme dans l’exemple suivant. Par défaut, le paramètre `WithBroker()` est défini sur true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Étape 2 : Activer l’accès au trousseau

Pour activer l’accès au trousseau, vous devez avoir défini un groupe d’accès au trousseau pour votre application. Vous pouvez utiliser l’API `WithIosKeychainSecurityGroup()` pour définir votre groupe d’accès au trousseau lorsque vous créez votre application :

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Pour plus d'informations, consultez [Activer l’accès au trousseau](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Étape 3 : Mettre à jour AppDelegate pour gérer le rappel
Lorsque Microsoft Authentication Library for .NET (MSAL.NET) appelle le répartiteur, ce dernier rappelle votre application à l’aide de la méthode `OpenUrl` de la classe `AppDelegate`. Comme MSAL attend la réponse du répartiteur, votre application doit coopérer pour rappeler MSAL.NET. Pour permettre cette coopération, mettez à jour le fichier `AppDelegate.cs` pour remplacer la méthode suivante.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {                
         return false;                  
    }
    
    return true;     
}            
```

Cette méthode est appelée à chaque démarrage de l’application. Elle permet de traiter la réponse du répartiteur et d’accomplir le processus d’authentification lancé par MSAL.NET.

### <a name="step-4-set-uiviewcontroller"></a>Étape 4 : Définir UIViewController()
Toujours dans le fichier `AppDelegate.cs`, vous devez définir une fenêtre d’objet. Normalement, pour Xamarin iOS, vous n’êtes pas tenu de définir cette fenêtre. Toutefois, vous avez besoin d’une fenêtre d’objet pour envoyer des demandes au répartiteur et recevoir ses réponses. 

Pour configurer la fenêtre d’objet : 
1. Dans le fichier `AppDelegate.cs`, définissez `App.RootViewController` sur un nouveau `UIViewController()`. Cette affectation garantit que l’appel au répartiteur comprend `UIViewController`. Si ce paramètre n’est pas correctement affecté, vous risquez de recevoir cette erreur :

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Dans l’appel `AcquireTokenInteractive`, utilisez `.WithParentActivityOrWindow(App.RootViewController)`, puis passez la référence à la fenêtre d’objet que vous allez utiliser.

    Dans `App.cs` :

    ```csharp
       public static object RootViewController { get; set; }
    ```

    Dans `AppDelegate.cs` :

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    Dans l’appel `AcquireToken` :

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Étape 5 : Inscrire un schéma d’URL
MSAL.NET utilise des URL pour appeler le répartiteur, avant de retourner la réponse du répartiteur à votre application. Pour effectuer l’aller-retour, inscrivez un schéma d’URL pour votre application dans le fichier `Info.plist`.

Le nom `CFBundleURLSchemes` doit inclure le préfixe `msauth.`, suivi de `CFBundleURLName`. 

Dans le schéma d’URL, `BundleId` identifie de manière unique l’application : `$"msauth.(BundleId)"`. Par conséquent, si `BundleId` est `com.yourcompany.xforms`, le schéma d’URL est `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Ce schéma d’URL fait alors partie de l’URI de redirection qui identifie de manière unique votre application lorsqu’elle reçoit la réponse du répartiteur.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Étape 6 : Ajouter l’identificateur du répartiteur à la section LSApplicationQueriesSchemes

MSAL utilise `–canOpenURL:` pour vérifier si le répartiteur est installé sur l’appareil. Dans iOS 9, Apple a verrouillé les schémas qu’une application peut interroger. 

Ajoutez `msauthv2` à la section `LSApplicationQueriesSchemes` du fichier `Info.plist`, comme dans l’exemple suivant :

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Étape 7 : Inscrire votre URI de redirection dans le portail d’applications

Si vous utilisez le répartiteur, votre URI de redirection doit remplir une exigence supplémentaire. Le format de l'URI de redirection _doit_ être le suivant :

```csharp
$"msauth.{BundleId}://auth"
```

Voici un exemple :

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Vous remarquerez que l’URI de redirection correspond au nom `CFBundleURLSchemes` que vous avez indiqué dans le fichier `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Étape 8 : Vérifier que l’URI de redirection est inscrit avec votre application

L’URI de redirection doit être inscrit dans le [portail d’inscription d’application](https://portal.azure.com) comme URI de redirection valide pour votre application. 

Le portail d’inscription d’application offre une nouvelle expérience conçue pour vous aider à calculer l’URI de réponse du répartiteur à partir de l’ID de bundle. 

Pour calculer l’URI de redirection :

1. Dans le portail d’inscription d’application, choisissez **Authentification** > **Essayer la nouvelle expérience**.

   ![Essayer la nouvelle expérience d’inscription d’application](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Sélectionnez **Ajouter une plateforme**.

   ![Ajouter une plateforme](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Lorsque la liste des plateformes est prise en charge, sélectionnez **iOS**.

   ![Configurer iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Entrez votre ID d’offre groupée comme demandé, puis sélectionnez **Configurer**.

   ![Entrer l’ID de bundle](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Quand vous avez terminé ces étapes, l’URI de redirection est automatiquement calculé.

![Copier URI de redirection](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Authentification répartie pour Android

### <a name="step-1-enable-broker-support"></a>Étape 1 : Activer la prise en charge du répartiteur

La prise en charge du répartiteur est activée pour chaque application cliente publique (PublicClientApplication). Elle est désactivée par défaut. Utilisez le paramètre `WithBroker()` (défini sur true par défaut) lors de la création de `IPublicClientApplication` via `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Étape 2 : Mettre à jour AppDelegate pour gérer le rappel

Quand MSAL.NET appelle le répartiteur, celui-ci rappelle votre application avec la méthode OnActivityResult(). Étant donné que MSAL attend la réponse du répartiteur, votre application doit router le résultat vers MSAL.NET.
Pour router le résultat vers `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`, vous pouvez remplacer la méthode OnActivityResult() comme indiqué ci-dessous.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Cette méthode est appelée chaque fois que l’application de répartiteur est lancée et permet de traiter la réponse du répartiteur et d’accomplir le processus d’authentification démarré par MSAL.NET.

### <a name="step-3-set-an-activity"></a>Étape 3 : Définir une activité

Pour que l’authentification répartie fonctionne, vous devez définir une activité afin que MSAL puisse envoyer et recevoir la réponse du répartiteur.

Pour cela, vous devez fournir l’activité (généralement MainActivity) à `WithParentActivityOrWindow(object parent)` en tant qu’objet parent. 

**Par exemple :**

Dans l’appel d’acquisition de jeton :

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Étape 4 : Inscrire votre RedirectUri sur le portail d’application

MSAL utilise des URL pour appeler le répartiteur, avant de retourner à votre application. Pour effectuer l’aller-retour, vous devez inscrire un schéma d’URL pour votre application. Cet URI de redirection doit être inscrit dans le portail d’inscription des applications Azure AD en tant qu’URI de redirection valide pour votre application.


L’URI de redirection dont vous avez besoin pour votre application dépend du certificat qui a été utilisé pour signer l’APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

La dernière partie de l’URI (`hgbUYHVBYUTvuvT&Y6tr554365466=`) correspond à la signature utilisée pour l’APK, encodée au format base64.
Toutefois, pendant la phase de développement de votre application dans Visual Studio, si vous déboguez votre code sans signer l’APK avec un certificat, Visual Studio le signera automatiquement à des fins de débogage. De cette façon, l’APK reçoit une signature unique pour l’ordinateur sur lequel il est créé. Par conséquent, chaque fois que vous créez votre application sur un autre ordinateur, vous devez mettre à jour l’URI de redirection dans le code de l’application et dans l’inscription de l’application sur le portail Azure afin de vous authentifier auprès de MSAL. 

Lors du débogage, vous pouvez rencontrer une exception MSAL (ou un message du journal) indiquant que l’URI de redirection fourni est incorrect. **Cette exception vous fournira également l’URI de redirection que vous devez utiliser** sur l’ordinateur où vous effectuez le débogage. Pour le moment, vous pouvez utiliser cet URI de redirection pour poursuivre le développement.

Une fois que vous êtes prêt à finaliser votre code, mettez à jour l’URI de redirection dans le code et dans l’inscription de l’application sur le portail Azure afin d’utiliser la signature du certificat avec lequel vous allez signer l’APK.

Dans la pratique, cela signifie que vous devez inscrire un URI de redirection pour chaque membre de l’équipe, ainsi qu’un URI de redirection pour la version signée de production de l’APK.

Vous pouvez également calculer cette signature vous-même, à l’aide d’une méthode similaire à celle de MSAL : 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Vous avez également la possibilité d’acquérir la signature de votre package à l’aide du keytool avec les commandes suivantes :

Pour Windows : `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Pour Mac : `keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [considérations relatives à l’utilisation de la plateforme Windows universelle avec MSAL.NET](msal-net-uwp-considerations.md).
