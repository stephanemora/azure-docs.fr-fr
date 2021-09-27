---
title: Déployer une application sur un cluster managé avec Azure Resource Manager
description: Découvrez comment déployer, mettre à niveau ou supprimer une application Service Fabric sur un cluster géré par Azure Service Fabric à l'aide d'Azure Resource Manager.
ms.topic: how-to
ms.date: 8/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e981e58cd5efb1430ab35772ab84428f7482977
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434517"
---
# <a name="manage-application-lifecycle-on-a-managed-cluster-using-azure-resource-manager"></a>Gérer le cycle de vie des applications sur un cluster géré à l’aide de Azure Resource Manager

Vous avez plusieurs options pour déployer des applications Azure Service Fabric sur votre cluster managé Service Fabric. Nous vous recommandons d’utiliser Azure Resource Manager. Si vous utilisez Resource Manager, vous pouvez décrire les applications et les services au format JSON, puis les déployer dans le même modèle Resource Manager que votre cluster. Contrairement à l’utilisation de PowerShell ou d’Azure CLI pour déployer et gérer des applications, si vous utilisez Resource Manager, vous n’avez pas besoin d’attendre que le cluster soit prêt. L’inscription, le provisionnement et le déploiement d’applications peuvent tous être réalisés en une seule étape. L’utilisation de Resource Manager est la meilleure façon de gérer le cycle de vie des applications dans votre cluster. Pour plus d’informations, consultez [Meilleures pratiques : Infrastructure en tant que code](service-fabric-best-practices-infrastructure-as-code.md#service-fabric-resources).

La gestion de vos applications en tant que ressources dans Resource Manager peut vous aider à obtenir des améliorations dans les domaines suivants :

* Piste d’audit : Resource Manager effectue l’audit de chaque opération et conserve un journal d’activité détaillé. Un journal d’activité peut vous aider à suivre toutes les modifications apportées aux applications et à votre cluster.
* Contrôle d’accès en fonction du rôle : vous pouvez gérer l’accès aux clusters et aux applications déployées sur le cluster en utilisant le même modèle Resource Manager.
* Efficacité de la gestion : Resource Manager vous fournit un emplacement unique (le portail Azure) pour la gestion de votre cluster et des déploiements d’applications critiques.

Dans ce document, découvrez comment :

> [!div class="checklist"]
>
> * Déployer des ressources d’application à l’aide de Resource Manager.
> * Mettez à niveau les ressources des applications de la structure de service en utilisant le Resource Manager.
> * Supprimez les ressources d'application du tissu de services.

## <a name="deploy-service-fabric-application-resources"></a>Déployer les ressources applicatives de Service Fabric

La procédure générale à suivre pour déployer une application et ses services à l’aide du modèle de ressource d’application Resource Manager est la suivante :
1. Empaquetez le code de l’application.
1. Chargez le package.
1. Référencez l’emplacement du package dans un modèle Resource Manager en tant que ressource d’application. 

Pour plus d’informations, consultez [Empaqueter une application](service-fabric-package-apps.md#create-an-sfpkg).

Ensuite, vous créez un modèle Resource Manager, vous mettez à jour le fichier de paramètres avec les détails de l’application et vous déployez le modèle sur le cluster Service Fabric. [Explorez des exemples](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster).

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Pour déployer une application à partir d’un modèle Resource Manager, vous devez disposer d’un compte de stockage. Le compte de stockage permet d’effectuer une copie intermédiaire de l’image d’application. 

Vous pouvez réutiliser un compte de stockage existant ou créer un nouveau compte de stockage pour la copie intermédiaire de vos applications. Si vous utilisez un compte de stockage existant, vous pouvez ignorer cette étape. 

![Créez un compte de stockage.][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Configurer votre compte de stockage

Après avoir créé le compte de stockage, vous créez un conteneur d’objets blob dans lequel vous pouvez effectuer une copie intermédiaire des applications. Dans le portail Azure, accédez au compte de Stockage Azure où vous souhaitez stocker vos applications. Sélectionnez **Objets blob** > **Ajouter un conteneur**. 

Les ressources de votre cluster peuvent être sécurisées en définissant le niveau d’accès public sur **privé**. Vous pouvez octroyer l’accès de plusieurs façons :

* Autorisez l’accès aux objets blob et aux files d’attente en utilisant [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Octroyez l’accès aux données d’objet blob et de file d’attente Azure en utilisant le [contrôle d’accès en fonction du rôle (RBAC) Azure dans le portail Azure](../storage/blobs/assign-azure-role-data-access.md).
* Déléguez l’accès en utilisant une [signature d’accès partagé](/rest/api/storageservices/delegate-access-with-shared-access-signature).

L’exemple illustré dans la capture d’écran suivante utilise un accès en lecture anonyme pour les objets blob.

![Créer un blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>Effectuer une copie intermédiaire de l’application dans votre compte de stockage

Avant de pouvoir déployer une application, vous devez effectuer une copie intermédiaire de l’application dans le stockage d’objets blob. Dans ce tutoriel, nous allons créer manuellement le package d’application. Gardez à l’esprit que cette étape peut être automatisée. Pour plus d’informations, consultez [Empaqueter une application](service-fabric-package-apps.md#create-an-sfpkg). 

Dans ce tutoriel, nous utilisons l’[exemple d’application de vote](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **Vote**, puis sélectionnez **Package**.

   ![Empaqueter une application][PackageApplication]  
1. Accédez au répertoire *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug*. Compressez le contenu dans un fichier nommé *Voting.zip*. Le fichier *ApplicationManifest.xml* doit se trouver à la racine dans le fichier zip.

   ![Application zip][ZipApplication]  
1. Renommez le fichier pour remplacer l’extension .zip par *.sfpkg*.

1. Dans le portail Azure, dans le conteneur **applications** de votre compte de stockage, sélectionnez **Charger**, puis chargez **Voting.sfpkg**. 

   ![Charger le package d’application][UploadAppPkg]

La copie intermédiaire de l’application est désormais réalisée et vous pouvez créer le modèle Resource Manager pour déployer l’application.

### <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager

L’exemple d’application contient des [modèles Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM-Managed-Cluster) que vous pouvez utiliser pour déployer l’application. Les noms des fichiers modèles sont *UserApp.json* et *UserApp.Parameters.json*.

> [!NOTE]
> Le fichier *UserApp.Parameters.json* doit être mis à jour avec le nom de votre cluster.
>
>

| Paramètre              | Description                                 | Exemple                                                      | Commentaires                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Le nom du cluster de déploiement | sf-cluster123                                                |                                                              |
| application            | Nom de l’application                 | Vote                                                       |
| version    | ID de ressource, type d’application et version de l’application.       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | Doit correspondre à ApplicationManifest.xml                 |
| serviceName            | Nom du service         | VotingWeb                                             | Doit être au format ServiceType            |
| serviceTypeName        | Le nom du type de service                | VotingWebType                                                    | Doit correspondre à ServiceManifest.xml                 |
| appPackageUrl          | L’URL du stockage Blob de l’application     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | L’URL du package d’application dans le stockage d’objets blob (la procédure de définition de l’URL est décrite ultérieurement dans cet article) |

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-service-fabric-application"></a>Déployer l’application Service Fabric

Exécutez l’applet de commande **New-AzResourceGroupDeployment** pour déployer l’application dans le groupe de ressources qui contient votre cluster :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>Mettre à niveau l’application Service Fabric à l’aide de Resource Manager

> [!IMPORTANT]
> Tout service déployé via le modèle Azure Resource Manager (ARM) doit être supprimé de la section DefaultServices du fichier ApplicationManifest.xml correspondant.


Vous pouvez mettre à niveau une application qui est déjà déployée sur un cluster Service Fabric pour l’une des raisons suivantes :

* Un nouveau service est ajouté à l’application. Une définition de service doit être ajoutée aux fichiers *service-manifest.xml* et *application-manifest.xml* quand un service est ajouté à l’application. Pour refléter une nouvelle version d’une application, vous devez également modifier la version du type d’application de 1.0.0 à 1.0.1 dans [UserApp.Parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM-Managed-Cluster/UserApp.Parameters.json) :

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* Une nouvelle version d’un service existant est ajoutée à l’application. Des exemples incluent des modifications du code de l’application et des mises à jour de la version et du nom du type d’application. Pour cette mise à niveau, mettez à jour UserApp.Parameters.json comme suit :

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-service-fabric-application-resources"></a>Supprimer les ressources de l'application Service Fabric
> [!NOTE]
> Les applications ne doivent pas être supprimées via le modèle Azure Resource Manager (ARM), car il n’existe pas de méthode déclarative pour nettoyer les ressources individuelles

Pour supprimer une application qui a été déployée à l’aide du modèle de ressources de l’application dans Resource Manager :

1. Utilisez l’applet de commande [Get-AzResource](/powershell/module/az.resources/get-azresource) pour obtenir l’ID de ressource de l’application :

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Supprimez les ressources d’application à l’aide de l’applet de commande [Remove-AzResource](/powershell/module/az.resources/remove-azresource):

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```


## <a name="migration-from-classic-to-managed-clusters"></a>Migration d’un cluster classique vers un cluster managé

Si vous effectuez la migration d’une ou plusieurs applications d’un cluster classique vers un cluster managé, vous devez vérifier que les types sont correctement spécifiés pour ne pas rencontrer d’erreurs. 

Les points suivants sont mentionnés en raison de leur fréquence d’utilisation, mais ils ne sont pas censés constituer une liste exhaustive de différences. 

* upgradeReplicaSetCheckTimeout est désormais un entier pour le SFRP managé, mais une chaîne dans le SFRP classique. 

Pour connaître la liste complète des propriétés et des types, consultez [Types de ressources des applications de clusters managés](/azure/templates/microsoft.servicefabric/managedclusters/applications?tabs=json).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le déploiement d’applications de cluster managé :

* [Déployer des secrets d’application de cluster managé](how-to-managed-cluster-application-secrets.md)
* [Déployer des applications de cluster managé avec une identité managée](how-to-managed-cluster-application-managed-identity.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
