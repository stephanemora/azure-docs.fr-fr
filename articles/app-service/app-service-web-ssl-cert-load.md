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
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475097"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Utiliser un certificat SSL dans votre code d’application dans Azure App Service

Ce guide montre comment utiliser des certificats publics ou privés dans votre code d’application. Un exemple de cas d’utilisation est l’accès de votre application à un service externe qui nécessite une authentification de certificat.

Cette approche à l’aide de certificats dans votre code se sert de SSL dans App Service, qui exige que votre application se trouver dans des fonctionnalités **base** niveau ou version ultérieure. Vous pouvez également [inclure le fichier de certificat dans votre référentiel de l’application](#load-certificate-from-file), mais il n’est pas une pratique recommandée pour les certificats privés.

Lorsque vous laissez App Service gérer vos certificats SSL, vous pouvez conserver les certificats et votre code d’application séparément et protéger vos données sensibles.

## <a name="upload-a-private-certificate"></a>Téléchargement d’un certificat privé

Avant de charger un certificat privé, assurez-vous que [il satisfait à toutes les exigences](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), à ceci près qu’il n’a pas besoin être configuré pour l’authentification du serveur.

Lorsque vous êtes prêt à télécharger, exécutez la commande suivante le <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Copiez l’empreinte de certificat et consultez [rendent le certificat accessible](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Téléchargement d’un certificat public

Certificats publics sont pris en charge dans les *.cer* format. Pour télécharger un certificat public, le <a href="https://portal.azure.com" target="_blank">Azure portal</a>et accédez à votre application.

Cliquez sur **paramètres SSL** > **certificats publics (.cer)**  > **télécharger un certificat Public** dans le volet de navigation gauche de votre application.

Dans **nom**, tapez un nom pour le certificat. Dans **fichier de certificat CER**, sélectionnez votre fichier CER.

Cliquez sur **Télécharger**.

![Charger un certificat public](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Une fois le certificat téléchargé, copiez l’empreinte de certificat et consultez [rendent le certificat accessible](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importer un certificat App Service

Consultez [acheter et configurer un certificat SSL pour Azure App Service](web-sites-purchase-ssl-web-site.md).

Une fois que le certificat est importé, copiez l’empreinte de certificat et consultez [rendent le certificat accessible](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Vérifiez le certificat accessible

Pour utiliser un certificat importé ou chargé dans le code de votre application, vérifiez son empreinte numérique accessible avec la `WEBSITE_LOAD_CERTIFICATES` paramètre d’application, en exécutant la commande suivante dans le <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Pour rendre tous vos certificats accessible, définissez la valeur sur `*`.

> [!NOTE]
> Ce paramètre place les certificats spécifiés dans le [User\My actuel](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) store pour la plupart des niveaux de tarification, mais dans le **isolé** niveau (par exemple, l’application s’exécute dans un [environnement App Service](environment/intro.md)), il place les certificats dans le [Machine\My Local](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) stocker.
>

![Configurer les paramètres de l’application](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Lorsque vous avez terminé, cliquez sur **Enregistrer**.

Les certificats configurés êtes maintenant prêts à être utilisé par votre code.

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
## <a name="load-certificate-from-file"></a>Charger un certificat à partir du fichier

Si vous avez besoin charger un fichier de certificat à partir de votre répertoire de l’application, il est préférable de télécharger à l’aide [FTPS](deploy-ftp.md) au lieu de [Git](deploy-local-git.md), par exemple. Vous devez conserver les données sensibles, comme un certificat privé en dehors du contrôle de code source.

Même si vous chargez le fichier directement dans votre code .NET, la bibliothèque vérifie toujours si le profil utilisateur actuel est chargé. Pour charger le profil utilisateur actuel, définissez la `WEBSITE_LOAD_USER_PROFILE` paramètre d’application avec la commande suivante dans le <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Une fois que ce paramètre est défini, ce qui suit C# exemple montre comment charger un certificat nommé `mycert.pfx` à partir de la `certs` répertoire de référentiel de votre application.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
