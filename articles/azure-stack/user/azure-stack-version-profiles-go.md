---
title: Utilisation de profils de version des API avec Go dans Azure Stack | Microsoft Docs
description: En savoir plus sur l’utilisation de profils de version des API avec Go dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 30969dcb7549f4107eb72b54d444a639c35b7ba0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264780"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Utilisez des profils de version des API avec Go dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="go-and-version-profiles"></a>Go et les profils de version

Un profil est une combinaison de différents types de ressources dans différentes versions provenant de différents services. L’utilisation d’un profil vous permettra de combiner différents types de ressources. Les profils peuvent apporter :

 - La stabilité de votre application en verrouillant sur des versions d’API spécifiques.
 - La compatibilité de votre application avec Azure Stack et les centres de données régionaux Azure.

Dans le kit de développement logiciel (SDK) Go, les profils sont disponibles sous les profils/chemin d’accès, leur version étant au format **YYYY-MM-DD**. Actuellement, le dernier profil de version Azure Stack est **2017-03-09**. Pour importer un service donné depuis un profil, vous devez importer le module correspondant depuis le profil. Par exemple, pour importer le service **Compute** depuis le profil **2017-03-09** :

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Installer le Kit de développement logiciel Microsoft Azure SDK pour Go

  1. Installez Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Installez le [langage de programmation Go](https://golang.org/dl).  
  Les profils d’API pour Azure nécessitent Go version 1.9 ou une version ultérieure.
  3. Installez le Kit de développement logiciel (SDK) Azure et ses dépendances en exécutant la commande Bash suivante :
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>Le Kit de développement logiciel (SDK) Go

Vous trouverez plus d’informations sur le Kit de développement logiciel (SDK) Azure sur :
- Le Kit de développement logiciel (SDK) sur la page [Installation du Kit de développement logiciel Microsoft Azure SDK pour Go](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Le Kit de développement logiciel (SDK) Azure est publiquement disponible sur GitHub sur la page [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dépendances de Go-AutoRest

Le Kit de développement logiciel (SDK) Go dépend des modules Azure Go-AutoRest pour envoyer des requêtes REST aux points de terminaison Azure Resource Manager. Vous devez importer les dépendances du module à partir de Azure Go-AutoRest [Azure Go-AutoRest sur GitHub](https://github.com/Azure/go-autorest). Vous trouverez les commandes Bash d’installation dans la section **Install**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Comment utiliser les profils de Kit de développement logiciel (SDK) Go sur Azure Stack

Pour exécuter un exemple de code Go sur Azure Stack :
  1. Installez le Kit de développement logiciel (SDK) Azure pour Go et ses dépendances. Pour obtenir des instructions, consultez la section précédente, [Installer le Kit de développement logiciel Microsoft Azure SDK pour Go](#install-azure-sdk-for-go).
  2. Obtenir les informations de métadonnées du point de terminaison du Gestionnaire des ressources. Le point de terminaison retourne un fichier JSON avec les informations requises pour exécuter votre code Go.

  > [!Note]  
  > Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : `https://management.local.azurestack.external/`  
  > Le **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Pour extraire les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Exemple de fichier JSON :

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour plus d’informations sur la création d’un abonnement, consultez [Créer des abonnements pour des offres dans Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Créer un principal de service avec une étendue « Abonnement » et un rôle **propriétaire**. Enregistrez l’ID du principal du service et le secret. Pour plus d’informations sur la création d’un principal de service pour Azure Stack, consultez [Créer un principal du service](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Votre environnement Azure Stack est configuré.
  5. Importez un module de service depuis le profil de Kit de développement logiciel (SDK) Go dans votre code. La version actuelle du profil Azure Stack est **2017-03-09**. Par exemple, pour importer un module de réseau à partir d’un type de profil **2017-03-09** : 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Dans votre fonction, créez et authentifiez un client à l’aide de l’appel de fonction **Nouveau** client. Vous pouvez utiliser le code suivant pour créer un réseau virtuel client :  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Définissez `<baseURI>` la valeur **ResourceManagerUrl** utilisée à l’étape deux.
  Définissez `<subscriptionID>` la valeur **SubscriptionID** enregistrée lors de l’étape trois.
  Pour créer le jeton, consultez la section Authentification ci-dessous.  

  7. Appelez les méthodes de l’API à l’aide du client que vous avez créé à l’étape précédente. Par exemple, pour créer un réseau virtuel à l’aide du client créé à l’étape précédente : 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Pour un exemple complet de création d’un réseau virtuel sur Azure Stack à l’aide d’un profil de Kit de développement logiciel (SDK) Go, consultez [Exemple](#example).

## <a name="authentication"></a>Authentification

Pour obtenir la propriété d’agent d’autorisation d’Azure Active Directory à l’aide du Kit de développement logiciel (SDK) Go, installez les modules Go-AutoRest. Ces modules doivent être déjà installés à l’aide de l’installation de « Go SDK » ; dans le cas contraire, installez le [package d’authentification sur GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

L’agent d’autorisation doit être défini comme l’agent d’autorisation pour le client de la ressource. Il existe différentes méthodes pour obtenir un agent d’autorisation ; pour une liste complète, voir ici.

Cette section présente une méthode courante pour obtenir des jetons de l’agent d’autorisation sur Azure Stack en utilisant les informations d’identification du client :

  1. Si un principal de service disposant du rôle de propriétaire sur l’abonnement est disponible, ignorez cette étape. Sinon, créez un principal de service [instructions]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) et attribuer-lui un rôle de « propriétaire » s’étendant à votre abonnement [instructions]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Enregistrez l’ID d’application du principal du service et le secret. 

  2. Importez le package **adal** à partir de Go-AutoRest dans votre code. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Créez une oauthConfig à l’aide de la méthode NewOAuthConfig du module **adal**. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Définissez `<activeDirectoryEndpoint>` sur la valeur de propriété « loginEndpoint » à partir des métadonnées ResourceManagerUrl récupérées lors de la section précédente de ce document.
  Définissez la valeur `<tenantID>` sur votre ID de locataire Azure Stack. 

  4. Enfin, créez un jeton de principal de service en utilisant la méthode NewServicePrincipalToken du module adal. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Définissez `<activeDirectoryResourceID>` sur une des valeurs de la liste « audience » dans les métadonnées ResourceManagerUrl récupérées lors de la section précédente de ce document.  
  Définissez `<clientID>` sur l’ID d’application du principal du service enregistrée lors de la création du principal de service lors de la section précédente de ce document.  
  Définissez `<clientSecret>` sur le secret d’application du principal du service enregistré lors de la création du principal de service lors de la section précédente de ce document.  

## <a name="example"></a>Exemples

Cette section présente un exemple de code Go pour créer un réseau virtuel sur Azure Stack. Pour obtenir des exemples complets de Kit de développement logiciel (SDK) Go, consultez le [référentiel d’exemples de Kit de développement logiciel (SDK) Go Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Les exemples Azure Stack sont disponibles sous hybride/ chemin d’accès à l’intérieur des dossiers de service du référentiel.

> [!Note]  
> Pour exécuter le code dans cet exemple, vérifiez que le fournisseur de ressources **Réseau** de l’abonnement utilisé est répertorié comme **inscrit**. Pour le vérifier, recherchez l’abonnement dans le portail Azure Stack et cliquez sur **fournisseurs de ressources.**

1. Importez les packages requis dans votre code. Vous devez utiliser le dernier profil disponible sur Azure Stack pour importer le module de réseau. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Définissez les variables d’environnement. Pour créer un réseau virtuel, vous devez disposer d’un groupe de ressources. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Maintenant que vous avez défini vos variables d’environnement, ajoutez une méthode pour créer un jeton d’authentification à l’aide du package **adal**. Consultez les informations sur l’authentification dans la section précédente.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Ajoutez la méthode principale. La méthode principale obtient d’abord un jeton à l’aide de la méthode définie à l’étape précédente. Ensuite, elle crée un client à l’aide du module de réseau du profil. Enfin, elle crée un réseau virtuel. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>Étapes suivantes
* [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md)
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)  
