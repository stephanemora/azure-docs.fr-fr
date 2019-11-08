---
title: Utiliser un certificat SSL dans le code d’application – Azure App Service | Microsoft Docs
description: Découvrez comment utiliser des certificats clients pour vous connecter aux ressources distantes qui en ont besoin.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510546"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Utiliser un certificat SSL dans votre code d’application dans Azure App Service

Votre code d’application App Service peut agir en tant que client et accéder à un service externe qui requiert une authentification par certificat. Ce guide pratique explique comment utiliser des certificats publics ou privés dans le code de votre application.

Cette approche de l’utilisation de certificats dans votre code utilisant la fonctionnalité SSL dans App Service, votre application doit être au niveau **De base** ou à un niveau supérieur. Vous pouvez aussi [inclure le fichier de certificat dans le référentiel de votre application](#load-certificate-from-file), mais cette pratique n’est pas recommandée pour des certificats privés.

Lorsque vous laissez App Service gérer vos certificats SSL, vous pouvez conserver les certificats et votre code d’application séparément et protéger vos données sensibles.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes de ce guide pratique, vous devez au préalable :

- [Création d’une application App Service](/azure/app-service/)
- [Ajouter un certificat à votre application](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trouver l’empreinte

Dans le <a href="https://portal.azure.com" target="_blank">Portail Azure</a>, dans le menu de gauche, sélectionnez **App Services** >  **\<app-name>** .

Dans la barre de navigation gauche de votre application, sélectionnez **Paramètres TLS/SSL**, puis **Certificats de clé privée (.pfx)** ou **Certificats de clé publique (.cer)** .

Trouvez le certificat que vous souhaitez utiliser et copiez-en l’empreinte.

![Copier l’empreinte du certificat](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Charger le certificat

Pour utiliser un certificat dans le code de votre application, ajoutez son empreinte au paramètre d’application `WEBSITE_LOAD_CERTIFICATES` en exécutant la commande suivante dans <a target="_blank" href="https://shell.azure.com" > Cloud Shell </a> :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pour rendre tous vos certificats accessibles, définissez la valeur sur `*`.

> [!NOTE]
> Ce paramètre place les certificats spécifiés dans le magasin [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) pour la plupart des niveaux de tarification. Cependant, pour le niveau **Isolé** (où l’application s’exécute dans un [App Service Environment](environment/intro.md)), il place les certificats dans le magasin[Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).
>

Les certificats configurés sont maintenant prêts à être utilisés par votre code.

## <a name="load-the-certificate-in-code"></a>Charger le certificat dans le code

Une fois que votre certificat est accessible, vous y accéder dans le code C# via l’empreinte du certificat. Le code suivant charge un certificat avec l’empreinte `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Charger un certificat à partir d’un fichier

Si vous devez charger un fichier de certificat à partir du répertoire de votre application, il est préférable de le charger en utilisant, par exemple, [FTPS](deploy-ftp.md) plutôt que [Git](deploy-local-git.md). Vous devez conserver les données sensibles, telles qu’un certificat privé, à l’écart du contrôle de code source.

Même si vous chargez le fichier directement dans votre code .NET, la bibliothèque vérifie si le profil de l’utilisateur actuel est chargé. Pour charger le profil de l’utilisateur actuel, définissez le paramètre d’application `WEBSITE_LOAD_USER_PROFILE` à l’aide de la commande suivante dans le <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Une fois ce paramètre défini, l’exemple C# suivant charge un certificat nommé `mycert.pfx` à partir du répertoire `certs` du référentiel de votre application.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>Autres ressources

* [Sécuriser un nom DNS personnalisé avec une liaison SSL](configure-ssl-bindings.md)
* [Appliquer le protocole HTTPS](configure-ssl-bindings.md#enforce-https)
* [Appliquer le protocole TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FORUM AUX QUESTIONS : App Service Certificates](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
