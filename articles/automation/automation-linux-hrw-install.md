---
title: Runbook Worker hybride Linux Azure Automation
description: Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet d’exécuter des Runbooks sur les ordinateurs Linux de votre centre de données local ou de votre environnement cloud.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267861"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Déployer un Runbook Worker hybride Linux

Vous pouvez utiliser la fonctionnalité de Runbook Worker hybride d’Azure Automation pour exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. La fonctionnalité Runbook Worker hybride Linux exécute des Runbooks en tant qu’utilisateur spécial dont les privilèges peuvent être élevés pour exécuter des commandes nécessitant une élévation. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés.

Cet article décrit comment installer la fonctionnalité Runbook Worker hybride sur un ordinateur Linux.

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

La fonctionnalité de Runbook Worker hybride prend en charge les distributions suivantes :

* Amazon Linux 2012.09 à 2015.09 (x86/x64)
* CentOS Linux 5, 6 et 7 (x86/x64)
* Oracle Linux 5, 6 et 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 et 7 (x86/x64)
* Debian GNU/Linux 6, 7 et 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS et 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 et 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Installation de la fonctionnalité Runbook Worker hybride Linux

L’installation et la configuration de la fonctionnalité Runbook Worker hybride sur un ordinateur Linux est un processus simple qui permet d’installer et de configurer le rôle manuellement. Cela nécessite l’activation de la solution **Automation Hybrid Worker** dans votre espace de travail Azure Log Analytics, puis l’exécution d’un ensemble de commandes pour inscrire l’ordinateur en tant que Worker et l’ajouter à un groupe.

Voici la configuration minimale requise pour une fonctionnalité Runbook Worker hybride Linux :

* 2 cœurs
* 4 Go de RAM
* Port 443 (sortie)

### <a name="package-requirements"></a>Packages requis

| **Package requis** | **Description** | **Version minimum**|
|--------------------- | --------------------- | -------------------|
|Glibc |Bibliothèque C de GNU| 2.5-12 |
|Openssl| Bibliothèques OpenSSL | 0.9.8e ou 1.0|
|Curl | Client web cURL | 7.15.5|
|Python-ctypes | |
|PAM | Modules d’authentification enfichable|
| **Package facultatif** | **Description** | **Version minimum**|
| PowerShell Core | Pour exécuter des Runbooks PowerShell, PowerShell doit être installé. Consultez la rubrique [Installation de PowerShell Core sous Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) pour savoir comment l’installer.  | 6.0.0 |

### <a name="installation"></a>Installation

