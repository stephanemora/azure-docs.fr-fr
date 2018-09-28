---
title: Envoyer des métriques de système d’exploitation invité au service cloud classique de magasin de métriques Azure Monitor
description: Envoyer des métriques de système d’exploitation invité au service cloud classique de magasin de métriques Azure Monitor
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986912"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Envoyer des métriques de système d’exploitation invité au service cloud classique de magasin de métriques Azure Monitor

[L’extension Windows Azure Diagnostics](azure-diagnostics.md) (WAD) d’Azure Monitor vous permet de collecter des métriques et des journaux à partir du système d’exploitation invité (SE invité) exécuté dans le cadre d’une machine virtuelle, d’un service cloud ou d’un cluster Service Fabric.  L’extension peut envoyer des données de télémétrie vers de nombreux emplacements différents répertoriés dans l’article précédemment lié.  

Cet article décrit le processus pour envoyer les métriques de performances du SE invité de services cloud classiques Azure au magasin de métriques Azure Monitor. À partir de WAD version 1.11, vous pouvez écrire des métriques directement dans le magasin de métriques d’Azure Monitor où les métriques standard de la plateforme sont déjà collectées. En les stockant dans cet emplacement, vous avez accès aux mêmes actions que celles disponibles pour les métriques de la plateforme.  Ces actions sont les suivantes : génération d’alertes en temps quasi réel, création de graphiques, routage, accès à partir de l’API REST et bien plus encore.  Avant, l’extension WAD écrivait dans Stockage Azure, mais pas dans le magasin de données d’Azure Monitor.  

Le processus décrit dans cet article fonctionne uniquement pour les compteurs de performances sur Microsoft Azure Cloud Services. Il ne fonctionne pas pour d’autres métriques personnalisées. 
   

## <a name="pre-requisites"></a>Conditions préalables

- Vous devez être [administrateur de services fédérés ou coadministrateur](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) sur votre abonnement Azure 

- Votre abonnement doit être inscrit auprès de [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Vous devez avoir installé [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1), ou vous pouvez utiliser [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>Approvisionner un service cloud et un compte de stockage 

1. Créez et déployez un service cloud classique (un exemple de déploiement et d’application de service cloud classique est disponible [ici](../cloud-services/cloud-services-dotnet-get-started.md)). 

2. Vous pouvez utiliser un compte de stockage existant ou en déployer un nouveau. Il est préférable que le compte de stockage se trouve dans la même région que le service cloud classique que vous venez de créer. Dans le Portail Azure, accédez au panneau de ressource de compte de stockage et choisissez les **clés**. Notez le nom et la clé de compte de stockage, car vous en avez besoin pour les étapes suivantes.

   ![Clés de compte de stockage](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Créer un principal du service 

Créez un principal de service dans votre locataire Azure Active Directory en suivant les instructions indiquées à l’emplacement ../azure/azure-resource-manager/resource-group-create-service-principal-portal. Notez ce qui suit au cours de ce processus : 
  - Vous pouvez indiquer n’importe quelle URL pour l’URL de connexion.  
  - Créez une clé secrète client pour cette application.  
  - Enregistrez la clé et l’ID client pour une utilisation ultérieure.  

Donnez à l’application créée dans le cadre de l’étape précédente des autorisations *Monitoring Metrics Publisher* (Éditeur de métriques de supervision) pour la ressource pour laquelle vous souhaitez émettre des métriques. Si vous envisagez d’utiliser l’application pour émettre des métriques personnalisées concernant de nombreuses ressources, vous pouvez accorder ces autorisations au niveau du groupe de ressources ou de l’abonnement.  

> [!NOTE]
> L’extension Diagnostics utilise le principal de service pour s’authentifier sur Azure Monitor et émettre des métriques pour votre service cloud. 

## <a name="author-diagnostics-extension-configuration"></a>Créer la configuration de l’extension Diagnostics 

Préparez le fichier config de l’extension Diagnostics de WAD. Ce fichier détermine quels journaux et compteurs de performances doivent être collectés par l’extension Diagnostics pour votre service cloud. Vous trouverez ci-dessous un exemple de fichier config de diagnostics.  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

Dans la section « SinksConfig » de votre fichier de diagnostics, définissez un nouveau récepteur Azure Monitor : 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

Dans la section de votre fichier config où vous répertoriez les compteurs de performances à collecter, ajoutez le récepteur Azure Monitor. Cette entrée garantit que tous les compteurs de performances spécifiés sont acheminés vers Azure Monitor en tant que métriques. N’hésitez pas à ajouter/supprimer des compteurs de performances selon vos besoins. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Enfin, dans la configuration privée, ajoutez une section *Azure Monitor Account* (Compte Azure Monitor). Entrez l’ID de client du principal de service et le secret qui ont été créés lors de l’étape précédente. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```
 
Enregistrez ce fichier de diagnostics en local.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Déployer l’extension Diagnostics sur votre service cloud 

Lancez PowerShell et connectez-vous à Azure 

```PowerShell
Login-AzureRmAccount 
```

Stockez dans des variables les informations sur le compte de stockage créé lors d’une étape précédente à l’aide des commandes suivantes. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
De la même façon, définissez le chemin d’accès du fichier de diagnostics sur une variable en utilisant la commande ci-dessous. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Déployez l’extension Diagnostics sur votre service cloud avec le fichier de diagnostics, avec le récepteur Azure Monitor configuré à l’aide de la commande suivante 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Vous devez toujours fournir un compte de stockage dans le cadre de l’installation de l’extension Diagnostics. Les journaux et/ou les compteurs de performances spécifiés dans le fichier config de diagnostics sont écrits dans le compte de stockage spécifié.  

## <a name="plot-metrics-in-the-azure-portal"></a>Tracer des métriques dans le Portail Azure 

Accédez au Portail Azure 

 ![Métriques dans le Portail Azure](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. Dans le menu de gauche, cliquez sur Surveiller 

1. Dans le panneau Surveiller, cliquez sur l’onglet Metrics Preview (Aperçu des métriques) 

1. Dans la liste déroulante des ressources, sélectionnez votre service cloud classique 

1. Dans la liste déroulante des espaces de noms, sélectionnez **azure.vm.windows.guest** 

1. Dans la liste déroulante des métriques, sélectionnez *Memory\Committed Bytes in Use* (Mémoire\Octets validés utilisés) 

Vous pouvez choisir d’afficher la mémoire totale utilisée par un rôle spécifique et chaque instance de rôle à l’aide des fonctionnalités de fractionnement et de filtrage des dimensions. 

 ![Métriques dans le Portail Azure](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur les [métriques personnalisées](metrics-custom-overview.md).



