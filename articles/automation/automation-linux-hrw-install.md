---
title: Déployer un Runbook Worker hybride Linux dans Azure Automation
description: Cet article explique comment déployer un Runbook Worker hybride qui vous permet d’exécuter des runbooks sur les ordinateurs Linux de votre centre de données local ou de votre environnement cloud.
services: automation
ms.subservice: process-automation
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: a6cf348142d694a03da24f32793fc72325701931
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835221"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Déployer un Runbook Worker hybride Linux

Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. La fonctionnalité Runbook Worker hybride Linux exécute des Runbooks en tant qu’utilisateur spécial dont les privilèges peuvent être élevés pour exécuter des commandes nécessitant une élévation. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés.

Cet article explique comment installer le Runbook Worker hybride sur une machine Linux, comment supprimer le Worker et comment supprimer un groupe Runbook Worker hybride.

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

La fonctionnalité de Runbook Worker hybride prend en charge les distributions suivantes :

* Amazon Linux 2012.09 à 2015.09 (x86/x64)
* CentOS Linux 5, 6 et 7 (x86/x64)
* Oracle Linux 5, 6 et 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 et 7 (x86/x64)
* Debian GNU/Linux 6, 7 et 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS et 18.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 et 12 (x86/x64)

## <a name="supported-runbook-types"></a>Types de Runbook pris en charge

Les Runbooks Worker hybrides ne prennent pas en charge la totalité des types de Runbook dans Azure Automation.

Les types de Runbook suivants fonctionnent sur un Worker hybride Linux :

* Python 2
* PowerShell

  > [!NOTE]
  > Les Runbooks PowerShell ont besoin que PowerShell Core soit installé sur l’ordinateur Linux. Consultez la rubrique [Installation de PowerShell Core sous Linux](/powershell/scripting/install/installing-powershell-core-on-linux) pour connaître la procédure d’installation.

Les types de Runbooks suivants ne fonctionnent pas sur un Worker hybride Linux :

* PowerShell Workflow
* Graphique
* Graphique workflow PowerShell

## <a name="deployment-requirements"></a>Composants requis pour le déploiement

Voici la configuration minimale requise pour une fonctionnalité Runbook Worker hybride Linux :

* 2 cœurs
* 4 Go de RAM
* Port 443 (sortie)

### <a name="package-requirements"></a>Packages requis

| **Package requis** | **Description** | **Version minimum**|
|--------------------- | --------------------- | -------------------|
|Glibc |Bibliothèque C de GNU| 2.5-12 |
|Openssl| Bibliothèques OpenSSL | 1.0 (TLS 1.1 et TLS 1.2 sont pris en charge)|
|Curl | Client web cURL | 7.15.5|
|Python-ctypes | Python 2.x est requis |
|PAM | Modules d’authentification enfichable|
| **Package facultatif** | **Description** | **Version minimum**|
| PowerShell Core | Pour exécuter des Runbooks PowerShell, PowerShell doit être installé. Consultez la rubrique [Installation de PowerShell Core sous Linux](/powershell/scripting/install/installing-powershell-core-on-linux) pour savoir comment l’installer.  | 6.0.0 |

## <a name="install-a-linux-hybrid-runbook-worker"></a>Installer un Runbook Worker hybride Linux

L'installation et la configuration de la fonctionnalité Runbook Worker hybride sur un ordinateur Linux est un processus manuel simple. Cela nécessite l’activation du Runbook Worker hybride dans votre espace de travail Azure Log Analytics, puis l’exécution d’un ensemble de commandes pour inscrire l’ordinateur en tant que Worker et l’ajouter à un groupe.

