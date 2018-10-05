---
title: Utilisation des profils de version d’API avec le Kit de développement logiciel (SDK) .NET dans Azure Stack | Microsoft Docs
description: Apprenez-en davantage sur l’utilisation des profils de version d’API avec .NET dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184400"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Utiliser des profils de version d’API avec .NET dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Le Kit de développement logiciel (SDK) .NET pour Azure Stack Resource Manager fournit des outils pour vous aider à créer et gérer votre infrastructure. Le Kit de développement logiciel (SDK) comporte des fournisseurs de ressources de calcul, de réseau, de stockage, de services d’application et [KeyVault](../../key-vault/key-vault-whatis.md). Le Kit de développement logiciel (SDK) .NET inclut 14 packages NuGet qui doivent être téléchargés dans votre solution de projet chaque fois que les informations de profil sont incorporées. Toutefois, vous pouvez télécharger spécifiquement le fournisseur de ressources que vous utiliserez pour le profil 2018-03-01-hybrid ou 2017-03-09-profile afin d’optimiser la mémoire pour votre application. Chaque package se compose d’un fournisseur de ressources, de la version d’API correspondante et du profil d’API auquel il appartient. Les profils d’API dans le Kit de développement logiciel (SDK) .NET permettent le développement du cloud hybride en vous offrant la possibilité de basculer entre les ressources Azure globales et les ressources sur Azure Stack.

## <a name="net-and-api-version-profiles"></a>.NET et les profils de version d’API

Un profil d’API est une combinaison de fournisseurs de ressources et de versions d’API. Vous pouvez utiliser un profil d’API pour obtenir la version la plus récente et la plus stable de chaque type de ressource dans un package de fournisseur de ressources.

-   Pour utiliser les versions les plus récentes de tous les services, utilisez le profil **La plus récente** des packages. Ce profil fait partie du package NuGet **Microsoft.Azure.Management**.

-   Pour utiliser les services compatibles avec Azure Stack, utilisez le package **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** ou **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**.

    -   Il existe deux packages pour chaque fournisseur de ressources pour chaque profil.

    -   Vérifiez que la partie **ResourceProvider** du package NuGet ci-dessus est remplacée par le fournisseur approprié.

-   Pour utiliser la dernière version d’API d’un service, utilisez le profil **La plus récente** du package NuGet spécifique. Par exemple, si vous souhaitez utiliser la **dernière version d’API** du service de calcul autonome, utilisez le profil **La plus récente** du package de **calcul**. Le profil **La plus récente** fait partie du package NuGet **Microsoft.Azure.Management**.

-   Pour utiliser des versions d’API spécifiques pour un type de ressource dans un fournisseur de ressources donné, utilisez les versions d’API spécifiques définies dans le package.

Notez que vous pouvez combiner toutes les options dans la même application.

## <a name="install-the-azure-net-sdk"></a>Installer le Kit de développement logiciel (SDK) Azure .NET

1.  Installez Git. Pour obtenir des instructions, consultez [Démarrage rapide - Installation de Git][].

2.  Pour installer les packages NuGet corrects, consultez la rubrique [Finding and installing a package][] (Recherche et installation d’un package).

3.  Les packages à installer dépendent de la version de profil que vous souhaitez utiliser. Les noms de package pour les versions de profil sont les suivants :

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**

4.  Pour installer les packages NuGet corrects pour Visual Studio Code, consultez le lien suivant afin de télécharger les [instructions du gestionnaire de package NuGet][].

5.  Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour obtenir des instructions pour créer un abonnement, consultez [Créer des abonnements pour des offres dans Azure Stack][].

6.  Créez un principal de service et enregistrez l’ID client, ainsi que la clé secrète client. Pour plus d’informations sur la création d’un principal de service pour Azure Stack, consultez l’article [Fournir l’accès des applications à Azure Stack][]. Notez que l’ID client est également connu en tant qu’ID d’application lors de la création d’un principal de service.

