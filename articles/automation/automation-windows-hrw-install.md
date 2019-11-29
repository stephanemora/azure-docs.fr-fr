---
title: Runbook Worker hybride Windows Azure Automation
description: Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet d’exécuter des Runbooks sur les machines Windows de votre centre de données local ou de votre environnement cloud.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd599fcfe403d64483e6b4db869b93b26f5db754
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480807"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Déployer un Runbook Worker hybride Windows

Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés. Cet article explique comment installer le Runbook Worker hybride sur une machine Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Installation de la fonctionnalité Windows Runbook Worker hybride

Il existe deux méthodes pour installer et configurer un Runbook Worker hybride Windows. La méthode recommandée utilise un runbook Automation afin d’automatiser totalement le processus de configuration d’un ordinateur Windows. La deuxième méthode consiste à suivre la procédure pas à pas pour installer et configurer le rôle manuellement.

> [!NOTE]
> Pour gérer la configuration des serveurs qui prennent en charge le rôle Runbook Worker hybride avec Desired State Configuration (DSC), vous devez les ajouter en tant que nœuds DSC.

Voici la configuration minimale requise pour un Runbook Worker hybride Windows :

* Windows Server 2012 ou version ultérieure.
* Windows PowerShell 5.1 ou version ultérieure ([télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 ou ultérieur.
* 2 cœurs
* 4 Go de RAM
* Port 443 (sortie)

Pour connaître les autres exigences relatives au réseau pour le Runbook Worker hybride, consultez [Configurer votre réseau](automation-hybrid-runbook-worker.md#network-planning).

Pour plus d’informations sur l’intégration de serveurs en vue d’une gestion avec DSC, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).
Si vous activez la [solution Update Management](../operations-management-suite/oms-solution-update-management.md), tout ordinateur Windows connecté à votre espace de travail Azure Log Analytics est automatiquement configuré en tant que Runbook Worker hybride pour prendre en charge les runbooks inclus dans cette solution. Toutefois, il n’est inscrit auprès d’aucun des groupes de Workers hybrides déjà définis dans votre compte Automation. 

L’ordinateur peut être ajouté à un groupe de Runbooks Workers hybrides dans votre compte Automation pour prendre en charge des runbooks Automation, à condition d’utiliser le même compte à la fois pour la solution et pour l’appartenance au groupe de Runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

Une fois le Runbook Worker déployé, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre à configurer vos Runbooks en vue d’automatiser les processus de votre centre de données local ou autre environnement cloud.

### <a name="automated-deployment"></a>Déploiement automatisé

Effectuez les étapes suivantes pour automatiser l’installation et la configuration du rôle Worker hybride Windows :

1. Téléchargez le script New-OnPremiseHybridWorker.ps1 à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker), directement sur l’ordinateur qui exécute le rôle Runbook Worker hybride ou sur un autre ordinateur de votre environnement. Copiez le script dans le Worker.

   Le script New-OnPremiseHybridWorker.ps1 nécessite les paramètres suivants lors de l’exécution :

   * *AutomationAccountName* (obligatoire) : nom de votre compte Automation.
   * *AAResourceGroupName* (obligatoire) : nom du groupe de ressources associé à votre compte Automation.
   * *OMSResourceGroupName* (facultatif) : nom du groupe de ressources pour l’espace de travail Log Analytics. Si ce groupe de ressources n’est pas spécifié, *AAResourceGroupName* est utilisé.
   * *HybridGroupName* (obligatoire) : nom d’un groupe de Runbooks Workers hybrides que vous spécifiez comme cible pour les Runbooks prenant en charge ce scénario.
   * *SubscriptionID* (obligatoire) : ID de l’abonnement Azure dans lequel se trouve votre compte Automation.
   * *WorkspaceName* (facultatif) : Nom de l’espace de travail Log Analytics. Si vous n’avez pas d’espace de travail Log Analytics, le script en crée un et le configure.

   > [!NOTE]
   > Lors de l’activation de solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation.
   >
   > Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de région pour un compte Automation et l’espace de travail Log Analytics](how-to/region-mappings.md).

2. Sur votre ordinateur, ouvrez **Windows PowerShell** à partir de l’écran **Démarrer** en mode Administrateur.
3. À partir de l’interface de ligne de commande PowerShell, accédez au dossier contenant le script que vous avez téléchargé. Modifiez les valeurs des paramètres *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId* et *-WorkspaceName*. Ensuite, exécutez le script.

     > [!NOTE]
     > Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous *devez* vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Vous êtes invité à accepter l’installation de NuGet et à vous authentifier avec vos informations d’identification Azure.

5. Une fois le script exécuté, la page **Groupes Worker hybride** affiche le nouveau groupe et le nombre de membres. S’il s’agit d’un groupe existant, le nombre de membres est incrémenté. Vous pouvez sélectionner le groupe dans la liste de la page **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**. La page **Workers hybrides** affiche chaque membre du groupe listé.

