---
title: Runbook Worker hybride Windows Azure Automation
description: Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet d’exécuter des Runbooks sur les machines Windows de votre centre de données local ou de votre environnement cloud.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: a6d2e2d912f176a88dc993803d750e37cff1acb6
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443662"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Déployer un Runbook Worker hybride Windows

Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés. Cet article explique comment déployer le Runbook Worker hybride sur une machine Windows.

Une fois le Runbook Worker déployé, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre à configurer vos Runbooks en vue d’automatiser les processus de votre centre de données local ou autre environnement cloud.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Installation et configuration du Runbook Worker hybride Windows

Pour installer et configurer un Runbook Worker hybride Windows, vous pouvez utiliser l’une des méthodes suivantes.

* Pour les machines virtuelles Azure, vous installez l’agent de Log Analytics pour Windows à l’aide de l’[extension de machine virtuelle pour Windows](../virtual-machines/extensions/oms-windows.md). L’extension installe l’agent Log Analytics sur des machines virtuelles Azure et inscrit des machines virtuelles dans un espace de travail Log Analytics existant à l’aide d’un modèle Azure Resource Manager ou PowerShell. Une fois l’agent installé, la machine virtuelle peut être ajoutée à un groupe Runbook Worker hybride dans votre compte Automation à la suite de l’**étape 4** dans la section [Déploiement manuel](#manual-deployment) ci-dessous.

* Utilisez un runbook Automation pour automatiser complètement le processus de configuration d’un ordinateur Windows. Il s’agit de la méthode recommandée pour les ordinateurs de votre centre de données ou d’un autre environnement Cloud.

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
Si vous activez la [solution Update Management](../operations-management-suite/oms-solution-update-management.md), tout ordinateur Windows connecté à votre espace de travail Azure Log Analytics est automatiquement configuré en tant que Runbook Worker hybride pour prendre en charge les runbooks inclus dans cette solution. Toutefois, il n’est inscrit auprès d’aucun des groupes de Workers hybrides déjà définis dans votre compte Automation. 

### <a name="adding-the-computer-to-a-hybrid-runbook-worker-group"></a>Ajout de l’ordinateur à un groupe de Runbooks Workers hybrides

L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation, à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

## <a name="automated-deployment"></a>Déploiement automatisé

Sur l’ordinateur cible, procédez comme suit pour automatiser l’installation et la configuration du rôle Worker hybride Windows.

### <a name="1-download-the-powershell-script"></a>1. Télécharger le script PowerShell

Téléchargez le script New-OnPremiseHybridWorker.ps1 à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker), directement sur l’ordinateur qui exécute le rôle Runbook Worker hybride ou sur un autre ordinateur de votre environnement. Copiez le script dans le Worker. Le script New-OnPremiseHybridWorker.ps1 nécessite les paramètres suivants lors de l’exécution :

   * *AAResourceGroupName* (obligatoire) : nom du groupe de ressources associé à votre compte Automation.
   * *OMSResourceGroupName* (facultatif) : nom du groupe de ressources pour l’espace de travail Log Analytics. Si ce groupe de ressources n’est pas spécifié, *AAResourceGroupName* est utilisé.
   * *SubscriptionID* (obligatoire) : ID de l’abonnement Azure dans lequel se trouve votre compte Automation.
   * *TenantID* (facultatif) : Identificateur de l’organisation locataire associée à votre compte Automation.
   * *WorkspaceName* (facultatif) : Nom de l’espace de travail Log Analytics. Si vous n’avez pas d’espace de travail Log Analytics, le script en crée un et le configure.
   * *AutomationAccountName* (obligatoire) : Nom de votre compte Automation.
   * *HybridGroupName* (obligatoire) : nom d’un groupe de Runbooks Workers hybrides que vous spécifiez comme cible pour les Runbooks prenant en charge ce scénario.
   * *Credential* (facultatif) : Informations d’identification à utiliser lors de la connexion à l’environnement Azure.
  
   > [!NOTE]
   > Lors de l’activation de solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation.
   >
   > Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de région pour un compte Automation et l’espace de travail Log Analytics](how-to/region-mappings.md).

### <a name="2-open-windows-powershell-command-line-shell"></a>2. Ouvrir un interpréteur de ligne de commande Commande Windows PowerShell

Ouvrez **Windows PowerShell** à partir de l’écran **Démarrer** en mode Administrateur.

### <a name="3-run-the-powershell-script"></a>3. Exécution du script PowerShell

À partir de l’interpréteur de ligne de commande PowerShell, accédez au dossier contenant le script que vous avez téléchargé. Modifiez les valeurs des paramètres *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* et *-WorkspaceName*. Ensuite, exécutez le script.

Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="4-install-nuget"></a>4. Installer NuGet

