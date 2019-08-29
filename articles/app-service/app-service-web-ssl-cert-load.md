---
title: Utiliser un certificat SSL dans le code d’application - Azure App Service | Microsoft Docs
description: Découvrez comment utiliser des certificats clients pour vous connecter aux ressources distantes qui en ont besoin.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066988"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Utiliser un certificat SSL dans votre code d’application dans Azure App Service

Ce guide pratique explique comment utiliser des certificats publics ou privés dans le code de votre application. Un exemple de cas d’utilisation est l’accès de votre application à un service externe qui nécessite une authentification de certificat.

Cette approche de l’utilisation de certificats dans votre code utilisant la fonctionnalité SSL dans App Service, votre application doit être au niveau **De base** ou à un niveau supérieur. Vous pouvez aussi [inclure le fichier de certificat dans le référentiel de votre application](#load-certificate-from-file), mais cette pratique n’est pas recommandée pour des certificats privés.

Lorsque vous laissez App Service gérer vos certificats SSL, vous pouvez conserver les certificats et votre code d’application séparément et protéger vos données sensibles.

## <a name="upload-a-private-certificate"></a>Téléchargement d’un certificat privé

Avant de charger un certificat privé, assurez-vous qu’il [satisfait à toutes les exigences](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), à l’exception de celle d’être configuré pour l’authentification du serveur.

Lorsque vous êtes prêt à charger le certificat, exécutez la commande suivante dans le <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Copiez l’empreinte du certificat, puis reportez-vous à la section [Rendre le certificat accessible](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Téléchargement d’un certificat public

Les certificats publics sont pris en charge au format *.cer*. Pour charger un certificat public, sur le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, accédez à votre application.

Cliquez sur **Paramètres SSL** > **Certificats publics (.cer)**  > **Charger un certificat public** dans le volet de navigation gauche de votre application.

Dans **Nom**, tapez un nom pour le certificat. Dans **Fichier de certificat CER**, sélectionnez votre fichier CER.

Cliquez sur **Télécharger**.

![Charger un certificat public](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Une fois le certificat chargé, copiez l’empreinte du certificat, puis reportez-vous à la section [Rendre le certificat accessible](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importer un App Service Certificate

Voir [Acheter et configurer un certificat SSL pour Azure App Service](web-sites-purchase-ssl-web-site.md).

Une fois le certificat importé, copiez l’empreinte du certificat, puis reportez-vous à la section [Rendre le certificat accessible](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Rendre le certificat accessible

Pour utiliser un certificat chargé ou importé dans le code de votre application, rendez son empreinte accessible à l’aide du paramètre d’application `WEBSITE_LOAD_CERTIFICATES` en exécutant la commande suivante dans le <a target="_blank" href="https://shell.azure.com" > Cloud Shell </a> :

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pour rendre tous vos certificats accessibles, définissez la valeur sur `*`.

> [!NOTE]
> Ce paramètre place les certificats spécifiés dans le magasin [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) pour la plupart des niveaux de tarification. Cependant, pour le niveau **Isolé** (où l’application s’exécute dans un [App Service Environment](environment/intro.md)), il place les certificats dans le magasin[Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).
>

![Configurer les paramètres de l’application](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Lorsque vous avez terminé, cliquez sur **Enregistrer**.

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