Avant de continuer, notez l’espace de travail Log Analytics lié à votre compte Automation. Notez également la clé primaire de votre compte Automation. Vous trouverez ces deux informations sur le portail Azure en sélectionnant votre compte Automation, l’**espace de travail** correspondant à l’ID d’espace de travail et **Clés** pour la clé primaire. Pour plus d’informations sur les ports et les adresses nécessaires à la fonctionnalité Runbook Worker hybride, consultez la rubrique [Configuration de votre réseau](automation-hybrid-runbook-worker.md#network-planning).

>[!NOTE]
> Le [compte nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) disposant des autorisations sudo correspondantes doit être présent lors de l’installation d’un Worker hybride Linux. Si vous essayez d’installer le Worker et que le compte n’est pas présent ou ne dispose pas des autorisations appropriées, l’installation échoue.

1. Utilisez l’une des méthodes suivantes pour activer le Runbook Worker hybride dans Azure :

   * Ajoutez le Runbook Worker hybride à votre abonnement à l’aide de la procédure décrite dans [Ajouter des journaux Azure Monitor à votre espace de travail](../log-analytics/log-analytics-add-solutions.md).
   * Exécutez l’applet de commande suivante :

        ```azurepowershell-interactive
         Set-AzOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installez l’agent Log Analytics pour Linux en exécutant la commande suivante. Remplacez \<WorkspaceID\> et \<WorkspaceKey\> par les valeurs appropriées de votre espace de travail.

   [!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Exécutez la commande suivante en remplaçant les valeurs des paramètres *-w*, *-k*, *-g* et *-e*. Pour le paramètre *-g*, remplacez la valeur par le nom du groupe Runbook Worker hybride auquel le nouveau Runbook Worker hybride Linux doit se joindre. Si le nom n’existe pas dans votre compte Automation, un nouveau groupe Runbook Worker hybride est constitué avec ce nom.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Une fois la commande exécutée, la page Groupes de Workers hybrides du portail Azure affiche le nouveau groupe et le nombre de membres. S’il s’agit d’un groupe existant, le nombre de membres est incrémenté. Vous pouvez sélectionner le groupe dans la liste de la page Groupes de Workers hybrides et sélectionner la vignette **Workers hybrides**. La page Workers hybrides affiche chacun des membres du groupe listé.

> [!NOTE]
> Si vous utilisez l'extension de machine virtuelle Azure Monitor pour Linux sur une machine virtuelle Azure, nous vous recommandons de définir `autoUpgradeMinorVersion` sur la valeur false, car la mise à niveau automatique des versions peut provoquer des problèmes au niveau du Runbook Worker hybride. Pour savoir comment mettre à niveau l'extension manuellement, consultez [Déploiement Azure CLI](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Désactiver la validation de signature

Par défaut, les Runbooks Workers hybrides Linux nécessitent la validation de signature. Si vous exécutez un runbook non signé sur un Worker, l'erreur `Signature validation failed` s'affiche. Pour désactiver la validation de la signature, exécutez la commande suivante. Remplacez le deuxième paramètre par votre ID d’espace de travail Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-computer"></a><a name="remove-linux-hybrid-runbook-worker"></a>Supprimer le Runbook Worker hybride à partir d’un ordinateur Linux local

Vous pouvez utiliser la commande `ls /var/opt/microsoft/omsagent` sur le Runbook Worker hybride pour obtenir l’ID d’espace de travail. Un dossier portant l'ID de l'espace de travail est créé.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Ce code ne supprime pas l'agent Log Analytics pour Linux de l'ordinateur. Il supprime uniquement les fonctionnalités et la configuration du rôle Runbook Worker hybride.

## <a name="remove-a-hybrid-worker-group"></a>Supprimer un groupe de Workers hybrides

Pour supprimer un groupe Runbook Worker hybride d’ordinateurs Linux, vous utilisez les mêmes étapes que pour un groupe de Workers hybrides Windows. Consultez [Supprimer un groupe de Workers hybrides](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour savoir comment résoudre les problèmes de vos Runbook Workers hybrides, consultez [Résoudre les problèmes liés à la fonctionnalité Runbook Worker hybride - Linux](troubleshoot/hybrid-runbook-worker.md#linux).
