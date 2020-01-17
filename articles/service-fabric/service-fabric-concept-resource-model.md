---
title: Modèle de ressource d’application Azure Service Fabric
description: Cet article fournit une vue d’ensemble de la gestion d’une application Service Fabric Azure avec Azure Resource Manager
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: b3cf0b8f21565a8d51b16ff6c8b4c52bbfe8edc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464808"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Qu’est-ce qu’un modèle de ressource d’application Service Fabric ?
Le déploiement d’applications Service Fabric dans votre cluster Service Fabric par le biais d’Azure Resource Manager est recommandé. Cette méthode permet de décrire les applications et les services au format JSON et de les déployer dans le même modèle Resource Manager que votre cluster. Au lieu de déployer et de gérer des applications via PowerShell ou Azure CLI, il n’est pas nécessaire d’attendre que le cluster soit prêt. Le processus d’inscription, d’approvisionnement et de déploiement d’applications peut s’effectuer en une seule étape. Il est recommandé de gérer le cycle de vie des applications dans votre cluster. Pour plus d’informations, consultez les [meilleures pratiques](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources).

Le cas échéant, gérez vos applications en tant que ressources Resource Manager pour améliorer les scénarios suivants :
* Piste d’audit : Resource Manager audite chaque opération et conserve un *journal d’activité* détaillé qui peut vous aider à suivre les modifications apportées à ces applications et à votre cluster.
* Contrôle d’accès en fonction du rôle : la gestion de l’accès aux clusters, ainsi qu’aux applications déployées sur le cluster, peut être effectuée par le biais du même modèle Resource Manager.
* Azure Resource Manager (par le biais du Portail Azure) devient un guichet unique pour la gestion de votre cluster et des déploiements d’applications critiques.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Cycle de vie des applications Service Fabric avec Azure Resource Manager 
Dans ce document, découvrez comment :

> [!div class="checklist"]
> * déployer des ressources d’application à l’aide d’Azure Resource Manager 
> * mettre à niveau des ressources d’application à l’aide d’Azure Resource Manager
> * Supprimer des ressources d’application

## <a name="deploy-application-resources-using-azure-resource-manager"></a>déployer des ressources d’application à l’aide d’Azure Resource Manager  
Pour déployer une application et ses services à l’aide du modèle de ressources d’application Azure Resource Manager, vous devez empaqueter le code de l’application, télécharger le package, puis référencer l’emplacement du package dans un modèle Azure Resource Manager en tant que ressource d’application. Pour plus d’informations, consultez [Empaqueter une application](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).
          
Ensuite, créez un modèle Azure Resource Manager, mettez à jour le fichier de paramètres avec les détails de l’application et déployez-le sur le cluster Service Fabric. Consultez les exemples [ici](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM).

### <a name="create-a-storage-account"></a>Créer un compte de stockage 
Le déploiement d’une application à partir d’un modèle Resource Manager nécessite un compte de stockage pour l’installation de l’image de l’application. Vous pouvez réutiliser un compte de stockage existant ou créer un nouveau compte de stockage pour indexer vos applications. Vous pouvez ignorer cette étape si vous envisagez d’utiliser un compte de stockage existant. 

![Créez un compte de stockage.][CreateStorageAccount]

