---
title: Utiliser un certificat TLS/SSL dans du code
description: Découvrez comment utiliser des certificats clients dans votre code. Authentifiez-vous auprès de ressources distantes avec un certificat client ou exécutez des tâches de chiffrement avec celles-ci.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b62352d09419de11135f4d7a2740e0e74b80255d
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962126"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Utiliser un certificat TLS/SSL dans votre code dans Azure App Service

Dans votre code d’application, vous pouvez accéder aux [certificats publics ou privés que vous ajoutez à App Service](configure-ssl-certificate.md). Votre code d’application peut agir en tant que client et accéder à un service externe qui exige une authentification par certificat, ou il peut être amené à effectuer des tâches de chiffrement. Ce guide pratique explique comment utiliser des certificats publics ou privés dans le code de votre application.

Cette approche de l'utilisation des certificats dans votre code utilise la fonctionnalité TLS d'App Service, qui requiert que votre application soit au niveau **De base** ou supérieur. Si votre application se situe au niveau **Gratuit** ou **Partagé**, vous pouvez [inclure le fichier de certificat dans le référentiel de votre application](#load-certificate-from-file).

Lorsque vous confiez la gestion de vos certificats TLS/SSL à App Service, vous pouvez conserver les certificats et le code de votre application séparément et protéger vos données sensibles.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce guide pratique, vous devez au préalable :

- [Création d’une application App Service](./index.yml)
- [Ajouter un certificat à votre application](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trouver l’empreinte

Sur le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, dans le menu de gauche, sélectionnez **App Services** >  **\<app-name>** .

Dans la barre de navigation gauche de votre application, sélectionnez **Paramètres TLS/SSL**, puis **Certificats de clé privée (.pfx)** ou **Certificats de clé publique (.cer)** .

Trouvez le certificat que vous souhaitez utiliser et copiez-en l’empreinte.

![Copier l’empreinte du certificat](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Rendre le certificat accessible

Pour accéder au certificat dans le code de votre application, ajoutez son Thumbprint au paramètre d’application `WEBSITE_LOAD_CERTIFICATES` en exécutant la commande suivante dans <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pour rendre tous vos certificats accessibles, définissez la valeur sur `*`.

## <a name="load-certificate-in-windows-apps"></a>Charger le certificat dans les applications Windows

Le paramètre d’application `WEBSITE_LOAD_CERTIFICATES` rend les certificats spécifiés accessibles pour votre application hébergée sur Windows dans le magasin de certificats Windows, dont l’emplacement dépend du [niveau tarifaire](overview-hosting-plans.md) :

- Niveau **Isolé**, dans [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Tous les autres niveaux, dans [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

Dans le code C#, vous accédez au certificat par l’empreinte numérique du certificat. Le code suivant charge un certificat avec l’empreinte `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

Dans le code Java, vous accédez au certificat à partir du magasin « Windows-MY » par le biais du champ Subject Common Name (Nom commun de l’objet) (consultez [Certificat de clé publique](https://en.wikipedia.org/wiki/Public_key_certificate)). Le code suivant montre comment charger un certificat de clé privée :

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Pour les langages qui ne prennent pas en charge ou qui n’offrent pas de prise en charge suffisante pour le magasin de certificats Windows, consultez [Charger un certificat à partir d’un fichier](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Charger le certificat dans les applications Linux

Le paramètre d’application `WEBSITE_LOAD_CERTIFICATES` permet l’accès des certificats spécifiés à vos applications hébergées sur Linux (y compris les applications de conteneur personnalisées) sous la forme de fichiers. Les fichiers se trouvent sous les répertoires suivants :

- Certificats privés - `/var/ssl/private` (fichiers `.p12`)
- Certificats publics - `/var/ssl/certs` (fichiers `.der`)

Les noms des fichiers de certificat sont les empreintes numériques de certificat. Le code C# suivant illustre le chargement d’un certificat public dans une application Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Pour savoir comment charger un certificat TLS/SSL à partir d'un fichier dans Node.js, PHP, Python, Java ou Ruby, consultez la documentation relative à la plateforme web ou au langage correspondant.

## <a name="load-certificate-from-file"></a>Charger un certificat à partir d’un fichier

Si vous devez charger un fichier de certificat que vous transférez manuellement vers le serveur, il est préférable de procéder à ce chargement en utilisant [FTPS](deploy-ftp.md) plutôt que [Git](deploy-local-git.md), par exemple. Vous devez conserver les données sensibles, telles qu’un certificat privé, à l’écart du contrôle de code source.

> [!NOTE]
> ASP.NET et ASP.NET Core sur Windows doivent accéder au magasin de certificats, même si vous chargez un certificat à partir d’un fichier. Pour charger un fichier de certificat dans une application Windows .NET, chargez le profil d’utilisateur actif au moyen de la commande suivante dans <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> :
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Cette approche de l'utilisation des certificats dans votre code utilise la fonctionnalité TLS d'App Service, qui requiert que votre application soit au niveau **De base** ou supérieur.

L’exemple C# suivant charge un certificat public depuis un chemin relatif dans votre application :

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Pour savoir comment charger un certificat TLS/SSL à partir d'un fichier dans Node.js, PHP, Python, Java ou Ruby, consultez la documentation relative à la plateforme web ou au langage correspondant.

## <a name="more-resources"></a>Plus de ressources

* [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](configure-ssl-bindings.md)
* [Appliquer le protocole HTTPS](configure-ssl-bindings.md#enforce-https)
* [Appliquer le protocole TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FORUM AUX QUESTIONS : App Service Certificates](./faq-configuration-and-management.md)