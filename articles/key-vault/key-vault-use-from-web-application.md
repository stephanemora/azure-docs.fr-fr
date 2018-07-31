---
title: 'Tutoriel : Utilisation d’Azure Key Vault à partir d’une application web | Microsoft Docs'
description: Utilisez ce tutoriel pour découvrir comment utiliser Azure Key Vault à partir d'une application web.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: barclayn
ms.openlocfilehash: ff59e39e54433aa673b093e2ee1fbe8c74010e54
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171321"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Tutoriel : Utilisation d'Azure Key Vault à partir d'une application web

Utilisez ce tutoriel pour découvrir comment utiliser Azure Key Vault à partir d'une application web. Il illustre le processus d’accès à une clé secrète à partir d’Azure Key Vault pour une utilisation dans une application web. Le tutoriel s’appuie sur le processus et utilise un certificat au lieu d’une clé secrète client. Ce tutoriel est conçu pour les développeurs web qui comprennent les principes fondamentaux de création d'applications web sur Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes : 

> [!div class="checklist"]
> * Ajouter des paramètres d’application pour le fichier web.config
> * Ajouter une méthode pour obtenir un jeton d'accès
> * Récupérer le jeton dans Application Start
> * S’authentifier avec un certificat

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin des éléments suivants :

* un URI pour une clé secrète dans Azure Key Vault ;
* un ID client et une clé secrète client pour une application web enregistrés avec Azure Active Directory qui a accès à votre Key Vault ;
* une application web. Ce tutoriel affiche les étapes d'une application ASP.NET MVC déployée dans Azure en tant qu'application web.

Suivez les étapes de [Prise en main d'Azure Key Vault](key-vault-get-started.md) pour obtenir l’URI pour une clé secrète, un ID client, une clé secrète client et enregistrer l’application. L’application web accédera au coffre et doit être enregistrée dans Azure Active Directory. Elle doit également disposer de droits d’accès à Key Vault. Si cela n'est pas le cas, revenez à Enregistrer une application dans le tutoriel de prise en main et répétez les étapes répertoriées. Pour plus d'informations sur la création d’Azure Web Apps, consultez [Vue d'ensemble de Web Apps](../app-service/app-service-web-overview.md).

