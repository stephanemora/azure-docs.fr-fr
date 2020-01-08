---
title: Utilisation d’un Stockage Blob pour IIS et d’un Stockage Table pour les événements dans Azure Monitor | Microsoft Docs
description: Azure Monitor peut lire les journaux d’activité pour des services Azure qui écrivent des diagnostics dans un Stockage Table ou des journaux d’activité IIS écrits dans un Stockage Blob.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/12/2017
ms.openlocfilehash: 3e8968f544e347b7b4cfe6db484c2ce8bd404820
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395986"
---
# <a name="collect-azure-resource-logs-from-azure-storage"></a>Collecter les journaux de ressources Azure depuis Stockage Azure

Azure Monitor peut lire les journaux d’activité pour des services suivants qui écrivent des diagnostics dans un Stockage Table ou des journaux d’activité IIS écrits dans un Stockage Blob :

* Clusters Service Fabric (version préliminaire)
* Virtual Machines
* Rôle de travail/web

Avant qu’Azure Monitor puisse collecter des données dans un espace de travail Log Analytics pour ces ressources, les diagnostics Azure doivent être activés.

Une fois les diagnostics activés, vous pouvez utiliser le Portail Microsoft Azure ou PowerShell pour configurer l’espace de travail afin de collecter les journaux d’activité.

Les diagnostics Azure sont une extension vous permettant de collecter des données de diagnostic à partir d’un rôle de travail, d’un rôle Web ou d’une machine virtuelle exécuté dans Azure. Les données sont stockées dans un compte de stockage Azure à partir duquel Azure Monitor peut les collecter.

Pour qu’Azure Monitor collecte ces journaux de diagnostics Azure, ceux-ci doivent être dans les emplacements suivants :

| Type de journal | Type de ressource | Location |
| --- | --- | --- |
| Journaux d’activité IIS |Virtual Machines <br> Rôles web <br> Rôles de travail |wad-iis-logfiles (Stockage Blob) |
| syslog |Virtual Machines |LinuxSyslogVer2v0 (Stockage Table) |
| Événements opérationnels Service Fabric |Nœuds Service Fabric |WADServiceFabricSystemEventTable |
| Événements Reliable Actor Service Fabric |Nœuds Service Fabric |WADServiceFabricReliableActorEventTable |
| Événements de service fiable Service Fabric |Nœuds Service Fabric |WADServiceFabricReliableServiceEventTable |
| Journaux d’événements Windows |Nœuds Service Fabric <br> Virtual Machines <br> Rôles web <br> Rôles de travail |WADWindowsEventLogsTable (Stockage Table) |
| Journaux d’activité de suivi des événements ETW Windows |Nœuds Service Fabric <br> Virtual Machines <br> Rôles web <br> Rôles de travail |WADETWEventTable (Stockage Table) |

> [!NOTE]
> Les journaux d’activité IIS des sites Web Azure ne sont actuellement pas pris en charge.
>
>

Pour les machines virtuelles, vous pouvez installer [l’agent Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) sur votre machine virtuelle pour activer des informations supplémentaires. Cela vous permet d’analyser les journaux d’activité IIS et les journaux des événements, mais également d’effectuer des analyses supplémentaires, notamment le suivi des modifications de configuration, l’évaluation SQL et l’évaluation de la mise à jour.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>Activation des diagnostics Azure dans une machine virtuelle pour la collecte des journaux IIS et des journaux des événements

Utilisez la procédure suivante pour activer les diagnostics Azure dans une machine virtuelle pour la collecte de journaux IIS et de journaux des événements à l’aide du portail Microsoft Azure.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Activation des diagnostics Azure dans une machine virtuelle à l’aide du portail Azure

1. Installez l’agent de machine virtuelle lorsque vous créez une machine virtuelle. Si la machine virtuelle existe déjà, vérifiez que l’agent de machine virtuelle est déjà installé.

   * Dans le portail Azure, accédez à la machine virtuelle, sélectionnez **Configuration facultative**, puis **Diagnostics**, et définissez **État** sur **Activé**.

     Une fois cela terminé, la machine virtuelle a l’extension Diagnostics Azure installée et en cours d’exécution. Cette extension est chargée de collecter vos données de diagnostic.
2. Activation de l’analyse et configuration de la journalisation des événements sur une machine virtuelle existante. Vous pouvez activer les diagnostics au niveau de la machine virtuelle. Pour activer les diagnostics et configurer la journalisation des événements par la suite, procédez comme suit :

   1. Sélectionnez la machine virtuelle.
   2. Cliquez sur **Supervision**.
   3. Cliquez sur **Diagnostics**.
   4. Définissez **État** sur **ACTIVÉ**.
   5. Cliquez sur les métriques de diagnostic que vous souhaitez utiliser.
   6. Cliquez sur **OK**.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>Activation des diagnostics Azure dans un rôle Web pour la collecte de journaux IIS et des événements

