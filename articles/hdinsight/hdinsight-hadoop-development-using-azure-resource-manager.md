---
title: Migrer vers les outils Azure Resource Manager pour HDInsight
description: Procédure de migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight
services: hdinsight
ms.reviewer: jasonh
author: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: adf5bbaee6669ac232c7d28a8c46cd2dd89c3d48
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955199"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migration vers les outils de développement Azure Resource Manager pour les clusters HDInsight

HDInsight déconseille les outils Azure Service Manager (ASM) pour HDInsight. Si vous avez utilisé Azure PowerShell, Azure Classic CLI ou le SDK .NET HDInsight pour les clusters HDInsight, nous vous invitons désormais à utiliser les versions Azure Resource Manager de PowerShell, de l’interface de ligne de commande et du SDK .NET. Cet article explique comment effectuer la migration vers la nouvelle approche Resource Manager. Cet article souligne également les éventuelles différences entre les approches ASM et Resource Manager pour HDInsight.

> [!IMPORTANT]
> La prise en charge de PowerShell, de l’interface de ligne de commande et du SDK .NET ASM sera interrompue le **1er janvier 2017**.
> 
> 

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migration de l’interface Azure Classic CLI vers Azure Resource Manager

> [!IMPORTANT]
> Azure CLI ne prend pas en charge les clusters HDInsight. Vous pouvez toujours utiliser l’interface Azure Classic CLI avec HDInsight, mais elle est dépréciée.

Voici les commandes de base pour utiliser HDInsight par le biais d’Azure Classic CLI :

* `azure hdinsight cluster create` - crée un nouveau cluster HDInsight
* `azure hdinsight cluster delete` - supprime un cluster HDInsight existant
* `azure hdinsight cluster show` -affiche des informations sur un cluster existant
* `azure hdinsight cluster list` - répertorie les clusters HDInsight pour votre abonnement Azure

Utilisez le commutateur `-h` pour inspecter les paramètres et les commutateurs disponibles pour chaque commande.

### <a name="new-commands"></a>Nouvelles commandes
Les nouvelles commandes disponibles avec Azure Resource Manager sont :

* `azure hdinsight cluster resize` -modifie de manière dynamique le nombre de nœuds worker dans le cluster
* `azure hdinsight cluster enable-http-access` - active l’accès HTTPs au cluster (activé par défaut)
* `azure hdinsight cluster disable-http-access` - désactive l’accès HTTPs au cluster
* `azure hdinsight script-action` - fournit des commandes pour créer/gérer des actions de script sur un cluster
* `azure hdinsight config` - fournit des commandes pour créer un fichier de configuration qui peut être utilisé avec la commande `hdinsight cluster create` pour fournir des informations de configuration.

### <a name="deprecated-commands"></a>Commandes déconseillées
Si vous utilisez les commandes `azure hdinsight job` pour envoyer des travaux vers votre cluster HDInsight, notez que celles-ci ne sont pas disponibles avec les commandes Resource Manager. Si vous devez envoyer des travaux par programme vers HDInsight à partir de scripts, vous devez à la place utiliser les API REST fournies par HDInsight. Pour plus d’informations sur l’envoi de travaux à l’aide des API REST, consultez les documents suivants.

* [Exécution à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Exécution de requêtes Hive avec Hadoop dans HDInsight via Curl](hadoop/apache-hadoop-use-hive-curl.md)
* [Exécution à distance des tâches Pig avec Hadoop sur HDInsight à l’aide de Curl](hadoop/apache-hadoop-use-pig-curl.md)