7.  Vérifiez que votre principal de service bénéficie du rôle contributeur/propriétaire sur votre abonnement. Pour plus d’informations sur l’assignation d’un rôle au principal de service, consultez l’article [Fournir l’accès des applications à Azure Stack][].

## <a name="prerequisites"></a>Prérequis

Pour utiliser le Kit de développement logiciel (SDK) .NET Azure avec Azure Stack, vous devez fournir les valeurs ci-après, puis définir ces valeurs avec des variables d’environnement. Pour définir les variables d’environnement, consultez les instructions fournies sous le tableau qui correspondent à votre système d’exploitation.

| Valeur                     | Variables d’environnement   | Description                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID client                 | AZURE_TENANT_ID       | La valeur de votre [*ID de locataire*][] Azure Stack.                                                                          |
| ID client                 | AZURE_CLIENT_ID       | L’ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article. |
| Identifiant d’abonnement           | AZURE_SUBSCRIPTION_ID | [*L’ID d’abonnement*][] correspond à la façon dont vous accédez à des offres dans Azure Stack.                                                      |
| Clé secrète client             | AZURE_CLIENT_SECRET   | Le secret d’application du principal de service enregistré lors de la création du principal de service.                                      |
| Point de terminaison Resource Manager | ARM_ENDPOINT           | Consultez [*Point de terminaison Azure Stack Resource Manager*][].                                                                    |

Pour rechercher l’ID de locataire de votre environnement Azure Stack, suivez les instructions fournies [ici](../azure-stack-csp-ref-operations.md). Pour définir vos variables d’environnement, procédez comme suit :

### <a name="microsoft-windows"></a>Microsoft Windows

Pour définir les variables d’environnement dans une invite de commandes Windows, utilisez le format suivant :

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>Systèmes macOS, Linux et Unix

Dans les systèmes Unix, vous pouvez utiliser la commande suivante :

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Point de terminaison Azure Stack Resource Manager

Microsoft Azure Resource Manager est une infrastructure de gestion qui permet aux administrateurs de déployer, gérer et surveiller les ressources Azure. Azure Resource Manager peut gérer ces tâches en groupe, plutôt qu’individuellement, dans une seule opération.

Vous pouvez obtenir les informations de métadonnées du point de terminaison Resource Manager. Le point de terminaison renvoie un fichier JSON avec les informations requises pour exécuter votre code.

Tenez compte des points suivants :

- Le **ResourceManagerUrl** dans le Kit de développement Azure Stack (ASDK) est : https://management.local.azurestack.external/

- L’URL **ResourceManagerUrl** dans les systèmes intégrés est : `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Pour extraire les métadonnées requises : `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Exemple de fichier JSON :

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Profils d’API existants

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg** : profil le plus récent généré pour Azure Stack. Utilisez ce profil pour que les services soient davantage compatibles avec Azure Stack, à condition que vous utilisiez le tampon 1808 ou un tampon ultérieur.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg** : si vous utilisez un tampon antérieur à la version 1808, utilisez ce profil.

3.  **La plus récente** : profil constitué des dernières versions de l’ensemble des services. Utilisez les dernières versions de tous les services. Ce profil fait partie du package NuGet **Microsoft.Azure.Management**.

