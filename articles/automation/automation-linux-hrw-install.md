---
title: Runbook Worker hybride Linux Azure Automation
description: Cet article fournit des informations sur l’installation d’un Runbook Worker hybride Azure Automation qui vous permet dexécuter des Runbooks sur les machines Linux de votre centre de données local ou de votre environnement cloud.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aca68b6e8d0e6b80a1504b16b9b3462f20fdc6c4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195656"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Déploiement d’un Runbook Worker hybride Linux

La fonctionnalité de Runbook Worker hybride d’Azure Automation vous permet d’exécuter des Runbooks directement sur l’ordinateur qui héberge le rôle et par rapport aux ressources de l’environnement afin de gérer ces ressources locales. La fonctionnalité Runbook Worker hybride Linux exécute des runbooks en tant qu’utilisateur spécial dont les privilèges peuvent être élevés pour exécuter des commandes nécessitant une élévation. Les Runbooks sont stockés et gérés dans Azure Automation, puis remis à un ou plusieurs ordinateurs désignés. Cet article décrit comment installer la fonctionnalité Runbook Worker hybride sur une machine Linux.

## <a name="supported-linux-operating-systems"></a>Systèmes d’exploitation Linux pris en charge

Voici la liste des distributions Linux prises en charge :

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5, 6 et 7 (x86/x64)
* Oracle Linux 5, 6 et 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 et 7 (x86/x64)
* Debian GNU/Linux 6, 7 et 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enteprise Server 11 et 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>Installation de la fonctionnalité Runbook Worker hybride Linux

L’installation et la configuration d’un Runbook Worker hybride sur un ordinateur Linux est un processus simple qui permet d’installer et de configurer le rôle manuellement. Cela nécessite l’activation de la solution **Automation Hybrid Worker** dans votre espace de travail Log Analytics, puis l’exécution d’un ensemble de commandes pour inscrire l’ordinateur en tant que Worker et l’ajouter à un groupe nouveau ou existant.

Voici la configuration minimale requise pour une fonctionnalité Runbook Worker hybride sur Linux :

* Deux cœurs minimum
* 4 Go de RAM minimum
* Port 443 (sortie)

### <a name="package-requirements"></a>Packages requis

| **Package requis** | **Description** | **Version minimum**|
|--------------------- | --------------------- | -------------------|
|Glibc |Bibliothèque C de GNU| 2.5-12 |
|Openssl| Bibliothèques OpenSSL | 0.9.8e ou 1.0|
|Curl | Client web cURL | 7.15.5|
|Python-ctypes | |
|PAM | Modules d’authentification enfichable|

