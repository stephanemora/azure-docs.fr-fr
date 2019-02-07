---
title: Pilote de volume Azure Files pour Service Fabric (version préliminaire) | Microsoft Docs
description: Service Fabric prend en charge l’utilisation d’Azure Files pour sauvegarder des volumes à partir de votre conteneur. Actuellement en mode préliminaire.
services: service-fabric
documentationcenter: other
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: other
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/10/2018
ms.author: twhitney, subramar
ms.openlocfilehash: f2636720f6f1faeffb9a63052efdf009668d806f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752073"
---
# <a name="service-fabric-azure-files-volume-driver-preview"></a>Pilote de volume Azure Files pour Service Fabric (version préliminaire)
Le plug-in de volume Azure Files est un [plug-in de volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) qui fournit des volumes basés sur [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) pour les conteneurs Docker. Ce plug-in de volume Docker est fourni sous la forme d’une application Service Fabric qui peut être déployée sur des clusters Service Fabric. Il vise à fournir des volumes basés sur Azure Files aux autres applications de conteneur Service Fabric qui sont déployées sur le cluster.

> [!NOTE]
> La version 6.4.571.9494 du plug-in de volume Azure Files est une préversion qui est disponible avec ce document. En tant que version préliminaire, son utilisation dans les environnements de production n’est **pas** prise en charge.
>