Cet exemple est basé sur l’approvisionnement manuel des identités Azure Active Directory. Mais vous devez utiliser l’authentification [Managed Service Identity (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview). Les fichiers MSI permettent d’approvisionner automatiquement les identités Azure AD. Pour plus d’informations, consultez l’exemple sur [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) et le [tutoriel MSI avec App Service et Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). Vous pouvez également consulter le [didacticiel MSI](tutorial-web-application-keyvault.md) propre à Key Vault.


## <a id="packages"></a>Ajouter des packages NuGet

Deux packages doivent être installés pour votre application web.

* Bibliothèque d'authentification Active Directory : contient des méthodes pour interagir avec Azure Active Directory et gérer l'identité de l'utilisateur
* Bibliothèque Azure Key Vault : contient des méthodes pour interagir avec Azure Key Vault

Ces deux packages peuvent être installés à l’aide de la console du Gestionnaire de Package en utilisant la commande Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Modifier le fichier web.config

Il existe trois paramètres d'application qui doivent être ajoutés au fichier web.config comme suit. Nous ajouterons les valeurs réelles sur le Portail Azure pour renforcer le niveau de sécurité.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Ajouter une méthode pour obtenir un jeton d'accès

Pour utiliser l'API Key Vault, vous avez besoin d'un jeton d'accès. Le client Key Vault gère les appels de l'API Key Vault, mais vous devez lui fournir une fonction qui lui fait obtenir le jeton d'accès. Voici l’exemple de code pour obtenir un jeton d'accès à partir d’Azure Active Directory. Ce code peut être placé n'importe où dans votre application. Vous pouvez par exemple ajouter une classe Utils ou EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Récupérer la clé secrète dans Application Start

Nous avons maintenant besoin du code pour appeler l'API Key Vault et récupérer la clé secrète. Le code suivant peut être placé n'importe où tant qu'il est appelé avant que vous ne deviez l'utiliser. Ce code a été créé dans l'événement Application Start dans Global.asax afin qu'il s'exécute au démarrage et rende la clé secrète disponible à l'application.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Ajouter des paramètres d’application sur le Portail Azure

Si vous disposez d’une application web Azure, vous pouvez maintenant ajouter les valeurs réelles d’AppSettings sur le Portail Azure. Ce faisant, les valeurs réelles ne seront pas dans le fichier Web.config, mais protégés via le portail où vous avez des fonctionnalités de contrôle d'accès distinctes. Ces valeurs seront remplacées par les valeurs que vous avez entrées dans votre fichier web.config. Assurez-vous que les noms sont identiques.

![Paramètres d’application affichés sur le Portail Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Authentification avec un certificat et non une clé secrète client

Maintenant que vous comprenez l’authentification d’une application Azure AD à l’aide de l’ID client et de la clé secrète client, nous allons utiliser un ID client et un certificat. Voici les étapes pour utiliser un certificat dans Azure Web App :

1. obtenir ou créer un certificat ;
2. associer le certificat à une application AzureAD ;
3. ajouter du code à votre application web pour utiliser le certificat ;
4. ajouter un certificat à votre application web.

### <a name="get-or-create-a-certificate"></a>obtenir ou créer un certificat ;

 Nous allons rendre un certificat de test pour ce tutoriel. Voici un script pour créer un certificat auto-signé. Accédez au répertoire dans lequel vous souhaitez créer les fichiers de certificat.  En outre, pour les dates de début et de fin du certificat, utilisez la date actuelle plus un an.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = "c:\data\KVWebApp.pfx" 
$CerFilePath = "c:\data\KVWebApp.cer" 
$DNSName = "MyComputer.Contoso.com" 
$Password ="MyPassword" 
$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\LocalMachine\My" -NotBefore 05/15/2018 -NotAfter 05/15/2019 
Export-PfxCertificate -cert $cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $cert -FilePath $CerFilePath 
```

Prenez note de la date de fin et du mot de passe pour le fichier.pfx (dans cet exemple : 15 mai 2019 et MyPassword). Vous en aurez besoin pour le script ci-dessous. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Associer le certificat à une application AzureAD

Maintenant que vous disposez d’un certificat, vous devez l’associer à une application Azure AD. L’association peut être effectuée via PowerShell. Exécutez les commandes suivantes pour associer le certificat à l’application Azure AD :

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Après avoir exécuté ces commandes, vous pouvez voir l'application dans Azure AD. Lorsque vous recherchez les enregistrements d’application, veillez à sélectionner **Mes applications** au lieu de « Toutes les applications » dans la boîte de dialogue de recherche. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Ajouter du code à votre application web pour utiliser le certificat

Nous allons maintenant ajouter du code à votre application web pour accéder au certificat et l’utiliser pour l’authentification. 

Tout d'abord vient le code d'accès au certificat. Notez que l'emplacement StoreLocation est CurrentUser, et non LocalMachine. Et que nous choisissons « false » pour la méthode Find, car nous utilisons un certificat de test.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



Vient ensuite le code qui utilise le CertificateHelper et crée un ClientAssertionCertificate, nécessaire pour l'authentification.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Voici le nouveau code pour obtenir le jeton d'accès. Il remplace la méthode GetToken de l’exemple précédent. Elle a un nom différent pour des raisons pratiques. J'ai inséré tout ce code dans la classe Utils de mon projet d’application web pour plus de facilité d'utilisation.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



La dernière modification de code a lieu dans la méthode Application_Start. Nous devons tout d'abord appeler la méthode GetCert() pour charger le ClientAssertionCertificate. Nous modifions ensuite la méthode de rappel que nous transmettons lors de la création d'un nouveau KeyVaultClient. Ce code remplace le code de l’exemple précédent.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Ajouter un certificat à une application web par le biais du Portail Azure

L’ajout d’un certificat à une application web est un processus simple qui comporte deux étapes. Tout d’abord, connectez-vous au Portail Azure et accédez à votre application web. Sur le volet Paramètres de votre application Web, cliquez sur l'entrée **Paramètres SSL**. Une fois ouvert, téléchargez le certificat que vous avez créé dans l’exemple précédent, KVWebApp.pfx. Assurez-vous que vous n’oubliez pas le mot de passe pour le fichier pfx.

![Ajouter un certificat à une application web sur le Portail Azure][2]

La dernière chose que vous devez faire consiste à ajouter un paramètre d’application à votre application web, nommé WEBSITE\_LOAD\_CERTIFICATES et avec la valeur *. Cette étape garantit que tous les certificats sont chargés. Si vous souhaitez charger uniquement les certificats que vous avez téléchargés, vous pouvez entrer une liste séparée par des virgules de leurs empreintes numériques.


## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez le service d’application, le coffre de clés et l’application Azure AD que vous avez utilisé pour le tutoriel.  


## <a id="next"></a>Étapes suivantes
> [!div class="nextstepaction"]
>[Informations de référence sur l’API Azure Key Vault Management](/dotnet/api/overview/azure/keyvault/management).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 