Pour plus d’informations sur d’autres méthodes d’exécution interactive de MapReduce, Hive et Pig, consultez [Utilisation de MapReduce avec Hadoop sur HDInsight](hadoop/hdinsight-use-mapreduce.md), [Utilisation de Hive avec Hadoop dans HDInsight](hadoop/hdinsight-use-hive.md) et [Utilisation de Pig avec Hadoop sur HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Exemples
**Création d’un cluster**

* Ancienne commande (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nouvelle commande : `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Suppression d’un cluster**

* Ancienne commande (ASM) - `azure hdinsight cluster delete myhdicluster`
* Nouvelle commande : `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Énumérer les clusters**

* Ancienne commande (ASM) - `azure hdinsight cluster list`
* Nouvelle commande : `azure hdinsight cluster list`

> [!NOTE]
> Pour la commande list, le fait de spécifier le groupe de ressources à l’aide de `-g` renvoie uniquement les clusters dans le groupe de ressources spécifié.
> 
> 

**Afficher les informations du cluster**

* Ancienne commande (ASM) - `azure hdinsight cluster show myhdicluster`
* Nouvelle commande : `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migration d’Azure PowerShell vers Azure Resource Manager
Les informations générales concernant Azure PowerShell en mode Azure Resource Manager sont disponibles dans la page [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

Les applets de commande Azure PowerShell Resource Manager peuvent être installées côte à côte avec les applets de commande ASM. Les applets de commande des deux modes se distinguent par leurs noms.  Le mode Resource Manager comprend *AzureRmHDInsight* dans les noms d’applets de commande, au lieu de *AzureHDInsight* en mode ASM.  Par exemple, *New-AzureRmHDInsightCluster* et *New-AzureHDInsightCluster*. Toutefois, certains paramètres et commutateurs peuvent avoir de nouveaux noms, et de nombreux nouveaux paramètres sont disponibles lorsque vous utilisez Resource Manager.  Par exemple, plusieurs applets de commande exigent un nouveau commutateur appelé *-ResourceGroupName*. 

Avant de pouvoir utiliser les applets de commande HDInsight, vous devez vous connecter à votre compte Azure et créer un nouveau groupe de ressources :

* Connect-AzureRmAccount ou [Select-AzureRmProfile](https://docs.microsoft.com/powershell/module/servicemanagement/azurerm.profile/select-azurermprofile?view=azuresmps-4.0.0). Consultez [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Applets de commande renommées
Pour répertorier les applets de commande HDInsight ASM dans la console Windows PowerShell :

    help *azurermhdinsight*

Le tableau suivant liste les applets de commande ASM et leurs noms en mode Resource Manager :

| Applets de commande ASM | Applets de commande Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightconfigvalues?view=azurermps-6.6.0) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightmetastore?view=azurermps-6.6.0) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightscriptaction?view=azurermps-6.6.0) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/add-azurermhdinsightstorage?view=azurermps-6.6.0) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjob?view=azurermps-6.6.0) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightjoboutput?view=azurermps-6.6.0) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/get-azurermhdinsightproperties?view=azurermps-6.6.0) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/invoke-azurermhdinsighthivejob?view=azurermps-6.6.0) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightclusterconfig?view=azurermps-6.6.0) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsighthivejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightmapreducejobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightpigjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightsqoopjobdefinition?view=azurermps-6.6.0) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightstreamingmapreducejobdefinition?view=azurermps-6.6.0) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/remove-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess?view=azurermps-6.6.0) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsightrdpservicesaccess?view=azurermps-6.6.0) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize?view=azurermps-6.6.0) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightdefaultstorage?view=azurermps-6.6.0) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/start-azurermhdinsightjob?view=azurermps-6.6.0) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/stop-azurermhdinsightjob?view=azurermps-6.6.0) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/use-azurermhdinsightcluster?view=azurermps-6.6.0) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/wait-azurermhdinsightjob?view=azurermps-6.6.0) |

### <a name="new-cmdlets"></a>Nouvelles applets de commande
Les nouvelles applets de commande suivantes sont uniquement disponibles en mode Resource Manager. 

**Applets de commande associées à une action de script :**

* **Get-AzureRmHDInsightPersistedScriptAction**: obtient les actions de script persistantes pour un cluster et les répertorie par ordre chronologique ou obtient des informations pour une action de script persistante spécifiée. 
* **Get-AzureRmHDInsightScriptActionHistory**: obtient l’historique d’actions de script pour un cluster et les répertorie par ordre chronologique inversé, ou obtient des informations d’une action de script exécutée précédemment. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: retire une action de script persistante d’un cluster HDInsight.
* **Set-AzureRmHDInsightPersistedScriptAction**: définit une action de script exécutée précédemment comme action de script persistante.
* **Submit-AzureRmHDInsightScriptAction**: envoie une nouvelle action de script vers un cluster Azure HDInsight. 

Pour plus d’informations sur l’utilisation, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

**Applets de commande associées à l’identité du cluster :**

* **Add-AzureRmHDInsightClusterIdentity**: ajoute une identité de cluster à un objet configuration de cluster pour que le cluster HDInsight puisse accéder à Azure Data Lake Store. Consultez la page [Créer un cluster HDInsight avec Data Lake Store à l’aide d’Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemples
**Créer un cluster**

Ancienne commande (ASM) : 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nouvelle commande :

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Supprimer un cluster**

Ancienne commande (ASM) :

    Remove-AzureHDInsightCluster -name $clusterName 

Nouvelle commande :

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Répertorier un cluster**

Ancienne commande (ASM) :

    Get-AzureHDInsightCluster

Nouvelle commande :

    Get-AzureRmHDInsightCluster 

**Afficher le cluster**

Ancienne commande (ASM) :

    Get-AzureHDInsightCluster -Name $clusterName

Nouvelle commande :

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Autres exemples
* [Création de clusters HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Envoi de tâches Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Envoi de tâches Pig](hadoop/apache-hadoop-use-pig-powershell.md)
* [Envoi de tâches Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migration vers le nouveau SDK .NET HDInsight
Le [Kit de développement logiciel (SDK) .NET HDInsight (ASM)](https://msdn.microsoft.com/library/azure/mt416619.aspx) Azure Service Management est maintenant déconseillé. Nous vous invitons désormais à utiliser le [SDK .NET HDInsight Resource Manager](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) qui est basé sur la gestion des ressources Azure. Les packages HDInsight ASM suivants sont désormais déconseillés.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Cette section fournit des liens vers des rubriques qui expliquent comment effectuer certaines tâches à l’aide du SDK Resource Manager.

| Comment... avec le SDK HDInsight Resource Manager | Liens |
| --- | --- |
| Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personnaliser un cluster à l’aide d’une action de script avec le Kit de développement logiciel (SDK) .NET |Consultez [Personnaliser des clusters HDInsight sous Linux à l’aide d’une action de script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Authentifier des applications de manière interactive à l’aide d’Azure Active Directory avec le Kit de développement logiciel (SDK) .NET |Consultez [Exécution de requêtes Hive avec le Kit de développement logiciel (SDK) .NET](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Dans cet article, l’extrait de code utilise l’approche de l’authentification interactive. |
| Authentifier des applications de manière non interactive à l’aide d’Azure Active Directory avec le Kit de développement logiciel (SDK) .NET |Consultez [Créer des applications HDInsight d’authentification non interactives](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Envoyer une tâche Hive à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Envoi de tâches Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Envoyer une tâche Pig à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Envoi de tâches Pig](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| Envoyer une tâche Sqoop à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Envoi de tâches Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Répertorier les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Répertorier les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Mettre à l’échelle les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Mettre à l’échelle les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Autoriser/révoquer l’accès aux clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Autoriser/révoquer l’accès aux clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Mettre à jour les informations d’identification de l’utilisateur HTTP pour les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Mettre à jour les informations d’identification de l’utilisateur HTTP pour les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Rechercher le compte de stockage par défaut pour les clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Rechercher le compte de stockage par défaut pour les clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Supprimer des clusters HDInsight à l’aide du Kit de développement logiciel (SDK) .NET |Consulter [Supprimer des clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemples
Vous trouverez ci-dessous des exemples de la façon dont une opération est effectuée à l’aide du SDK ASM, ainsi que l’extrait de code équivalent pour le SDK Resource Manager.

**Création d’un client CRUD de cluster**

* Ancienne commande (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nouvelle commande (autorisation du principal de service)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nouvelle commande (autorisation de l’utilisateur)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Création d’un cluster**

* Ancienne commande (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nouvelle commande
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Activation de l’accès HTTP**

* Ancienne commande (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nouvelle commande
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Suppression d’un cluster**

* Ancienne commande (ASM)
  
        client.DeleteCluster(dnsName);
* Nouvelle commande
  
        client.Clusters.Delete(resourceGroup, dnsname);