Avant de continuer, vous devez noter l’espace de travail Log Analytics auquel votre compte Automation est lié, ainsi que la clé primaire de votre compte Automation. Vous trouverez ces deux informations sur le portail en sélectionnant votre compte Automation et l’**espace de travail** correspondant à l’ID d’espace de travail et **Clés** pour la clé primaire. Pour plus d’informations sur les ports et les adresses nécessaires à la fonctionnalité Runbook Worker hybride, consultez [Configuration de votre réseau](automation-hybrid-runbook-worker.md#network-planning).

1. Activez la solution « Automation Hybrid Worker » dans Azure. Pour ce faire, vous pouvez :

   1. Ajoutez la solution **Automation Hybrid Worker** à votre abonnement à l’aide de la procédure dans la rubrique [Ajouter des solutions de gestion Log Analytics à votre espace de travail](../log-analytics/log-analytics-add-solutions.md).
   1. Exécutez l’applet de commande suivante :

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Installez l’agent OMS pour Linux en exécutant la commande suivante, en remplaçant \<WorkspaceID\> et \<WorkspaceKey\> par les valeurs appropriées de votre espace de travail.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Exécutez la commande suivante en remplaçant les valeurs des paramètres *-w*, *-k*, *-g* et *-e*. Pour le paramètre *-g*, remplacez la valeur par le nom du groupe Runbook Worker hybride auquel le nouveau Runbook Worker hybride Linux doit se joindre. Si le nom n’existe pas déjà dans votre compte Automation, un nouveau groupe Runbook Worker hybride est constitué avec ce nom.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. Une fois la commande terminée, la page Groupes de Workers hybrides du portail Azure affiche le nouveau groupe et le nombre de membres, ou, si le groupe existe déjà, incrémente le nombre de membres. Vous pouvez sélectionner le groupe dans la liste de la page **Groupes de Workers hybrides** et sélectionner la vignette **Workers hybrides**. La page **Workers hybrides** affiche chaque membre du groupe listé.

## <a name="turning-off-signature-validation"></a>Désactivation de la validation de signature

Par défaut, les Runbooks Workers hybrides Linux nécessitent la validation de signature. Si vous exécutez un Runbook non signé sur un rôle de travail, une erreur contenant « Échec de la validation de la signature » s’affiche. Pour désactiver la validation des signatures, exécutez la commande suivante, en remplaçant le deuxième paramètre par votre ID d’espace de travail Log Analytics :

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Types de Runbook pris en charge

Les Runbooks Worker hybrides ne prennent pas en charge la totalité de l’ensemble de types de Runbook disponibles dans Azure Automation.

Les types de Runbook suivants fonctionnent sur un Worker hybride Linux :

* Python 2
* PowerShell

Les types de Runbook suivants ne fonctionnent pas sur un Worker hybride Linux :

* Workflow PowerShell
* Graphique
* Graphique workflow PowerShell

## <a name="troubleshooting"></a>Résolution de problèmes

Le Runbook Worker hybride Linux dépend de l’agent OMS pour Linux pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du Worker échoue, voici les causes possibles de l’erreur :

### <a name="the-oms-agent-for-linux-is-not-running"></a>L’agent OMS pour Linux n’est pas en cours d’exécution.

Si l’agent OMS pour Linux n’est pas en cours d’exécution, le Runbook Worker hybride Linux ne peut pas communiquer avec Azure Automation. Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante : `ps -ef | grep python`. Vous devez voir une sortie similaire à celle qui suit, les processus python avec le compte d’utilisateur **nxautomation**. Si les solutions Update Management ou Azure Automation ne sont pas activées, aucun des processus suivants n’est en cours d’exécution.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

La liste suivante présente les processus démarrés pour un Runbook Worker hybride Linux. Ils se trouvent tous dans l’annuaire `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** : processus du gestionnaire de Workers, directement démarré à partir de DSC.

* **worker.conf** : processus Worker hybride automatiquement inscrit, démarré par le gestionnaire de Workers. Ce processus est utilisé par Update Management et il est transparent pour l’utilisateur. Ce processus n’est pas être présent si la solution Update Management n’est pas activée sur la machine.

* **diy/worker.conf** : processus Worker hybride personnalisé. Le processus Worker hybride personnalisé est utilisé pour exécuter des runbooks utilisateur sur le Runbook Worker hybride. Il diffère uniquement du processus Worker hybride automatiquement inscrit par le fait qu’il utilise une configuration différente. Ce processus n’est pas présent si la solution Azure Automation n’est pas activée et si le Worker hybride Linux personnalisé n’est pas inscrit.

Si l’agent OMS pour Linux n’est pas en cours d’exécution, exécutez la commande suivante pour démarrer le service : `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-does-not-exist"></a>La classe spécifiée n’existe pas

Si vous voyez l’erreur **La classe spécifiée n’existe pas...** dans `/var/opt/microsoft/omsconfig/omsconfig.log`, alors l’agent OMS pour Linux a besoin d’une mise à jour. Exécutez la commande suivante pour réinstaller l’agent OMS :

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Pour obtenir des instructions supplémentaires sur la façon de résoudre les problèmes liés à Update Management, consultez [Update Management : résolution des problèmes](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>Étapes suivantes

* Consultez [exécuter des runbooks sur un Runbook Worker hybride](automation-hrw-run-runbooks.md) pour apprendre comment configurer vos runbooks afin d’automatiser les processus dans votre centre de données local ou un autre environnement cloud.
* Pour obtenir des instructions sur la suppression des Runbooks Worker hybrides, consultez [Remove Azure Automation Hybrid Runbook Workers](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker) (Supprimer des Runbooks Worker hybrides Azure Automation).
