---
title: Déployer un Runbook Worker hybride Windows dans Azure Automation
description: Cet article décrit comment déployer un Runbook Worker hybride qui vous permet d’exécuter des runbooks sur des ordinateurs Windows de votre centre de données local ou de votre environnement cloud.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: 6913ae8cbd8c73bd2763bd89172280feee9df973
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185617"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Déployer un Runbook Worker hybride Windows

Vous pouvez utiliser la fonctionnalité Runbook Worker hybride d’Azure Automation pour exécuter des runbooks directement sur la machine qui héberge le rôle et sur les ressources de l’environnement afin de gérer ces ressources locales. Azure Automation stocke et gère les runbooks, puis les remet à une ou plusieurs machines désignées. Cet article explique comment déployer un Runbook Worker hybride sur une machine Windows, comment supprimer le Worker et comment supprimer un groupe Runbook Worker hybride.

Une fois le Runbook Worker déployé, consultez [Exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre à configurer vos Runbooks en vue d’automatiser les processus de votre centre de données local ou autre environnement cloud.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants.

### <a name="a-log-analytics-workspace"></a>Un espace de travail Log Analytics

Le rôle Runbook Worker hybride dépend d’un espace de travail Azure Monitor Log Analytics pour l’installation et la configuration du rôle. Vous pouvez le créer en utilisant [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), [PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) ou le [portail Azure](../azure-monitor/learn/quick-create-workspace.md).

Si vous n’avez pas d’espace de travail Azure Monitor Log Analytics, consultez le [guide de conception des journaux Azure Monitor](../azure-monitor/platform/design-logs-deployment.md) avant de créer l’espace de travail.

Si vous avez déjà un espace de travail, mais que celui-ci n’est pas lié à votre compte Automation, l’activation d’une fonctionnalité Automation ajoute certaines fonctionnalités pour Azure Automation, comme la prise en charge de la fonctionnalité Runbook Worker hybride. Quand vous activez l’une des fonctionnalités Azure Automation dans votre espace de travail Log Analytics, notamment [Update Management](automation-update-management.md) ou [Change Tracking and Inventory](change-tracking.md), les composants Worker sont automatiquement poussés vers la machine agent.

   Pour ajouter la fonctionnalité Update Management à votre espace de travail, exécutez l’applet de commande PowerShell suivante :

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   Pour ajouter la fonctionnalité Change Tracking and Inventory à votre espace de travail, exécutez l’applet de commande PowerShell suivante :

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Agent Log Analytics

Le rôle Runbook Worker hybride requiert l’[agent Log Analytics](../azure-monitor/platform/log-analytics-agent.md) pour le système d’exploitation Windows pris en charge.

### <a name="supported-windows-operating-system"></a>Système d’exploitation Windows pris en charge

Les versions suivantes du système d’exploitation Windows sont officiellement prises en charge pour un Runbook Workers hybride Windows :

* Windows Server 2019
* Windows Server 2016, versions 1709 et 1803
* Windows Server 2012, 2012 R2
* Windows Server 2008 SP2 (x64), 2008 R2
* Windows 10 entreprise (y compris multi-sessions) et Pro
* Windows 8 Entreprise et Professionnel
* Windows 7 SP1

### <a name="minimum-requirements"></a>Configuration minimale requise

Voici la configuration minimale requise pour un Runbook Worker hybride Windows :

* Windows PowerShell 5.1 ou version ultérieure ([télécharger WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))
* .NET Framework 4.6.2 ou version ultérieure
* 2 cœurs
* 4 Go de RAM
* Port 443 (sortie)

### <a name="network-configuration"></a>Configuration réseau

Pour connaître les autres exigences relatives au réseau pour le Runbook Worker hybride, consultez [Configurer votre réseau](automation-hybrid-runbook-worker.md#network-planning).

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Ajout d’un ordinateur à un groupe de Runbook Workers hybrides

Vous pouvez ajouter l’ordinateur du Worker à un groupe de Runbooks Workers hybrides inclus dans votre compte Automation. Notez que vous devez prendre en charge les runbooks Automation tant que vous utilisez le même compte pour la fonctionnalité Azure Automation et l’appartenance à des groupes de runbooks Workers hybrides. Cette fonctionnalité a été ajoutée à la version 7.2.12024.0 du groupe de Runbooks Workers hybrides.

>[!NOTE]
>L’activation d’une solution [Update Management](automation-update-management.md) Azure Automation configure automatiquement tout ordinateur Windows connecté à votre espace de travail Log Analytics en tant que Runbook Worker hybride pour prendre en charge les mises à jour de son système d’exploitation. Toutefois, ce Worker n’est inscrit auprès d’aucun des groupes de runbooks Workers hybrides déjà définis dans votre compte Automation.

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>Activation de machines pour la gestion avec Azure Automation State Configuration

Pour plus d’informations sur l’activation d’ordinateurs en vue d’une gestion avec Azure Automation State Configuration, consultez [Activer la gestion des machines par Azure Automation State Configuration](automation-dsc-onboarding.md).

> [!NOTE]
> Pour gérer la configuration des machines qui prennent en charge le rôle Runbook Worker hybride avec Desired State Configuration (DSC), vous devez ajouter ces machines en tant que nœuds DSC.

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Options d’installation du Runbook Worker hybride Windows

Pour installer et configurer un Runbook Worker hybride Windows, vous pouvez utiliser l’une des méthodes suivantes.

* Pour les machines virtuelles Azure, installez l’agent Log Analytics pour Windows à l’aide de l’[extension de machine virtuelle pour Windows](../virtual-machines/extensions/oms-windows.md). L’extension installe l’agent Log Analytics sur des machines virtuelles Azure et inscrit des machines virtuelles dans un espace de travail Log Analytics existant à l’aide d’un modèle Azure Resource Manager ou de PowerShell. Une fois l’agent installé, la machine virtuelle peut être ajoutée à un groupe de runbooks Workers hybrides dans votre compte Automation.

* Pour les machines virtuelles non Azure, installez l’agent Log Analytics pour Windows avec les options de déploiement décrites dans l’article [Connecter des ordinateurs Windows à Azure Monitor](../azure-monitor/platform/agent-windows.md). Vous pouvez répéter ce processus pour plusieurs machines afin d’ajouter plusieurs Workers à votre environnement. Une fois l’agent installé, les machines virtuelles peuvent être ajoutées à un groupe Runbook Worker hybride dans votre compte Automation.

* Utilisez un script PowerShell fourni pour [automatiser](#automated-deployment) complètement le processus de configuration d’un ou plusieurs ordinateurs Windows. Il s’agit de la méthode recommandée pour les machines de votre centre de données ou d’un autre environnement cloud.

## <a name="automated-deployment"></a>Déploiement automatisé

Sur l’ordinateur cible, procédez comme suit pour automatiser l’installation et la configuration du rôle Worker hybride Windows à l’aide du script PowerShell **New-onpremisehybridworker.ps1**. Le script effectue les étapes suivantes :

* Installe les modules nécessaires
* Se connecte votre compte Azure
* Vérifie l’existence du groupe de ressources spécifié et du compte Automation
* Crée des références à des attributs de compte Automation
* Crée un espace de travail Azure Monitor Log Analytics s’il n’est pas spécifié
* Activer la solution Azure Automation dans l’espace de travail
* Télécharger et Installer l’agent Log Analytics pour Windows
* Inscrire la machine en tant que Runbook Worker hybride

### <a name="step-1---download-the-powershell-script"></a>Étape 1 : Télécharger le script PowerShell

Téléchargez le script **New-OnPremiseHybridWorker.ps1** à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker). Une fois que vous avez téléchargé le script, copiez-le ou exécutez-le sur la machine cible. Le script **New-OnPremiseHybridWorker.ps1** utilise les paramètres décrits ci-dessous pendant l’exécution.

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
> Lors de l’activation de fonctionnalités, Azure Automation prend uniquement en charge certaines régions pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Pour obtenir la liste des paires de mappages prises en charge, consultez [Mappage de régions pour un compte Automation et un espace de travail Log Analytics](how-to/region-mappings.md).

### <a name="step-2---open-windows-powershell-command-line-shell"></a>Étape 2 : Ouvrir un interpréteur de commandes Windows PowerShell

Dans le **Menu Démarrer**, cliquez sur **Démarrer**, tapez **PowerShell**, cliquez avec le bouton droit sur **Windows PowerShell**, puis cliquez sur **Exécuter en tant qu’administrateur**.

### <a name="step-3---run-the-powershell-script"></a>Étape 3 : Exécuter le script PowerShell

À partir de l’interpréteur de commandes PowerShell, accédez au dossier contenant le script que vous avez téléchargé. Modifiez les valeurs des paramètres `AutomationAccountName`, `AAResourceGroupName`, `OMSResourceGroupName`, `HybridGroupName`, `SubscriptionID` et `WorkspaceName`. Ensuite, exécutez le script.

Une fois le script exécuté, vous êtes invité à vous authentifier auprès d’Azure. Vous devez vous connecter avec un compte membre du rôle Administrateurs des abonnements et coadministrateur de l’abonnement.

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <nameOfAutomationAccount> -AAResourceGroupName <nameOfResourceGroup>`
-OMSResourceGroupName <nameOfOResourceGroup> -HybridGroupName <nameOfHRWGroup> `
-SubscriptionID <subscriptionId> -WorkspaceName <nameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>Étape 4 : Installer NuGet

Vous êtes invité à accepter l’installation de NuGet et à vous authentifier avec vos informations d’identification Azure. Si vous ne disposez pas de la dernière version de NuGet, vous pouvez le télécharger à partir de la page répertoriant les [versions de distribution NuGet disponibles](https://www.nuget.org/downloads).

### <a name="step-5---verify-the-deployment"></a>Étape 5 : Vérifier le déploiement

Une fois l’exécution du script terminée, la page Groupes Worker hybride de votre compte Automation affiche le nouveau groupe et le nombre de membres. S’il s’agit d’un groupe existant, le nombre de membres est incrémenté. Vous pouvez sélectionner le groupe dans la liste de la page Groupes de Workers hybrides et sélectionner la vignette **Workers hybrides**. La page Workers hybrides indique chaque membre du groupe listé.

## <a name="manual-deployment"></a>Déploiement manuel

Pour installer et configurer un runbook Worker hybride pour Windows, effectuez les étapes suivantes.

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>Étape 1 : vérifier que l’agent rend compte à l’espace de travail

L’agent Log Analytics pour Windows connecte les ordinateurs à un espace de travail Log Analytics Azure Monitor. Lorsque vous installez l’agent sur votre machine et que vous le connectez à votre espace de travail, il télécharge automatiquement les composants nécessaires au runbook Worker hybride.

Lorsque l’agent s’est correctement connecté à votre espace de travail Log Analytics, après quelques minutes, vous pouvez exécuter la requête suivante pour vérifier qu’il envoie les données de pulsation à l’espace de travail.

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

Dans les résultats de la recherche, vous voyez en principe des enregistrements de pulsation pour la machine, ce qui indique que l’agent est connecté et qu’il rend compte au service. Par défaut, chaque agent transmet un enregistrement de pulsation à l’espace de travail qui lui est attribué. Utilisez les étapes suivantes pour effectuer l’installation et la configuration de l’agent.

1. Activez la fonctionnalité pour ajouter l’ordinateur agent. Pour la solution Update Management et les machines virtuelles Azure, consultez [Activer les machines virtuelles Azure](automation-onboard-solutions-from-automation-account.md#enable-azure-vms) et , pour les machines virtuelles non Azure, consultez [Activer des machines dans l’espace de travail](automation-onboard-solutions-from-automation-account.md#enable-machines-in-the-workspace). Pour la solution Change Tracking et les machines virtuelles Azure, consultez [Activer les machines virtuelles Azure](automation-enable-changes-from-auto-acct.md#enable-azure-vms) et, pour les machines virtuelles non Azure, consultez [Activer des machines dans l’espace de travail](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace).

2. Pour confirmer la version du Runbook Worker hybride, accédez à `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` et notez la **version** du sous-dossier.

### <a name="step-3---install-the-runbook-environment-and-connect-to-azure-automation"></a>Étape 3 : installer l’environnement du runbook et se connecter à Azure Automation

Quand vous configurez un agent pour qu’il envoie des rapports à un espace de travail Log Analytics, la solution Azure Automation envoie (push) le module `HybridRegistration` PowerShell, qui contien le cmdlet`Add-HybridRunbookWorker`. Vous utilisez cette cmdlet pour installer l’environnement du Runbook sur la machine et l’inscrire auprès d’Azure Automation.

Ouvrez une session PowerShell en mode administrateur et exécutez les commandes suivantes pour importer le module.

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Exécutez maintenant l’applet de commande `Add-HybridRunbookWorker` à l’aide de la syntaxe suivante.

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

Vous trouverez les informations requises pour les paramètres `Url` et `Key` dans la page **Clés** de votre compte Automation. Sélectionnez **Clés** sous la section **Paramètres du compte** sur le côté gauche de la page.

![Page Gérer les clés](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* Pour le paramètre `Url`, copiez la valeur de l’**URL**.

* Pour le paramètre `Key`, copiez la valeur de la clé d’accès principale (**PRIMARY ACCESS KEY**).

* Pour le paramètre `GroupName`, utilisez le nom du groupe de Runbooks Workers hybrides. Si ce groupe existe déjà dans le compte Automation, la machine active y est ajoutée. Si le groupe n’existe pas, il est ajouté.

* Si nécessaire, définissez le paramètre `Verbose` pour recevoir des détails sur l’installation.

### <a name="step-4----install-powershell-modules"></a>Étape 4 : installer des modules PowerShell

Les Runbooks peuvent utiliser toutes les activités et applets de commande définies dans les modules installés dans votre environnement Azure Automation. Comme ces modules ne sont pas automatiquement déployés sur des ordinateurs locaux, vous devez les installer manuellement. L’exception est le module Azure. Le module est installé par défaut et permet d’accéder aux cmdlets de l’ensemble des services et activités Azure pour Azure Automation.

Comme l’objectif principal de Runbook Worker hybride est de gérer les ressources locales, vous devrez probablement installer les modules qui prennent en charge ces ressources, en particulier le module `PowerShellGet`. Pour plus d’informations sur l’installation des modules Windows PowerShell, consultez [Windows PowerShell](/powershell/scripting/developer/windows-powershell).

Les modules installés doivent se trouver dans un emplacement référencé par la variable d’environnement `PSModulePath` afin d’être importés automatiquement par le Worker hybride. Pour plus d’informations, consultez [Installer des modules dans PSModulePath](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7).

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>Supprimer la fonctionnalité Runbook Worker hybride d’un ordinateur Windows local

1. Dans le portail Azure, accédez à votre compte Automation.

2. Sous **Paramètres du compte**, sélectionnez **Clés**, puis notez les valeurs des champs **URL** et **Clé d’accès primaire**.

3. Ouvrez une session PowerShell en mode administrateur et exécutez la commande suivante avec votre URL et les valeurs de clé d'accès principal. Utilisez le paramètre `Verbose` pour afficher un journal détaillé du processus de suppression. Pour supprimer des ordinateurs obsolètes à partir de votre groupe Worker hybride, utilisez le paramètre facultatif `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>Supprimer un groupe de Workers hybrides

Pour supprimer un groupe de Runbooks Workers hybrides, vous commencer par supprimer les Runbooks Workers hybrides de chaque ordinateur membre du groupe. Ensuite, suivez les étapes ci-dessous pour supprimer le groupe :

1. Dans le portail Azure, ouvrez le compte Automation.

2. Sélectionnez **Groupes Worker hybride** sous **Automatisation des processus**. Sélectionnez le groupe à supprimer. La page Propriétés du groupe s’affiche.

   ![Page Propriétés](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Dans la page Propriétés du groupe sélectionné, cliquez sur **Supprimer**. Un message vous invite à confirmer cette action. Si vous êtes sûr de vouloir continuer, sélectionnez **Oui**.

   ![Message de confirmation](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Ce processus peut prendre plusieurs secondes. Vous pouvez suivre la progression sous **Notifications** dans le menu.

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).

* Pour savoir comment résoudre les problèmes de vos Runbook Workers hybrides, consultez [Résoudre les problèmes liés à la fonctionnalité Runbook Worker hybride](troubleshoot/hybrid-runbook-worker.md#general).