Reportez-vous à [Procédure : activer les diagnostics dans un service cloud](../../cloud-services/cloud-services-dotnet-diagnostics.md) pour connaître les étapes générales d’activation des diagnostics Azure. Les instructions ci-dessous utilisent ces informations et les personnalisent pour une utilisation avec Log Analytics.

Avec les diagnostics Azure activés :

* Les journaux d’activité IIS sont stockés par défaut et les données de journal d’activité sont transférées selon l’intervalle de transfert scheduledTransferPeriod.
* Les journaux des événements Windows ne sont pas transférés par défaut.

### <a name="to-enable-diagnostics"></a>Activation des diagnostics

Pour activer les journaux des événements Windows, ou pour modifier l’intervalle scheduledTransferPeriod, configurez Diagnostics Azure à l’aide du fichier de configuration XML (diagnostics.wadcfg), comme indiqué dans [Étape 4 : création de votre fichier de configuration Diagnostics et installation de l’extension](../../cloud-services/cloud-services-dotnet-diagnostics.md)

L’exemple de fichier de configuration suivant collecte des journaux d’activité IIS et tous les événements des journaux d’activité de l’application et du système :

```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

Assurez-vous que votre paramètres ConfigurationSettings spécifient un compte de stockage, comme dans l’exemple suivant :

```xml
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

Les valeurs **AccountName** et **AccountKey** figurent dans le portail Azure, sur le tableau de bord du compte de stockage, sous Gérer les clés d’accès. Le protocole de la chaîne de connexion doit être **https**.

Une fois que la configuration de diagnostic mise à jour est appliquée à votre service cloud et écrit des diagnostics dans Stockage Azure, vous êtes prêt à configurer l’espace de travail Log Analytics.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Utiliser le portail Azure pour collecter les journaux d’activité à partir de Stockage Azure

Vous pouvez utiliser le Portail Microsoft Azure pour configurer un espace de travail Log Analytics dans Azure Monitor afin de collecter les journaux d’activité pour les services Azure suivants :

* Clusters Service Fabric
* Virtual Machines
* Rôle de travail/web

Dans le portail Azure, accédez à votre espace de travail Log Analytics, puis effectuer les tâches suivantes :

1. Cliquez sur *Journaux d’activité des comptes de stockage*.
2. Cliquez sur la tâche *Ajouter*.
3. Sélectionnez le compte de stockage contenant les journaux de diagnostic.
   * Ce compte peut être un compte de stockage classique ou un compte de stockage Azure Resource Manager
4. Sélectionnez le Type de données pour lequel vous souhaitez collecter des journaux d’activité.
   * Les choix sont Journaux d’activité IIS, Événements, Syslog (Linux), Journaux d’activité de suivi des événements ETW, Événements Service Fabric
5. La valeur de Source est automatiquement complétée selon le type de données, et ne peut pas être modifiée
6. Cliquez sur OK pour enregistrer la configuration.

Répétez les étapes 2 à 6 pour les autres comptes de stockage et types de données que vous voulez collecter dans l’espace de travail.

Après environ 30 minutes, vous pourrez voir les données du compte de stockage dans l’espace de travail Log Analytics. Vous voyez les données écrites dans le stockage uniquement après application de la configuration. L’espace de travail ne lit pas les données préexistantes du compte de stockage.

> [!NOTE]
> Le portail ne valide pas l’existence de la Source dans le compte de stockage ou le fait que de nouvelles données sont écrites.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>Activer les diagnostics Azure dans une machine virtuelle pour la collecte des journaux IIS et des journaux des événements avec PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Utilisez les étapes de [Configuration d’Azure Monitor pour indexer les diagnostics Azure](powershell-workspace-configuration.md#configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage) pour utiliser PowerShell afin de lire à partir des diagnostics Azure écrits dans le stockage de table.

Vous pouvez spécifier les événements écrits dans Azure Storage plus précisément à l’aide d’Azure PowerShell.
Pour plus d'informations, consultez la page [Activation des diagnostics dans les machines virtuelles Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Vous pouvez activer et mettre à jour les diagnostics Azure en utilisant le script PowerShell suivant.
Vous pouvez également utiliser ce script avec une configuration de journalisation personnalisée.
Modifiez le script pour définir le compte de stockage, le nom du service et le nom de la machine virtuelle.
Le script utilise des applets de commande pour les machines virtuelles classiques.

Examinez l’exemple de script suivant, copiez-le et modifiez-le si nécessaire, enregistrez l’exemple dans un fichier de script PowerShell, puis exécutez le script.

```powershell
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>Étapes suivantes

* [Collecter les journaux d’activité et les indicateurs de performance des services Azure](collect-azure-metrics-logs.md) pour les services pris en charge par Azure.
* [Activer les solutions](../../azure-monitor/insights/solutions.md) pour fournir des informations sur les données.
* [Utiliser les requêtes de recherche](../../azure-monitor/log-query/log-query-overview.md) pour analyser les données.
