---
title: Intégrer Azure Monitor pour machines virtuelles | Microsoft Docs
description: Cet article décrit la façon dont vous intégrez et configurez Azure Monitor pour les machines virtuelles afin que vous puissiez comprendre comment votre application distribuée s’exécute et les problèmes d’intégrité qui ont été identifiés.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064354"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Comment intégrer Azure Monitor pour machines virtuelles 
Cet article décrit comment configurer Azure Monitor pour surveiller l’intégrité du système d’exploitation de vos machines virtuelles Azure et comment découvrir et mapper les dépendances des applications pouvant être hébergées dessus.  

L’activation d’Azure Monitor pour machines virtuelles est accomplie en utilisant l’une des méthodes suivantes, des détails sur l’utilisation de chaque méthode sont fournis plus loin dans l’article.  

* Une seule machine virtuelle Azure en sélectionnant **Insights (préversion)** directement à partir de la machine virtuelle.
* Plusieurs machines virtuelles Azure à l’aide d’Azure Policy pour vous assurer que les machines virtuelles évaluées, nouvelles et existantes, disposent des dépendances nécessaires installées et qu’elles sont correctement configurées.  Les machines virtuelles non conformes sont signalées afin que vous puissiez décider la façon dont vous souhaitez les corriger, selon ce qui n’est pas conforme.  
* Plusieurs machines virtuelles Azure ou des groupes de machines virtuelles identiques sur un abonnement ou un groupe de ressources spécifié à l’aide de PowerShell.

## <a name="prerequisites"></a>Prérequis
Avant de commencer, assurez-vous de disposer des éléments décrits dans les sous-sections suivantes.

### <a name="log-analytics"></a>Log Analytics 

Un espace de travail Log Analytics dans les régions suivantes est actuellement pris en charge :

  - USA Centre-Ouest  
  - USA Est  
  - Europe Ouest  
  - Asie Sud-Est<sup>1</sup>  

<sup>1</sup> Cette région ne prend pas encore en charge la fonctionnalité d’intégrité d’Azure Monitor pour machines virtuelles   

Si vous ne disposez pas d’un espace de travail, vous pouvez le créer via [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../log-analytics/log-analytics-quick-create-workspace.md).  

Pour activer la solution, vous devez être membre du rôle de contributeur Log Analytics. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Le tableau ci-après répertorie les systèmes d’exploitation Windows et Linux qui sont pris en charge avec Azure Monitor pour machines virtuelles.  Une liste complète détaillant les versions principales et mineures de système d’exploitation et de noyau Linux prises en charge est fournie plus loin dans cette section.

|Version du SE |Performances |Cartes |Intégrité |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> La fonctionnalité d’Azure Monitor pour machines virtuelles est uniquement disponible à partir d’Azure Monitor, elle n’est pas disponible lorsque vous y accédez directement à partir du volet gauche de la machine virtuelle Azure.  

>[!NOTE]
>Les informations ci-après concernent la prise en charge du système d’exploitation Linux :  
> - Seules les versions du noyau SMP Linux et par défaut sont prises en charge.  
> - Les versions non standard du noyau, par exemple PAE et Xen, ne sont prises en charge par aucune distribution Linux. Par exemple, un système avec la chaîne de version « 2.6.16.21-0.8-xen » n’est pas pris en charge.  
> - Les noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.  
> - Le noyau CentOSPlus n’est pas pris en charge.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Version du SE | Version du noyau |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Version du SE | Version du noyau |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Serveur Ubuntu

| Version du SE | Version du noyau |
|:--|:--|
| Ubuntu 18.04 | noyau 4.15.* |
| Ubuntu 16.04.3 | noyau 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 avec noyau Unbreakable Enterprise
| Version du SE | Version du noyau
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 avec noyau Unbreakable Enterprise

| Version du SE | Version du noyau
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Version du SE | Version du noyau
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="hybrid-environment-connected-sources"></a>Sources connectées d’environnement hybride
Azure Monitor pour machines virtuelles Map obtient ses données à partir de l’agent de dépendances Microsoft. Celui-ci dépend de l’agent Log Analytics pour ses connexions à Log Analytics. Cela signifie que l’agent Log Analytics doit être installé et configuré avec l’agent de dépendances sur le système.  Le tableau suivant décrit les sources connectées prises en charge par la fonctionnalité Map dans un environnement hybride.