Avant de continuer, notez l’espace de travail Log Analytics lié à votre compte Automation. Notez également la clé primaire de votre compte Automation. Vous trouverez ces deux informations sur le portail Azure en sélectionnant votre compte Automation, l’**espace de travail** correspondant à l’ID d’espace de travail et **Clés** pour la clé primaire. Pour plus d’informations sur les ports et les adresses nécessaires à la fonctionnalité Runbook Worker hybride, consultez la rubrique [Configuration de votre réseau](automation-hybrid-runbook-worker.md#network-planning).

1. Activez la solution **Automation Hybrid Worker** dans Azure en utilisant l’une des méthodes suivantes :

   * Ajoutez la solution **Automation Hybrid Worker** à votre abonnement à l’aide de la procédure décrite dans la rubrique [Ajouter des solutions de gestion Log Analytics à votre espace de travail](../log-analytics/log-analytics-add-solutions.md).
   * Exécutez l’applet de commande suivante :

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installez l’agent OMS pour Linux en exécutant la commande suivante. Remplacez \<WorkspaceID\> et \<WorkspaceKey\> par les valeurs appropriées de votre espace de travail.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Exécutez la commande suivante en remplaçant les valeurs des paramètres *-w*, *-k*, *-g* et *-e*. Pour le paramètre *-g*, remplacez la valeur par le nom du groupe Runbook Worker hybride auquel le nouveau Runbook Worker hybride Linux doit se joindre. Si le nom n’existe pas dans votre compte Automation, un nouveau groupe Runbook Worker hybride est constitué avec ce nom.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Une fois la commande exécutée, la page **Groupes de travail hybrides** du portail Azure affiche le nouveau groupe et le nombre de membres. S’il s’agit d’un groupe existant, le nombre de membres est incrémenté. Vous pouvez sélectionner le groupe dans la liste de la page **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**. La page **Workers hybrides** affiche chaque membre du groupe listé.

## <a name="turning-off-signature-validation"></a>Désactivation de la validation de signature

Par défaut, les Runbooks Workers hybrides Linux nécessitent la validation de signature. Si vous exécutez un Runbook non signé sur un Worker, une erreur d’échec de la validation de la signature s’affiche. Pour désactiver la validation de la signature, exécutez la commande suivante. Remplacez le deuxième paramètre par votre ID d’espace de travail Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Types de Runbook pris en charge

Les Runbooks Worker hybrides ne prennent pas en charge la totalité des types de Runbook dans Azure Automation.

Les types de Runbook suivants fonctionnent sur un Worker hybride Linux :

* Python 2
* PowerShell

  > [!NOTE]
  > Les Runbooks PowerShell ont besoin que PowerShell Core soit installé sur l’ordinateur Linux. Consultez la rubrique [Installation de PowerShell Core sous Linux](/powershell/scripting/setup/installing-powershell-core-on-linux) pour connaître la procédure d’installation.

Les types de Runbooks suivants ne fonctionnent pas sur un Worker hybride Linux :

* Workflow PowerShell
* Graphique
* Graphique workflow PowerShell

## <a name="troubleshooting"></a>Résolution de problèmes

Le Runbook Worker hybride Linux dépend de l’agent OMS pour Linux pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du Worker échoue, voici les causes possibles de l’erreur :

### <a name="the-oms-agent-for-linux-isnt-running"></a>L’agent OMS pour Linux n’est pas en cours d’exécution.

Si l’agent OMS pour Linux n’est pas en cours d’exécution, le Runbook Worker hybride Linux ne peut pas communiquer avec Azure Automation. Vérifiez que l’agent est en cours d’exécution en entrant la commande `ps -ef | grep python`. 

Vous devez voir une sortie semblable à celle qui suit (les processus Python avec le compte d’utilisateur **nxautomation**). Si les solutions Update Management ou Azure Automation ne sont pas activées, aucun des processus suivants n’est en cours d’exécution.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Les processus suivants sont démarrés pour un Runbook Worker hybride Linux. Ils se trouvent tous dans le répertoire `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** : il s’agit du processus de gestionnaire de Worker. Il est démarré directement à partir de la configuration d’état souhaité (DSC).

* **worker.conf** : il s’agit du processus Worker hybride inscrit automatiquement. Il est démarré par le gestionnaire de Worker. Ce processus est utilisé par Update Management et il est transparent pour l’utilisateur. Ce processus n’est présent que si la solution Update Management est activée sur la machine.

* **diy/worker.conf** : il s’agit du processus Worker hybride personnalisé. Le processus Worker hybride personnalisé est utilisé pour exécuter des runbooks utilisateur sur le Runbook Worker hybride. Il diffère uniquement du processus Worker hybride inscrit automatiquement par le fait qu’il utilise une configuration différente. Ce processus n’est présent que si la solution Azure Automation est activée et si le Worker hybride Linux personnalisé est inscrit.

Si l’agent OMS pour Linux n’est pas en cours d’exécution, exécutez la commande suivante pour démarrer le service : `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-doesnt-exist"></a>La classe spécifiée n’existe pas.

Si vous voyez l’erreur « la classe spécifiée n’existe pas » dans `/var/opt/microsoft/omsconfig/omsconfig.log`, l’agent OMS pour Linux doit être mis à jour. Exécutez la commande suivante pour réinstaller l’agent OMS :

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Pour obtenir des instructions supplémentaires sur la façon de résoudre les problèmes liés à Update Management, consultez la rubrique [Update Management : résolution des problèmes](automation-update-management.md#troubleshooting).

## <a name="next-steps"></a>Étapes suivantes

* Pour apprendre à configurer vos Runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud, consultez la rubrique [Exécuter des Runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md).
* Pour obtenir des instructions sur la suppression des Runbooks Worker hybrides, consultez la rubrique [Supprimer des Runbooks Worker hybrides Azure Automation](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
