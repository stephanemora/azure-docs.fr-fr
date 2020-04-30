---
title: Runbook Worker hybride Windows Azure Automation
description: Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet d’exécuter des Runbooks sur les machines Windows de votre centre de données local ou de votre environnement cloud.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617341"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Déployer un Runbook Worker hybride Windows

Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Azure Automation stocke et gère les runbooks, puis les remet à un ou plusieurs ordinateurs désignés. Cet article explique comment déployer un runbook Worker hybride sur une machine Windows.

Une fois le Runbook Worker déployé, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre à configurer vos Runbooks en vue d’automatiser les processus de votre centre de données local ou autre environnement cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installation et configuration du Runbook Worker hybride Windows

Pour installer et configurer un Runbook Worker hybride Windows, vous pouvez utiliser l’une des méthodes suivantes.

* Pour les machines virtuelles Azure, installez l’agent Log Analytics pour Windows à l’aide de l’[extension de machine virtuelle pour Windows](../virtual-machines/extensions/oms-windows.md). L’extension installe l’agent Log Analytics sur des machines virtuelles Azure et inscrit des machines virtuelles dans un espace de travail Log Analytics existant à l’aide d’un modèle Azure Resource Manager ou de PowerShell. Une fois l’agent installé, la machine virtuelle peut être ajoutée à un groupe de runbooks Workers hybrides dans votre compte Automation. Reportez-vous aux étapes 3 et 4 de la section [Déploiement manuel](#manual-deployment).

* Utilisez un runbook Automation pour automatiser complètement le processus de configuration d’un ordinateur Windows. Il s’agit de la méthode recommandée pour les machines de votre centre de données ou d’un autre environnement cloud.

* Suivez une procédure pas-à-pas pour installer et configurer manuellement le rôle Runbook Worker hybride sur votre machine virtuelle non-Azure.

> [!NOTE]
> Pour gérer la configuration des serveurs qui prennent en charge le rôle Runbook Worker hybride avec Desired State Configuration (DSC), vous devez les ajouter en tant que nœuds DSC.

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Configuration minimale requise pour un Runbook Worker hybride Windows

Voici la configuration minimale requise pour un Runbook Worker hybride Windows :

* Windows Server 2012 ou version ultérieure
* Windows PowerShell 5.1 ou version ultérieure ([télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou version ultérieure
* 2 cœurs
* 4 Go de RAM
* Port 443 (sortie)

### <a name="network-configuration"></a>Configuration réseau

Pour connaître les autres exigences relatives au réseau pour le Runbook Worker hybride, consultez [Configurer votre réseau](automation-hybrid-runbook-worker.md#network-planning).

### <a name="server-onboarding-for-management-with-automation-dsc"></a>Intégration de serveur pour la gestion via Automation DSC

Pour plus d’informations sur l’intégration de serveurs en vue d’une gestion avec DSC, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).

L’activation de la [solution Update Management](../operations-management-suite/oms-solution-update-management.md) configure automatiquement tout ordinateur Windows connecté à votre espace de travail Log Analytics en tant que runbook Worker hybride pour prendre en charge les runbooks inclus dans la solution. Toutefois, ce Worker n’est inscrit auprès d’aucun des groupes de runbooks Workers hybrides déjà définis dans votre compte Automation.

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>Ajout de l’ordinateur à un groupe de runbooks Workers hybrides

Vous pouvez ajouter l’ordinateur du Worker à un groupe de runbooks Workers hybrides inclus dans votre compte Automation. Notez que vous devez prendre en charge les runbooks Automation tant que vous utilisez le même compte pour la solution et l’appartenance à des groupes de runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

## <a name="automated-deployment"></a>Déploiement automatisé

Sur l’ordinateur cible, procédez comme suit pour automatiser l’installation et la configuration du rôle Worker hybride Windows.

### <a name="step-1---download-the-powershell-script"></a>Étape 1 : Télécharger le script PowerShell

Téléchargez le script **New-OnPremiseHybridWorker.ps1** à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Le téléchargement doit être effectué directement depuis l’ordinateur qui exécute le rôle Runbook Worker hybride ou à partir d’un autre ordinateur de votre environnement. Une fois que vous avez téléchargé le script, copiez-le dans votre Worker. Le script **New-OnPremiseHybridWorker.ps1** utilise les paramètres décrits ci-dessous pendant l’exécution.

| Paramètre | Statut | Description |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | Obligatoire | nom du groupe de ressources associé à votre compte Automation. |
| `AutomationAccountName` | Obligatoire | nom de votre compte Automation.
| `Credential` | Facultatif | Informations d’identification à utiliser lors de la connexion à l’environnement Azure. |
| `HybridGroupName` | Obligatoire | nom d’un groupe de Runbooks Workers hybrides que vous spécifiez comme cible pour les Runbooks prenant en charge ce scénario. |
| `OMSResourceGroupName` | Facultatif | nom du groupe de ressources pour l’espace de travail Log Analytics. Si ce groupe de ressources n’est pas spécifié, la valeur de `AAResourceGroupName` est utilisée. |
| `SubscriptionID` | Obligatoire | Identificateur de l’abonnement Azure associé à votre compte Automation. |
| `TenantID` | Facultatif | Identificateur de l’organisation locataire associée à votre compte Automation. |
| `WorkspaceName` | Facultatif | Nom de l’espace de travail Log Analytics. Si vous n’avez pas d’espace de travail Log Analytics, le script en crée un et le configure. |

> [!NOTE]
> Lors de l’activation de solutions, Azure Automation prend uniquement en charge certaines régions pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Étape 2 : Ouvrir un interpréteur de commandes Windows PowerShell

Ouvrez **Windows PowerShell** à partir de l’écran **Démarrer** en mode Administrateur.

### <a name="step-3---run-the-powershell-script"></a>Étape 3 : Exécuter le script PowerShell

À partir de l’interpréteur de commandes PowerShell, accédez au dossier contenant le script que vous avez téléchargé. Modifiez les valeurs des paramètres `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` et `WorkspaceName`. Ensuite, exécutez le script.

Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Étape 4 : Installer NuGet

Vous êtes invité à accepter l’installation de NuGet et à vous authentifier avec vos informations d’identification Azure. Si vous ne disposez pas de la dernière version de NuGet, vous pouvez l’obtenir à partir de la page répertoriant les [versions de distribution NuGet disponibles](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Étape 5 : Vérifier le déploiement

Une fois le script exécuté, la page Groupes de Workers hybrides affiche le nouveau groupe et le nombre de membres. S’il s’agit d’un groupe existant, le nombre de membres est incrémenté. Vous pouvez sélectionner le groupe dans la liste de la page Groupes de Workers hybrides et sélectionner la vignette **Workers hybrides**. La page Workers hybrides indique chaque membre du groupe listé.

## <a name="manual-deployment"></a>Déploiement manuel

Sur l’ordinateur cible, effectuez les deux premières étapes une fois pour votre environnement Automation. Ensuite, effectuez les étapes restantes pour chaque ordinateur Worker.

### <a name="step-1---create-a-log-analytics-workspace"></a>Étape 1 : Créer un espace de travail Log Analytics

Si vous ne disposez pas déjà d’un espace de travail Log Analytics, consultez le [guide de conception de journal Azure Monitor](../azure-monitor/platform/design-logs-deployment.md) avant de créer l’espace de travail.

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>Étape 2 : Ajouter une solution Automation à l’espace de travail Log Analytics

La solution Automation ajoute des fonctionnalités à Azure Automation, notamment la prise en charge du runbook Worker hybride. Lorsque vous ajoutez la solution à votre espace de travail Log Analytics, elle envoie (push) automatiquement les composants Worker à l’ordinateur agent que vous allez installer de la manière décrite à l’étape suivante.

Pour ajouter la solution Automation à votre espace de travail, exécutez l’applet de commande PowerShell suivante.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>Étape 3 : Installer l’agent Log Analytics pour Windows

L’agent Log Analytics pour Windows connecte les ordinateurs à un espace de travail Log Analytics Azure Monitor. Lorsque vous installez l’agent sur votre ordinateur et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants nécessaires au runbook Worker hybride.

Pour installer l’agent sur l’ordinateur, suivez les instructions de l’article [Connecter des ordinateurs Windows aux journaux Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Vous pouvez répéter ce processus pour plusieurs ordinateurs afin d’ajouter plusieurs Workers à votre environnement.

Lorsque l’agent s’est correctement connecté à votre espace de travail Log Analytics, après quelques minutes, vous pouvez exécuter la requête suivante pour vérifier qu’il envoie les données de pulsation à l’espace de travail.

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Dans les résultats de recherche, vous devez voir des enregistrements de pulsation pour l’ordinateur, indiquant qu’il est connecté et qu’il rend compte au service. Par défaut, chaque agent transmet un enregistrement de pulsation à l’espace de travail qui lui est attribué. 

Utilisez les étapes suivantes pour effectuer l’installation et la configuration de l’agent.

1. Autorisez la solution à intégrer l’ordinateur agent. Consultez [Intégrer des machines dans l’espace de travail](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace).
2. Vérifiez que l’agent a correctement téléchargé la solution Automation. 
3. Pour vérifier la version du runbook Worker hybride, accédez à **C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation** et notez le sous-dossier **version**.

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>Étape 4 : Installer l’environnement du runbook et vous connecter à Azure Automation

Quand vous configurez un agent pour qu’il envoie des rapports à un espace de travail Log Analytics, la solution Automation envoie (push) le module `HybridRegistration` PowerShell, qui contient l’applet de commande `Add-HybridRunbookWorker`. Vous utilisez cette cmdlet pour installer l’environnement du Runbook sur la machine et l’inscrire auprès d’Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Exécutez maintenant l’applet de commande `Add-HybridRunbookWorker` à l’aide de la syntaxe suivante.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Vous pouvez obtenir les informations nécessaires pour cette applet de commande dans la page Gérer les clés du portail Azure. Ouvrez cette page en sélectionnant **Clés** dans la page Paramètres de votre compte Automation.

![Page Gérer les clés](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pour le paramètre `GroupName`, utilisez le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, l’ordinateur est ajouté à ce dernier. Si le groupe n’existe pas, il est ajouté.
* Pour le paramètre `EndPoint`, utilisez l’entrée **URL** de la page Gérer les clés.
* Pour le paramètre `Token`, utilisez l’entrée **PRIMARY ACCESS KEY** de la page Gérer les clés.
* Si nécessaire, définissez le paramètre `Verbose` pour recevoir des détails sur l’installation.

### <a name="step-5----install-powershell-modules"></a>Étape 5 : Installer des modules PowerShell

Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules installés dans votre environnement Azure Automation. Comme ces modules ne sont pas automatiquement déployés sur les ordinateurs locaux, vous devez les installer manuellement. L’exception est le module Azure. Le module est installé par défaut et permet d’accéder aux cmdlets de l’ensemble des services et activités Azure pour Azure Automation.

Étant donné que l’objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devrez probablement installer les modules qui prennent en charge ces ressources, en particulier le module `PowerShellGet`. Pour plus d’informations sur l’installation des modules Windows PowerShell, consultez [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Les modules installés doivent se trouver dans un emplacement référencé par la variable d’environnement `PSModulePath` afin d’être importés automatiquement par le Worker hybride. Pour plus d’informations, consultez [Installer des modules dans PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour obtenir des instructions sur la suppression des runbooks Workers hybrides, consultez [Supprimer des runbooks Workers hybrides Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Pour savoir comment résoudre les problèmes de vos runbooks Workers hybrides, consultez [Résolution des problèmes liés aux runbooks Workers hybrides Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Pour obtenir des instructions supplémentaires afin de résoudre les problèmes liés à la gestion des mises à jour, consultez [Update Management : résolution des problèmes](troubleshoot/update-management.md).
