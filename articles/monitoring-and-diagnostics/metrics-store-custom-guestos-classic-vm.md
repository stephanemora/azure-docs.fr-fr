---
title: Envoyer les métriques du système d’exploitation invité d’une machine virtuelle Windows (classique) vers le magasin de données Azure Monitor
description: Envoyer les métriques du système d’exploitation invité d’une machine virtuelle Windows (classique) vers le magasin de données Azure Monitor
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: cb803450f7765ae62292ff3afb7f32209b437f78
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978915"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Envoyer les métriques du système d’exploitation invité d’une machine virtuelle Windows (classique) vers le magasin de données Azure Monitor

[L’extension Windows Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) d’Azure Monitor vous permet de collecter des métriques et des journaux à partir du système d’exploitation invité (SE invité) exécuté dans le cadre d’une machine virtuelle, d’un service cloud ou d’un cluster Service Fabric. L’extension peut envoyer des données de télémétrie vers de nombreux emplacements différents répertoriés dans l’article précédemment lié.

Cet article décrit le processus pour envoyer les métriques de performance du SE invité d’une machine virtuelle Windows (classique) vers le magasin de métriques d’Azure Monitor. À partir de WAD version 1.11, vous pouvez écrire des métriques directement dans le magasin de métriques d’Azure Monitor où les métriques standard de la plateforme sont déjà collectées. En les stockant dans cet emplacement, vous avez accès aux mêmes actions que celles disponibles pour les métriques de la plateforme.  Ces actions sont les suivantes : génération d’alertes quasiment en temps réel, création de graphiques, routage, accès à partir de l’API REST et ainsi de suite.  Avant, l’extension WAD écrivait dans le Stockage Azure, mais pas dans le magasin de données d’Azure Monitor. 

La procédure décrite dans cet article fonctionne uniquement avec les machines virtuelles classiques exécutant le système d’exploitation Windows.

## <a name="pre-requisites"></a>Conditions préalables

- Vous devez être [administrateur de services fédérés ou coadministrateur](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) sur votre abonnement Azure 

- Votre abonnement doit être inscrit avec [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Vous devez avoir installé [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1), ou vous pouvez utiliser [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Création d’une machine virtuelle classique et d’un compte de stockage

1. Créez une machine virtuelle classique en utilisant l’option ![Create Classic VM](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png) (Créer une machine virtuelle classique) dans le portail Azure

1. Lorsque vous créez cette machine virtuelle, choisissez de créer un nouveau compte de stockage classique. Nous utiliserons ce compte de stockage dans les étapes ultérieures.

1. Dans le portail Azure, naviguez jusqu’au panneau de ressources du compte de stockage, choisissez **Clés** et notez le nom du compte de stockage et la clé du compte de stockage. Vous aurez besoin de ces clés dans les étapes ultérieures ![Clés d’accès de stockage](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Créer un principal du service

Créez un principal de service dans votre locataire Azure Active Directory en suivant les instructions indiquées dans [Créer un principal du service](../azure-resource-manager/resource-group-create-service-principal-portal.md). Notez ce qui suit au cours de ce processus : 
- Créer une clé secrète client pour cette application  
- Enregistrez la clé et l’ID client pour une utilisation ultérieure.

Donnez à l’application créée dans « Publication des métriques de surveillance » les autorisations pour la ressource dont vous souhaitez émettre des métriques. Vous pouvez utiliser un groupe de ressources ou tout un abonnement.  

> [!NOTE]
> L’extension Diagnostics utilise le principal de service pour s’authentifier sur Azure Monitor et émettre des métriques pour votre machine virtuelle classique.

## <a name="author-diagnostics-extension-configuration"></a>Création de la configuration de l’extension Diagnostics

1. Préparez le fichier de configuration actuelle de l’extension Diagnostics de WAD. Ce fichier détermine quels journaux et compteurs de performances doivent être collectés par l’extension Diagnostics pour votre machine virtuelle classique. Voici un exemple :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. Dans la section « SinksConfig » de votre fichier de diagnostics, définissez un nouveau récepteur Azure Monitor :

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. Dans la section de votre fichier de configuration où figure la liste des compteurs de performance à collecter, dirigez les compteurs de performances vers le récepteur Azure Monitor « AzMonSink ».

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. Dans la configuration privée, définissez le compte Azure Monitor et ajoutez les informations du principal de service à utiliser pour émettre des métriques.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Enregistrez ce fichier localement.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>Déploiement de l’extension Diagnostics sur votre service cloud

1. Lancez PowerShell et connectez-vous

    ```powershell
    Login-AzureRmAccount
    ```

1. Commencez par définir le contexte sur votre machine virtuelle classique

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Définissez le contexte du compte de stockage classique créé avec la machine virtuelle.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Définissez le chemin d’accès du fichier de diagnostics sur une variable en utilisant la commande ci-dessous.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Préparez la mise à jour sur votre machine virtuelle classique avec le fichier de diagnostics en utilisant le récepteur Azure Monitor configuré

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Déployez la mise à jour sur votre machine virtuelle en exécutant la commande ci-dessous

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Vous devez toujours obligatoirement fournir un compte de stockage dans le cadre de l’installation de l’extension Diagnostics. Les journaux et/ou les compteurs de performances spécifiés dans le fichier de configuration de diagnostics sont écrits dans le compte de stockage spécifié.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Traçage des métriques dans le portail Azure

1.  Accédez au portail Azure

1.  Dans le menu de gauche, cliquez sur Surveiller

1.  Dans le panneau de l’analyse, cliquez sur **Métriques**
   ![Accès à Métriques](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. Dans la liste déroulante de la ressource, sélectionnez votre machine virtuelle classique

1. Dans la liste déroulante d’espaces de noms, sélectionnez **azure.vm.windows.guest**

1. Dans la liste déroulante des métriques, sélectionnez **Memory\Committed Bytes in Use (Mémoire\Octets validés utilisés)**
   ![Traçage des métriques](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).