## <a name="prerequisites"></a>Prérequis
* La version Windows du plug-in de volume Azure Files fonctionne sur [Windows Server version 1709](https://docs.microsoft.com/windows-server/get-started/whats-new-in-windows-server-1709), [Windows 10 version 1709](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1709) ou des systèmes d’exploitation plus récents uniquement. La version Linux du plug-in de volume Azure Files fonctionne sur toutes les versions de système d’exploitation prises en charge par Service Fabric.

* Le plug-in du volume Azure Files fonctionne uniquement sur Service Fabric version 6.2 ou version plus récente.

* Suivez les instructions de la [documentation Azure Files](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share) pour créer un partage de fichiers pour l’application de conteneur Service Fabric à utiliser en tant que volume.

* Vous devrez avoir installé [Powershell avec le module Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started) ou [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli).

* Si vous utilisez des conteneurs Hyper-V, les extraits de code suivants doivent être ajoutés dans ClusterManifest (cluster local), à la section fabricSettings dans votre modèle ARM (cluster Azure) ou dans le fichier ClusterConfig.json (cluster autonome). Vous avez besoin du nom de volume et du port que le volume écoute sur le cluster. 

Dans ClusterManifest, vous devez ajouter ce qui suit à la section Hosting. Dans cet exemple, le nom de volume est **sfazurefile** et le port qu’il écoute sur le cluster est **19300**.  

``` xml 
<Section Name="Hosting">
  <Parameter Name="VolumePluginPorts" Value="sfazurefile:19300" />
</Section>
```

Dans la section fabricSettings de votre modèle ARM (pour les déploiements Azure) ou le fichier ClusterConfig.json (pour les déploiements autonomes), l’extrait de code suivant doit être ajouté. 

```json
"fabricSettings": [
  {
    "name": "Hosting",
    "parameters": [
      {
          "name": "VolumePluginPorts",
          "value": "sfazurefile:19300"
      }
    ]
  }
]
```


## <a name="deploy-the-service-fabric-azure-files-application"></a>Déployer l’application Azure Files pour Service Fabric

L’application Service Fabric qui fournit les volumes pour vos conteneurs peut être téléchargée à partir du [lien](https://aka.ms/sfvolume6.4) suivant. L’application peut être déployée sur le cluster via [PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications), [CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle-sfctl) ou des [API FabricClient](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications-fabricclient).

1. À l’aide de la ligne de commande, basculez vers le répertoire racine du package d’application téléchargé.

    ```powershell
    cd .\AzureFilesVolume\
    ```

    ```bash
    cd ~/AzureFilesVolume
    ```

2. Copiez le package d’application sur le magasin d’images et exécutez la commande ci-dessous avec la valeur appropriée pour [ApplicationPackagePath] et [ImageStoreConnectionString] :

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath [ApplicationPackagePath] -ImageStoreConnectionString [ImageStoreConnectionString] -ApplicationPackagePathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl cluster select --endpoint https://testcluster.westus.cloudapp.azure.com:19080 --pem test.pem --no-verify
    sfctl application upload --path [ApplicationPackagePath] --show-progress
    ```

3. Enregistrer le type d’application

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore AzureFilesVolumePlugin
    ```

    ```bash
    sfctl application provision --application-type-build-path [ApplicationPackagePath]
    ```

4. Créer l’application Dans la commande pour créer l’application ci-dessous, notez le paramètre d’application **ListenPort**. Cette valeur spécifiée pour ce paramètre d’application est le port sur lequel le plug-in de volume Azure Files écoute les requêtes du démon Docker. Il est important de s’assurer que le port fourni pour l’application n’est pas en conflit avec un autre port utilisé par le cluster ou vos applications.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100'}
    ```

    ```bash
    sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort":"19100"}'
    ```

> [!NOTE]

> Windows Server 2016 Datacenter ne prend pas en charge le mappage de montages SMB à des conteneurs ([cette fonctionnalité est uniquement prise en charge sur Windows Server version 1709](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/container-storage)). Cette contrainte empêche le mappage de volume réseau et les pilotes de volume Azure Files sur les versions antérieures à 1709.
>   

### <a name="deploy-the-application-on-a-local-development-cluster"></a>Déployer l’application sur un cluster de développement local
Le nombre d’instances de service par défaut pour l’application de plug-in de volume Azure Files est -1, ce qui signifie qu’il y a une instance du service déployée sur chaque nœud du cluster. Toutefois, lorsque vous déployez l’application de plug-in de volume Azure Files sur un cluster de développement local, le nombre d’instances de service doit être configuré sur 1. Cela est possible avec le paramètre d’application **InstanceCount**. Par conséquent, la commande permettant de déployer l’application de plug-in de volume Azure Files sur un cluster de développement local est :

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/AzureFilesVolumePluginApp -ApplicationTypeName AzureFilesVolumePluginType -ApplicationTypeVersion 6.4.571.9494 -ApplicationParameter @{ListenPort='19100';InstanceCount='1'}
```

```bash
sfctl application create --app-name fabric:/AzureFilesVolumePluginApp --app-type AzureFilesVolumePluginType --app-version 6.4.571.9494 --parameter '{"ListenPort": "19100","InstanceCount": "1"}'
```
## <a name="configure-your-applications-to-use-the-volume"></a>Configurer vos applications pour utiliser le volume
L’extrait de code suivant montre comment spécifier un volume basé sur Azure Files dans le manifeste de l’application de votre application. L’élément spécifique d’intérêt est la balise **Volume** :

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
      <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      <Parameter Name="MyStorageVar" DefaultValue="c:\tmp"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
            <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
            <Volume Source="azfiles" Destination="c:\VolumeTest\Data" Driver="sfazurefile">
                <DriverOption Name="shareName" Value="" />
                <DriverOption Name="storageAccountName" Value="" />
                <DriverOption Name="storageAccountKey" Value="" />
                <DriverOption Name="storageAccountFQDN" Value="" />
            </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Le nom du pilote du plug-in de volume Azure Files est **sfazurefile**. Cette valeur est définie pour l’attribut **Pilote** de l’élément **Volume** dans le manifeste de l’application.

Dans l’élément **Volume** de l’extrait de code ci-dessus, le plug-in de volume Azure Files requiert les balises suivantes :
- **Source** : il s’agit du nom du volume. L’utilisateur peut choisir n’importe quel nom pour son volume.
- **Destination** : cette balise correspond à l’emplacement auquel est mappé le volume dans le conteneur en cours d’exécution. Ainsi, la destination ne peut pas être un emplacement qui existe déjà dans votre conteneur.

Comme indiqué dans les éléments **DriverOption** de l’extrait de code ci-dessus, le plug-in de volume Azure Files prend en charge les options de pilote suivantes :
- **shareName** - Nom du partage de fichiers Azure Files qui fournit le volume au conteneur.
- **storageAccountName** - Nom du compte de stockage Azure qui contient le partage de fichiers Azure Files.
- **storageAccountKey** - Clé d’accès du compte de stockage Azure qui contient le partage de fichiers Azure Files.
- **storageAccountFQDN** - Nom du domaine associé au compte de stockage. Si storageAccountFQDN n’est pas spécifié, le nom du domaine est composé du nom du compte de stockage (storageAccountName) et du suffixe par défaut (.file.core.windows.net).  

    ```xml
    - Example1: 
        <DriverOption Name="shareName" Value="myshare1" />
        <DriverOption Name="storageAccountName" Value="myaccount1" />
        <DriverOption Name="storageAccountKey" Value="mykey1" />
        <!-- storageAccountFQDN will be "myaccount1.file.core.windows.net" -->
    - Example2: 
        <DriverOption Name="shareName" Value="myshare2" />
        <DriverOption Name="storageAccountName" Value="myaccount2" />
        <DriverOption Name="storageAccountKey" Value="mykey2" />
        <DriverOption Name="storageAccountFQDN" Value="myaccount2.file.core.chinacloudapi.cn" />
    ```

## <a name="using-your-own-volume-or-logging-driver"></a>À l’aide de votre propre pilote de volume ou de journalisation
Service Fabric permet également l’utilisation de vos propres pilotes de [volume](https://docs.docker.com/engine/extend/plugins_volume/) ou de [journalisation](https://docs.docker.com/engine/admin/logging/overview/) personnalisés. Si le pilote de volume/journalisation Docker n’est pas installé sur le cluster, vous pouvez l’installer manuellement à l’aide des protocoles RDP/SSH. Vous pouvez effectuer l’installation avec ces protocoles par le biais d’un [script de démarrage de groupe de machines virtuelles identiques](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/) ou d’un [script SetupEntryPoint](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model).

Voici un exemple de script permettant d’installer le [pilote de volume Docker pour Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/) :

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

Dans vos applications, pour utiliser le pilote de volume ou de journalisation que vous avez installé, vous devez spécifier les valeurs appropriées dans les éléments **Volume** et **LogConfig** sous **ContainerHostPolicies** dans votre manifeste de l’application.

```xml
<ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
    <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
    <LogConfig Driver="[YOUR_LOG_DRIVER]" >
        <DriverOption Name="test" Value="vale"/>
    </LogConfig>
    <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
    <Volume Source="[MyStorageVar]" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
    <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="[YOUR_VOLUME_DRIVER]" IsReadOnly="true">
        <DriverOption Name="[name]" Value="[value]"/>
    </Volume>
</ContainerHostPolicies>
```

Quand vous spécifiez un plug-in de volume, Service Fabric crée automatiquement le volume à l’aide des paramètres spécifiés. La balise **Source** pour l’élément **Volume** est le nom du volume, et la balise **Driver** spécifie le plug-in de pilote de volume. La balise **Destination** correspond à l’emplacement auquel est mappé la **Source** dans le conteneur en cours d’exécution. Ainsi, la destination ne peut pas être un emplacement qui existe déjà dans votre conteneur. Vous pouvez spécifier les options à l’aide de la balise **DriverOption** comme suit :

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Les paramètres d’application sont pris en charge pour les volumes comme indiqué dans l’extrait de code de manifeste précédent (recherchez `MyStorageVar` pour obtenir un exemple d’utilisation).

Si un pilote de journalisation Docker est spécifié, vous devez déployer des agents (ou conteneurs) pour gérer les journaux dans le cluster. Vous pouvez utiliser la balise **DriverOption** pour spécifier des options pour le pilote de journal.

## <a name="next-steps"></a>Étapes suivantes
* Pour voir des exemples de conteneur, y compris le pilote de volume, consultez les [exemples de conteneur Service Fabric](https://github.com/Azure-Samples/service-fabric-containers).
* Pour déployer des conteneurs sur un cluster Service Fabric, consultez l’article [Déployer un conteneur sur Service Fabric](service-fabric-deploy-container.md).