Vous êtes invité à accepter l’installation de NuGet et à vous authentifier avec vos informations d’identification Azure. Si vous ne disposez pas de la dernière version de NuGet, vous pouvez l’obtenir à partir de la page répertoriant les [versions de distribution NuGet disponibles](https://www.nuget.org/downloads).

### <a name="5-verify-the-deployment"></a>5. Vérifier le déploiement

Une fois le script exécuté, la page **Groupes Worker hybride** affiche le nouveau groupe et le nombre de membres. S’il s’agit d’un groupe existant, le nombre de membres est incrémenté. Vous pouvez sélectionner le groupe dans la liste de la page **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**. La page **Workers hybrides** affiche chaque membre du groupe listé.

## <a name="manual-deployment"></a>Déploiement manuel

Sur l’ordinateur cible, effectuez les deux premières étapes une fois pour votre environnement Automation. Ensuite, effectuez les étapes restantes pour chaque ordinateur Worker.

### <a name="1-create-a-log-analytics-workspace"></a>1. Créer un espace de travail Log Analytics

Si vous ne disposez pas déjà d’un espace de travail Log Analytics, commencez par consulter le [guide de conception de journal Azure Monitor](../azure-monitor/platform/design-logs-deployment.md) avant de créer un espace de travail.

### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Ajouter une solution Automation à l’espace de travail Log Analytics

La solution Automation ajoute des fonctionnalités à Azure Automation, notamment la prise en charge des Runbooks Workers hybrides. Lorsque vous ajoutez la solution à votre espace de travail Log Analytics, elle envoie automatiquement les composants Worker à l’ordinateur agent que vous allez installer à l’étape suivante.

Pour ajouter la solution **Automation** à votre espace de travail, exécutez la cmdlet PowerShell suivante.

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. Installer l’agent Log Analytics pour Windows

L’agent Log Analytics pour Windows connecte les ordinateurs à un espace de travail Log Analytics Azure Monitor. Lorsque vous installez l’agent sur votre ordinateur et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants requis pour le Runbook Worker hybride.

Pour installer l’agent sur l’ordinateur, suivez les instructions de l’article [Connecter des ordinateurs Windows aux journaux Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Vous pouvez répéter ce processus pour plusieurs ordinateurs afin d’ajouter plusieurs Workers à votre environnement.

Lorsque l’agent s’est correctement connecté à votre espace de travail Log Analytics, après quelques minutes, vous pouvez exécuter la requête suivante pour vérifier qu’il envoie les données de pulsation à l’espace de travail :

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Dans les résultats de recherche, vous devez voir des enregistrements de pulsation pour l’ordinateur, indiquant qu’il est connecté et qu’il rend compte au service. Par défaut, chaque agent transmet un enregistrement de pulsation à l’espace de travail qui lui est attribué. Vous pouvez vérifier que l’agent a correctement téléchargé la solution Automation lorsqu’un dossier appelé AzureAutomationFiles figure dans C:\Program Files\Microsoft Monitoring Agent\Agent. Pour vérifier la version du Runbook Worker hybride, accédez à C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ et notez le sous-dossier \\*version*.

### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installer l'environnement de Runbook et se connecter à Azure Automation

Quand vous configurez un agent pour qu’il envoie des rapports à un espace de travail Log Analytics, la solution Automation envoie (push) le module HybridRegistration PowerShell, qui contient la cmdlet **Add-HybridRunbookWorker**. Vous utilisez cette cmdlet pour installer l’environnement du Runbook sur la machine et l’inscrire auprès d’Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Exécutez ensuite la cmdlet **Add-HybridRunbookWorker** en utilisant la syntaxe suivante.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Vous pouvez obtenir les informations requises pour cette cmdlet dans la page « Gérer les clés » du Portail Azure. Ouvrez cette page en sélectionnant l’option **Clés** sur la page **Paramètres** de votre compte Automation.

![Page Gérer les clés](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pour le paramètre *GroupName*, utilisez le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, l’ordinateur est ajouté à ce dernier. Si le groupe n’existe pas, il est ajouté.
* Pour le paramètre *EndPoint*, utilisez l’entrée **URL** de la page Gérer les clés.
* Pour le paramètre *Token*, utilisez l’entrée **PRIMARY ACCESS KEY** de la page Gérer les clés.

Pour recevoir des informations détaillées concernant l’installation, utilisez le commutateur *-Verbose* avec **Add-HybridRunbookWorker**.

### <a name="5-install-powershell-modules"></a>5. Installer des modules PowerShell

Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules qui sont installés dans votre environnement Azure Automation. Toutefois, comme ces modules ne sont pas automatiquement déployés sur les ordinateurs locaux, vous devez les installer manuellement. L’exception est le module Azure. Le module est installé par défaut et permet d’accéder aux cmdlets de l’ensemble des services et activités Azure pour Azure Automation.

Étant donné que l’objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devrez probablement installer les modules qui prennent en charge ces ressources, en particulier le module PowerShellGet. Pour plus d’informations sur l’installation des modules Windows PowerShell, consultez [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell).

Les modules installés doivent se trouver dans un emplacement référencé par la variable d’environnement PSModulePath afin d’être importés automatiquement par le Worker hybride. Pour plus d’informations, consultez [Installer des modules dans PSModulePath](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour obtenir des instructions sur la suppression des Runbooks Worker hybrides, consultez la rubrique [Supprimer des Runbooks Worker hybrides Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Pour savoir comment résoudre les problèmes de vos Runbooks Workers hybrides, consultez [Résolution des problèmes relatifs aux Runbooks Workers hybrides Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Pour obtenir des instructions supplémentaires sur la façon de résoudre les problèmes liés à Update Management, consultez la rubrique [Update Management : résolution des problèmes](troubleshoot/update-management.md).