### <a name="manual-deployment"></a>Déploiement manuel

Effectuez les deux premières étapes pour votre environnement Automation, puis répétez les étapes restantes pour chaque ordinateur Worker.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. Créer un espace de travail Log Analytics

Si vous ne disposez pas déjà d’un espace de travail Log Analytics, créez-en un en suivant les instructions de l’article [Gestion des espaces de travail](../azure-monitor/platform/manage-access.md). Vous pouvez utiliser un espace de travail existant si vous en avez déjà un.

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Ajouter une solution Automation à l’espace de travail Log Analytics

La solution de journaux Azure Monitor Automation ajoute des fonctionnalités à Azure Automation, notamment la prise en charge des Runbooks Workers hybrides. Lorsque vous ajoutez la solution à votre espace de travail, les composants Worker sont automatiquement transférés à l’ordinateur agent que vous allez installer à l’étape suivante.

Pour ajouter la solution de journaux Azure Monitor **Automation** à votre espace de travail, exécutez la commande PowerShell suivante.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3. Installation de Microsoft Monitoring Agent

Microsoft Monitoring Agent connecte les ordinateurs aux journaux Azure Monitor. Lorsque vous installez l’agent sur votre ordinateur local et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants nécessaires pour le Runbook Worker hybride.

Pour installer l’agent sur l’ordinateur local, suivez les instructions de l’article [Connecter des ordinateurs Windows aux journaux Azure Monitor](../log-analytics/log-analytics-windows-agent.md). Vous pouvez répéter ce processus pour plusieurs ordinateurs afin d’ajouter plusieurs Workers à votre environnement.

Lorsque l’agent parvient à se connecter aux journaux Azure Monitor, il est répertorié sous l’onglet **Sources connectées** de la page **Paramètres** de Log Analytics. Vous pouvez vérifier que l’agent a correctement téléchargé la solution Automation lorsqu’un dossier appelé **AzureAutomationFiles** figure dans C:\Program Files\Microsoft Monitoring Agent\Agent. Pour vérifier la version du Runbook Worker hybride, vous pouvez accéder à C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ et consulter le sous-dossier \\*version*.

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. Installer l'environnement de Runbook et se connecter à Azure Automation

Lorsque vous ajoutez un agent aux journaux Azure Monitor, la solution Automation déclenche le module PowerShell **HybridRegistration**, qui contient la cmdlet **Add-HybridRunbookWorker**. Vous utilisez cette applet de commande pour installer l'environnement de Runbook sur la machine et l'inscrire auprès d'Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module :

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Exécutez ensuite l’applet de commande **Add-HybridRunbookWorker** en utilisant la syntaxe suivante :

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Vous pouvez obtenir les informations requises pour cette applet de commande dans la page **Gérer les clés** du portail Azure. Ouvrez cette page en sélectionnant l’option **Clés** sur la page **Paramètres** de votre compte Automation.

![Page Gérer les clés](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** est le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, l’ordinateur est ajouté à ce dernier. Si le groupe n’existe pas, il est ajouté.
* **EndPoint** correspond à l’entrée **URL** de la page **Gérer les clés**.
* **Token** correspond à l’entrée **Clé d’accès primaire** de la page **Gérer les clés**.

Pour recevoir des informations détaillées concernant l’installation, utilisez le commutateur **-Verbose** avec **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. Installer des modules PowerShell

Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules qui sont installés dans votre environnement Azure Automation. Toutefois, comme ces modules ne sont pas automatiquement déployés sur les ordinateurs locaux, vous devez les installer manuellement. L’exception est le module Azure, qui est installé par défaut et qui permet d’accéder aux applets de commande de l’ensemble des services et activités Azure pour Azure Automation.

Étant donné que l’objectif principal de la fonctionnalité Runbook Worker hybride est de gérer les ressources locales, vous devrez probablement installer les modules qui prennent en charge ces ressources. Pour plus d’informations sur l’installation des modules Windows PowerShell, consultez [Installation de modules](/powershell/scripting/developer/windows-powershell). 

Les modules installés doivent se trouver dans un emplacement référencé par la variable d’environnement **PSModulePath** afin d’être importés automatiquement par le Worker hybride. Pour plus d’informations, consultez la page [Modifier le chemin d’Installation de PSModulePath](/powershell/scripting/developer/windows-powershell).

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour obtenir des instructions sur la suppression des Runbooks Worker hybrides, consultez la rubrique [Supprimer des Runbooks Worker hybrides Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Pour savoir comment résoudre les problèmes de vos Runbooks Workers hybrides, consultez [Résolution des problèmes relatifs aux Runbooks Workers hybrides Windows](troubleshoot/hybrid-runbook-worker.md#windows).
* Pour obtenir des instructions supplémentaires sur la façon de résoudre les problèmes liés à Update Management, consultez la rubrique [Update Management : résolution des problèmes](troubleshoot/update-management.md).