### <a name="configure-storage-account"></a>Configurer un compte de stockage 
Une fois le compte de stockage créé, vous devez créer un conteneur blob dans lequel les applications peuvent être indexées. Dans le Portail Azure, accédez au compte de stockage pour stocker vos applications. Sélectionnez le panneau **Blobs**, puis cliquez sur le bouton **Ajouter un conteneur**. Les ressources de votre cluster peuvent être sécurisées en définissant le niveau d’accès public sur privé. L’accès peut être accordé de plusieurs façons :
* [Autoriser l’accès aux blobs et aux files d’attente avec Azure Active Directory](../storage/common/storage-auth-aad-app.md)
* [Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC dans le Portail Azure](../storage/common/storage-auth-aad-rbac-portal.md)
* [Déléguer l’accès avec une signature d’accès partagé (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature
)

 Pour cet exemple, nous allons continuer à utiliser l’accès en lecture anonyme pour les blobs.

![Créer un blob][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Indexer une application dans un compte de stockage
Pour que l’application puisse être déployée, elle doit être indexée dans le stockage Blob. Dans ce didacticiel, vous allez créer le package d’application manuellement, cependant, cette étape peut être automatisée.  Pour plus d’informations, consultez [Empaqueter une application](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). Dans les étapes suivantes, l’[exemple d'application de vote](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) sera utilisé.

1. Dans Visual Studio, cliquez avec le bouton de droite sur le projet Vote, puis sélectionnez le package.   
![Empaqueter une application][PackageApplication]  
2. Ouvrez le répertoire **.\service-fabric-dotnet-quickstart\Voting\pkg\Debug** qui vient d’être créé, puis compressez le contenu dans un fichier appelé **Voting.zip** de telle sorte que fichier ApplicationManifest.xml se trouve à la racine du fichier zip.  
![Application zip][ZipApplication]  
3. Renommez l’extension du fichier . zip par **.sfpkg**.
4. Dans le Portail Azure, dans le conteneur **applications** de votre compte de stockage, cliquez sur **Chargement** pour charger **Voting.sfpkg**.  
![Chargement du package de l'application][UploadAppPkg]

L’application est maintenant indexée. Vous êtes à présent prêt pour créer le modèle Azure Resource Manager pour déployer l’application.      
   
### <a name="create-the-azure-resource-manager-template"></a>Créer le modèle Azure Resource Manager
L’exemple d’application contient des [Modèles Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) qui peuvent être utilisés pour déployer l’application. Les modèles de fichiers sont nommés **UserApp.json** et **UserApp.Parameters.json**. 

> [!NOTE] 
> Le fichier **UserApp.Parameters.json** doit être mis à jour avec le nom de votre cluster.
>
>

| Paramètre              | Description                                 | Exemple                                                      | Commentaires                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Le nom du cluster de déploiement | sf-cluster123                                                |                                                              |
| application            | Nom de l’application                 | Vote                                                       |
| applicationTypeName    | Le nom du type de l’application           | VotingType                                                   | Doit correspondre à ce qui se trouve dans ApplicationManifest.xml                 |
| applicationTypeVersion | La version du type d’application         | 1.0.0                                                        | Doit correspondre à ce qui se trouve dans ApplicationManifest.xml                 |
| serviceName            | Le nom du service         | Voting~VotingWeb                                             | Doit être au format ApplicationName~ServiceType            |
| serviceTypeName        | Le nom du type de service                | VotingWeb                                                    | Doit correspondre à ce qui se trouve dans ServiceManifest.xml                 |
| appPackageUrl          | L’URL du stockage Blob de l’application     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | L’URL du package d’application dans le stockage Blob (la procédure de définition est décrite ci-dessous) |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Déployer l’application 
Pour déployer l’application, exécutez New-AzResourceGroupDeployment pour effectuer le déploiement sur le groupe de ressources qui contient votre cluster.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Mettre à niveau l’application Service Fabric avec Azure Resource Manager
Les applications déjà déployées sur un cluster Service Fabric seront mises à niveau pour les raisons suivantes :

1. Un nouveau service est ajouté à l’application. Une définition de service doit être ajoutée au fichier service-manifest.xml et application-manifest.xml. Ensuite, pour refléter la nouvelle version de l’application, vous devez mettre à jour la version du type d’application de 1.0.0 à 1.0.1 [UserApp.parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json).

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. Une nouvelle version d’un service existant est ajoutée à l’application. Cela implique des modifications du code de l’application et des mises à jour de la version et du nom du type d’application.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Supprimer des ressources d’application
Les applications déployées à l’aide du modèle de ressource d’application dans Azure Resource Manager peuvent être supprimées du cluster à l’aide des étapes ci-dessous

1) Obtenir l’ID de ressource pour l’application à l’aide de la commande [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)  

    #### <a name="get-resource-id-for-application"></a>Obtenir l’ID de ressource pour l’application
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) Supprimer les ressources d’application à l’aide de [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)  

    #### <a name="delete-application-resource-using-its-resource-id"></a>Supprimer la ressource d’application à l’aide de son ID de ressource
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Étapes suivantes
Obtenir des informations sur le modèle de ressource de l’application :

* [Modéliser une application dans Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Manifestes des services et applications Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Voir aussi
* [Bonnes pratiques](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Gérer des applications et services en tant que ressources Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