Pour plus d’informations sur les profils d’API et Azure Stack, consultez la section [Résumé des profils d’API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Utilisation du profil d’API du Kit de développement logiciel (SDK) Azure .NET

Le code suivant doit être utilisé pour instancier un client de profil. Ce paramètre est uniquement requis pour Azure Stack ou d’autres clouds privés. Azure global a déjà ces paramètres par défaut.

Le code ci-après est requis pour authentifier le principal de service sur Azure Stack. Il crée un jeton spécifique à Azure Stack à partir de l’ID de locataire et de la base d’authentification.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Ceci vous permet d’utiliser les packages NuGet de profils d’API pour déployer correctement votre application sur Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Définir des fonctions de définition d’environnement Azure Stack

Pour authentifier le principal de service auprès de l’environnement Azure Stack, utilisez le code suivant :

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Ceci remplace le client d’initialisation de service à authentifier auprès d’Azure Stack.

## <a name="samples-using-api-profiles"></a>Exemples à l’aide de profils d’API

Vous pouvez utiliser les exemples suivants se trouvant dans les référentiels GitHub en tant que référence de création de solutions avec des profils d’API .NET et Azure Stack.

-   [Projet de test pour une machine virtuelle, un réseau virtuel, des groupes de ressources et un compte de stockage][]
-   Gérer des machines virtuelles avec .NET

### <a name="sample-unit-test-project"></a>Exemple de projet de test unitaire 

1.  Clonez le référentiel à l’aide de la commande suivante :

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  Créez un principal de service Azure et assignez un rôle pour accéder à l’abonnement. Pour obtenir des instructions sur la création d’un principal de service, consultez [Utiliser Azure PowerShell pour créer un principal de service avec un certificat][].

3.  Récupérez les valeurs requises suivantes :

    1.  ID client
    2.  ID client
    3.  Clé secrète client
    4.  Identifiant d’abonnement
    5.  Point de terminaison Resource Manager

4.  Définissez les variables d’environnement ci-après en utilisant les informations que vous avez récupérées à partir du principal de service que vous avez créé à l’aide de l’invite de commandes :

    1.  export AZURE_TENANT_ID={votre id de locataire}
    2.  export AZURE_CLIENT_ID={votre id de client}
    3.  export AZURE_CLIENT_SECRET={votre secret de client}
    4.  export AZURE_SUBSCRIPTION_ID={votre id d’abonnement}
    5.  export ARM_ENDPOINT={votre URL Azure Stack Resource Manager}

   Sous Windows, utilisez **set** et non **export**.

5.  Assurez-vous que la variable d’emplacement est définie sur votre emplacement Azure Stack. Par exemple, LOCAL = « local ».

6.  Définissez les informations d’identification de connexion personnalisées vous permettant de vous authentifier auprès d’Azure Stack. Notez que cette partie du code est incluse dans le dossier Authorization dans cet exemple.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Ajoutez le code suivant si vous utilisez Azure Stack pour remplacer le client d’initialisation de service pour l’authentification auprès d’Azure Stack. Notez qu’une partie du code est déjà incluse dans le dossier Authorization dans cet exemple.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  À l’aide du gestionnaire de package NuGet, recherchez le profil « 2018-03-01-hybrid » et installez les packages associés à ce profil pour les fournisseurs de ressources de calcul, de réseau, de stockage, KeyVault et App Services.

2.  Dans chaque tâche du fichier .cs, définissez les paramètres nécessaires à l’utilisation d’Azure Stack. Voici un exemple pour la tâche `CreateResourceGroupTest` :

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Cliquez avec le bouton droit sur chaque tâche, puis sélectionnez **Exécuter le test**.

    1.  Les coches vertes dans la fenêtre du volet latéral indiquent que chaque tâche a été créée correctement selon les paramètres fournis. Vérifiez votre abonnement Azure Stack pour vous assurer que les ressources ont été créées correctement.

    2.  Pour plus d’informations sur l’exécution de tests unitaires, consultez l’article [Exécuter des tests unitaires avec l’Explorateur de tests.][]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les profils d’API, consultez les articles suivants :

- [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md)
- [Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack](azure-stack-profiles-azure-resource-manager-versions.md)

  [Démarrage rapide - Installation de Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Finding and installing a package]: /nuget/tools/package-manager-ui (Recherche et installation d’un package)
  [Instructions du gestionnaire de package NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Créer des abonnements pour des offres dans Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md
  [Fournir l’accès des applications à Azure Stack]: ../azure-stack-create-service-principals.md
  [*ID de locataire*]: ../azure-stack-identity-overview.md
  [*ID d’abonnement*]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [*Point de terminaison Azure Stack Resource Manager*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Résumé des profils d’API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Projet de test pour une machine virtuelle, un réseau virtuel, des groupes de ressources et un compte de stockage]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Utiliser Azure PowerShell pour créer un principal de service avec un certificat]: ../azure-stack-create-service-principals.md
  [Exécuter des tests unitaires avec l’Explorateur de tests.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