| Source connectée | Prise en charge | Description |
|:--|:--|:--|
| Agents Windows | Oui | Outre [l’agent Log Analytics pour Windows](../log-analytics/log-analytics-concept-hybrid.md), les agents Windows nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez la page [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Agents Linux | Oui | Outre [l’agent Log Analytics pour Linux](../log-analytics/log-analytics-concept-hybrid.md), les agents Linux nécessitent l’agent de dépendances Microsoft. Pour obtenir la liste complète des versions des systèmes d’exploitation, consultez la page [Systèmes d’exploitation pris en charge](#supported-operating-systems). |
| Groupe d’administration Microsoft System Center Operations Manager | Non  | |  

Sous Windows, Microsoft Monitoring Agent (MMA) est utilisé à la fois par System Center Operations Manager et par Log Analytics pour collecter et envoyer des données de contrôle. System Center Operations Manager et Log Analytics fournissent différentes versions prêtes à l’emploi de l’agent. Les deux versions peuvent envoyer leurs rapports à System Center Operations Manager, à Log Analytics ou aux deux.  

Sous Linux, l’agent Log Analytics pour Linux collecte et envoie les données de surveillance à Log Analytics.   

Si vos ordinateurs Windows ou Linux ne peuvent pas se connecter directement au service, vous devez configurer l’agent Log Analytics pour qu’il se connecte à Log Analytics à l’aide de la passerelle OMS. Pour plus d’informations sur le déploiement et la configuration de la passerelle OMS, voir [Connecter des ordinateurs sans accès à Internet avec la passerelle OMS](../log-analytics/log-analytics-oms-gateway.md).  

L’agent de dépendances peut être téléchargé à partir de l’emplacement suivant.

| Fichier | SE | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>Données relatives aux diagnostics et à l’utilisation
Microsoft collecte automatiquement les données sur l’utilisation et les performances via votre utilisation du service Azure Monitor. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service. Afin d’offrir des fonctionnalités de dépannage précises et efficaces, les données de la fonctionnalité Map incluent des informations sur la configuration de votre logiciel, telles que le système d’exploitation et la version, l’adresse IP, le nom DNS et le nom de la station de travail. Microsoft ne collecte pas de nom, d’adresse ou d’autres coordonnées.

Pour plus d’informations sur l’utilisation et la collecte de données, voir la [Déclaration de confidentialité des Services en ligne de Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Compteurs de performance activés
Azure Monitor pour machines virtuelles configure un espace de travail Log Analytics pour collecter des compteurs de performances utilisés par la solution.  Le tableau suivant répertorie les objets et les compteurs configurés par la solution et qui sont collectés toutes les 60 secondes.

### <a name="windows-performance-counters"></a>Compteurs de performances Windows

|Nom d’objet |Nom du compteur |  
|------------|-------------|  
|LogicalDisk |% Free Space |  
|LogicalDisk |Avg. Disk sec/Read |  
|LogicalDisk |Avg. Disk sec/Transfer |  
|LogicalDisk |Avg. Disk sec/Write |  
|LogicalDisk |Disk Bytes/sec |  
|LogicalDisk |Nb d’octets de lecture de disque/s  |  
|LogicalDisk |Nb d’opérations de lectures de disque/s  |  
|LogicalDisk |Disk Transfers/sec |  
|LogicalDisk |Nb d’octets d’écriture de disque/s |  
|LogicalDisk |Nb d’opération d’écriture de disque/s |  
|LogicalDisk |Free Megabytes |  
|Mémoire |Nombre d’octets disponibles |  
|Carte réseau |Octets reçus/s |  
|Carte réseau |Octets envoyés/s |  
|Processeur |% temps processeur |  

### <a name="linux-performance-counters"></a>Compteurs de performances Linux

|Nom d’objet |Nom du compteur |  
|------------|-------------|  
|Logical Disk |% Used Space |  
|Logical Disk |Nb d’octets de lecture de disque/s  |  
|Logical Disk |Nb d’opérations de lectures de disque/s  |  
|Logical Disk |Disk Transfers/sec |  
|Logical Disk |Nb d’octets d’écriture de disque/s |  
|Logical Disk |Nb d’opération d’écriture de disque/s |  
|Logical Disk |Free Megabytes |  
|Logical Disk |Logical Disk Bytes/sec |  
|Mémoire |Available MBytes Memory |  
|Réseau |Total Bytes Received |  
|Réseau |Total Bytes Transmitted |  
|Processeur |% temps processeur |  

## <a name="enable-from-the-azure-portal"></a>Activer à partir du portail Azure
Pour activer la surveillance de votre machine virtuelle Azure à partir du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez **Machines virtuelles**. 
2. Sélectionnez une machine virtuelle dans la liste. 
3. Sur la page de la machine virtuelle, dans la section **Surveillance**, sélectionnez **Insights (préversion)**.
4. Sur la page **Insights (préversion)**, sélectionnez **Essayer maintenant**.

    ![Activer Azure Monitor pour machines virtuelles pour une machine virtuelle](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Dans la page **Intégration des insights de Azure Monitor**, si vous disposez d’un espace de travail Log Analytics appartenant au même abonnement, sélectionnez-le dans la liste déroulante.  La liste présélectionne l’espace de travail par défaut et l’emplacement où la machine virtuelle est déployée dans l’abonnement. 

    >[!NOTE]
    >Si vous souhaitez créer un espace de travail Log Analytics pour stocker les données de supervision de la machine virtuelle, suivez les instructions de l’article [Créer un espace de travail Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) dans l’une des régions prises en charge répertoriées précédemment.   

Une fois que vous avez activé la surveillance, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques d’intégrité de la machine virtuelle. 

![Activer Azure Monitor pour machines virtuelles surveillant le traitement du déploiement](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Activer à l’aide d’Azure Policy
Pour activer la solution pour plusieurs machines virtuelles Azure garantissant une activation automatique et une conformité cohérente pour les nouvelles machines configurées, [Azure Policy](../azure-policy/azure-policy-introduction.md) est recommandé.  L’utilisation d’Azure Policy avec les stratégies fournies offre les avantages suivants pour les nouvelles machines virtuelles :

* L’activation d’Azure Monitor pour machines virtuelles pour chaque machine virtuelle dans l’étendue définie
* Déployer l’agent Log Analytics 
* Déployer l’agent de dépendances pour découvrir les dépendances d’application et les afficher dans le mappage
* Évaluer si l’image du système d’exploitation de votre machine virtuelle Azure se trouve dans une liste prédéfinie dans la définition de stratégie  
* Évaluer si votre machine virtuelle Azure se connecte à un espace de travail autre que celui spécifié
* Créer un rapport sur les résultats de conformité 
* Prise en charge de la correction pour les machines virtuelles non conformes

Pour l’activer pour votre locataire, ce processus requiert :

- Configurer un espace de travail Log Analytics en utilisant les étapes répertoriées ici
- Importer la définition d’initiative dans votre locataire (au niveau du groupe d’administration ou de l’abonnement)
- Assigner la stratégie à l’étendue souhaitée
- Passer en revue les résultats de conformité

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Ajouter les stratégies et l’initiative à votre abonnement
Pour utiliser les stratégies, vous pouvez utiliser un script PowerShell fourni ([Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2)) disponible à partir de la galerie PowerShell Azure pour effectuer cette tâche. Ce script ajoute les stratégies et une initiative à votre abonnement.  Procédez comme suit pour configurer Azure Policy dans votre abonnement. 

1. Téléchargez le script PowerShell sur votre système de fichiers local.

2. Utilisez la commande PowerShell suivante dans le dossier pour ajouter des stratégies. Le script prend en charge les paramètres facultatifs suivants : 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Remarque: si vous envisagez d’assigner l’initiative/stratégie à plusieurs abonnements, les définitions doivent être stockées dans le groupe d'administration qui contient les abonnements auxquels vous allez affecter la stratégie. Par conséquent, vous devez utiliser le paramètre -ManagementGroupID.
    >
   
    Exemple sans paramètres : `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie
Après avoir exécuté le script PowerShell `Add-VMInsightsPolicy.ps1`, les stratégies et l’initiative suivantes sont ajoutées :

* **Déployer l’agent Log Analytics pour les machines virtuelles Windows (préversion)**
* **Déployer l’agent Log Analytics pour les machines virtuelles Linux (préversion)**
* **Déployer l’agent de dépendances pour les machines virtuelles Windows (préversion)**
* **Déployer l’agent de dépendances pour les machines virtuelles Linux (préversion)**
* **Vérifier le déploiement de l’agent Log Analytics - image de machine virtuelle (OS) non listée (préversion)**
* **Vérifier le déploiement de l’agent de dépendances - image de machine virtuelle (OS) non listée (préversion)**

Le paramètre d’initiative suivant est ajouté :

- **Espace de travail Log Analytics** (vous devez fournir l’ID de ressource de l’espace de travail en cas d’application d’une affectation à l’aide de PowerShell ou CLI)

    Pour les machines virtuelles considérées comme non conformes d’après les stratégies d’audit **Machines virtuelles hors de l’étendue du système d’exploitation...**, les critères de la stratégie de déploiement incluent uniquement les machines virtuelles déployées à partir d’images de machine virtuelle Azure bien connues. Consultez la documentation pour savoir si le système d’exploitation de la machine virtuelle est pris en charge.  Si ce n’est pas le cas, vous devez dupliquer la stratégie de déploiement et la mettre à jour/modifier pour inclure l’image dans son étendue.

La stratégie facultative autonome suivante est ajoutée :

- **La machine virtuelle est configurée pour un espace de travail Log Analytics différent (préversion)**

    Elle peut être utilisée pour identifier les machines virtuelles déjà configurées avec l’[extension de machine virtuelle Log Analytics](../virtual-machines/extensions/oms-windows.md), mais qui sont configurées avec un espace de travail autre que celui prévu (comme indiqué par l’affectation de stratégie). Cela prend un paramètre pour WorkspaceID.

Avec cette version initiale, vous pouvez uniquement créer l’affectation de stratégie à partir du portail Azure. Pour comprendre comment effectuer ces étapes, consultez [Créer une affectation de stratégie à partir du portail Azure](../azure-policy/assign-policy-definition.md).

## <a name="enable-with-powershell"></a>Activer avec PowerShell
Pour activer Azure Monitor pour machines virtuelles pour plusieurs machines virtuelles ou des groupes identiques de machines virtuelles, vous pouvez utiliser un script PowerShell fourni ([Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)) disponible à partir de la galerie PowerShell Azure pour effectuer cette tâche.  Ce script effectue une itération dans chaque machine virtuelle et groupe identique de machines virtuelles dans votre abonnement, au sein du groupe de ressources à portée spécifié par *ResourceGroup*, ou dans une seule machine virtuelle ou groupe identique spécifié par *Name*.  Pour chaque machine virtuelle ou groupe identique de machines virtuelles, le script vérifie si l’extension de machine virtuelle est déjà installée. Si ce n’est pas le cas, il essaie de l’installer.  Sinon, il choisit d’installer les extensions de machine virtuelle Agent de dépendances et Log Analytics.   

Ce script requiert le module Azure PowerShell version 5.7.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Connect-AzureRmAccount` pour créer une connexion avec Azure.

Pour obtenir une aide concernant le script, vous pouvez exécuter `Get-Help` pour obtenir une liste des détails de l’argument et d’exemples d’utilisation.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

L’exemple suivant illustre l’utilisation des commandes PowerShell dans le dossier pour activer Azure Monitor pour machines virtuelles et comprendre la sortie attendue :

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Activer pour un environnement hybride
Cette section explique comment intégrer des machines virtuelles ou des ordinateurs physiques hébergés dans votre centre de données ou un autre environnement cloud pour la surveillance par Azure Monitor pour machines virtuelles.  

L’agent de dépendances Azure Monitor pour machines virtuelles Map ne transmet pas de données par lui-même et ne requiert pas de modifications des pare-feu ni des ports. Les données dans Map sont toujours transmises par l’agent Log Analytics au service Azure Monitor, directement ou bien par le biais de la [passerelle OMS](../log-analytics/log-analytics-oms-gateway.md) si vos stratégies de sécurité n’autorisent pas les ordinateurs du réseau à se connecter à internet.

Passez en revue les exigences et les méthodes de déploiement pour l’[agent Log Analytique Linux et Windows](../log-analytics/log-analytics-concept-hybrid.md).

Étapes résumées :

1. Installer l’agent Log Analytics pour Windows ou Linux
2. Installer l’agent de dépendances Azure Monitor pour machines virtuelles Map
3. Permettre la collecte des compteurs de performance
4. Intégrer Azure Monitor pour machines virtuelles

### <a name="install-the-dependency-agent-on-windows"></a>Installer l’agent de dépendances sous Windows 
L’agent de dépendances peut être installé manuellement sur les ordinateurs Windows en exécutant `InstallDependencyAgent-Windows.exe`. Si vous exécutez ce fichier exécutable sans aucune option, il démarre un assistant de configuration que vous pouvez suivre pour une installation interactive.  

>[!NOTE]
>Des privilèges d’administrateur sont requis pour installer ou désinstaller l’agent.

Le tableau suivant répertorie les paramètres spécifiques pris en charge par le programme d’installation pour l’agent à partir de la ligne de commande.  

| Paramètre | Description |
|:--|:--|
| /? | Retourne la liste des options de ligne de commande. |
| /S | Effectuez une installation silencieuse sans interaction utilisateur. |

Par exemple, pour exécuter le programme d’installation avec le paramètre `/?`, tapez `InstallDependencyAgent-Windows.exe /?`

Les fichiers de l’agent de dépendances Windows sont installés dans `C:\Program Files\Microsoft Dependency Agent` par défaut.  Si le démarrage de l’agent de dépendances échoue une fois l’installation terminée, recherchez des informations détaillées sur l’erreur dans les journaux. Le répertoire contenant les journaux est `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Installer l’agent de dépendances sous Linux
L’agent de dépendances est installé sur les serveurs Linux à partir de `InstallDependencyAgent-Linux64.bin`, un script d’interpréteur de commandes avec un fichier à extraction automatique. Vous pouvez exécuter le fichier à l’aide de `sh` ou ajouter des autorisations d’exécution au fichier lui-même.

>[!NOTE]
> L’accès racine est requis pour installer ou configurer l’agent.
> 

| Paramètre | Description |
|:--|:--|
| -help | Récupérez la liste des options de ligne de commande. |
| -s | Effectuez une installation silencieuse sans invite utilisateur. |
| --check | Vérifiez les autorisations et le système d’exploitation, sans installer l’agent. |

Par exemple, pour exécuter le programme d’installation avec le paramètre `-help`, tapez `InstallDependencyAgent-Linux64.bin -help`.

Installez l’agent de dépendances Linux en tant que racine en exécutant la commande suivante, `sh InstallDependencyAgent-Linux64.bin`
    
Si le démarrage de l’agent de dépendances échoue, recherchez des informations détaillées sur l’erreur dans les journaux. Sur les agents Linux, le répertoire contenant les journaux est `/var/opt/microsoft/dependency-agent/log`.

Les fichiers de l’agent de dépendances sont placés dans les répertoires suivants :

| Fichiers | Lieu |
|:--|:--|
| Fichiers principaux | /opt/microsoft/dependency-agent |
| Fichiers journaux | /var/opt/microsoft/dependency-agent/log |
| Fichiers de configuration | /etc/opt/microsoft/dependency-agent/config |
| Fichiers exécutables de service | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Fichiers de stockage binaires | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Activer les compteurs de performance
Si l’espace de travail Log Analytics référencé par la solution n’est pas déjà configuré pour collecter les compteurs de performance requis par la solution, ils doivent être activés. Cela peut être effectué manuellement comme décrit [ici](../log-analytics/log-analytics-data-sources-performance-counters.md), ou bien en téléchargeant et en exécutant un script PowerShell disponible à partir de la [galerie Azure Powershell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Intégrer Azure Monitor pour machines virtuelles
Cette méthode inclut un modèle JSON spécifiant la configuration pour activer les composants de la solution à votre espace de travail Log Analytics.  

Si vous n’êtes pas familiarisé avec le déploiement de ressources à l’aide d’un modèle, consultez les rubriques suivantes :
* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Si vous avez choisi d’utiliser Azure CLI, vous devez d’abord l’installer et l’utiliser localement. Vous devez exécuter Azure CLI 2.0.27 ou version ultérieure. Pour identifier votre version, exécutez `az --version`. Si vous devez installer ou mettre à niveau Azure CLI, consultez [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Créer et exécuter un modèle

1. Copiez et collez la syntaxe JSON suivante dans votre fichier :

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Enregistrez ce fichier sous **installsolutionsforvminsights.json** dans un dossier local.
3. Modifiez les valeurs pour **WorkspaceName**, **ResourceGroupName**, et **WorkspaceLocation**.  La valeur de **WorkspaceName** est l’ID de ressource complet de votre espace de travail Log Analytics, incluant le nom de l’espace de travail, et la valeur de **WorkspaceLocation** correspond à la région au sein de laquelle l’espace de travail est défini.
4. Vous êtes prêt à déployer ce modèle à l’aide de la commande PowerShell suivante :

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Le changement de configuration peut prendre quelques minutes. Lorsqu’il est terminé, un message similaire à celui-ci s’affiche avec les résultats :

    ```powershell
    provisioningState       : Succeeded
    ```
Une fois que vous avez activé la surveillance, 10 minutes peuvent s’écouler avant que vous ne puissiez voir les métriques et l’état d’intégrité pour l’ordinateur hybride. 

## <a name="next-steps"></a>Étapes suivantes

Une fois la surveillance activée pour votre machine virtuelle, ces informations sont disponibles pour être analysées par Azure Monitor pour machines virtuelles.  Pour savoir comment utiliser la fonctionnalité d’intégrité, consultez [Afficher l’intégrité d’Azure Monitor pour machines virtuelles](monitoring-vminsights-health.md), ou pour afficher les dépendances des applications découvertes, consultez [Afficher Azure Monitor pour machines virtuelles Map](monitoring-vminsights-maps.md).  