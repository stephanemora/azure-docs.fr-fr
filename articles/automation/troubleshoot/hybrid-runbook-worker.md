---
title: Résolution des problèmes relatifs aux Runbooks Workers hybrides Azure Automation
description: Cet article fournit des informations sur la résolution des problèmes liés aux Runbooks Workers hybrides Azure Automation.
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064322"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Résoudre les problèmes liés aux Runbooks Workers hybrides

Cet article fournit des informations sur la résolution des problèmes liés aux Runbooks Workers hybrides.

## <a name="general"></a>Généralités

Le Runbook Worker hybride dépend d’un agent pour communiquer avec votre compte Automation et ainsi enregistrer le worker, recevoir des travaux de runbook et signaler l’état. Pour Windows, cet agent est Microsoft Monitoring Agent. Pour Linux, il s’agit de l’Agent OMS pour Linux.

###<a name="runbook-execution-fails"></a>Scénario : Échec de l’exécution d’un Runbook

#### <a name="issue"></a>Problème

L’exécution du Runbook échoue et l’erreur suivante s’affiche :

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Le runbook est interrompu peu après la tentative de l’exécuter trois fois. Il existe des conditions susceptibles d’interrompre l’exécution correcte du runbook et le message d’erreur lié n’inclut pas d’informations supplémentaires indiquant pourquoi.

#### <a name="cause"></a>Cause :

Voici quelques causes possibles :

* Les Runbooks ne peuvent pas s’authentifier auprès des ressources locales.

* Le Worker hybride est derrière un pare-feu ou un proxy.

* Les Runbooks ne peuvent pas s’authentifier auprès des ressources locales.

* L’ordinateur désigné pour exécuter la fonctionnalité Runbook Worker hybride ne répond pas à la configuration matérielle minimale requise.

#### <a name="resolution"></a>Résolution :

Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur le port 443.

Les ordinateurs qui exécutent les workers hybrides doivent respecter la configuration matérielle minimale requise avant de pouvoir héberger cette fonctionnalité. Sinon, en fonction de l’utilisation des ressources d’autres processus d’arrière-plan et de la contention due aux runbooks lors de l’exécution, l’ordinateur est surchargé, entraînant des retards ou des délais d’attente pour la tâche du runbook.

Vérifiez que l’ordinateur désigné pour exécuter la fonctionnalité Runbook Worker hybride répond à la configuration matérielle minimale requise. Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et de Windows. S’il existe une surcharge de la mémoire ou du processeur, cela peut indiquer la nécessité de mettre à niveau ou d’ajouter des processeurs supplémentaires, ou d’augmenter la mémoire pour résoudre le goulot d’étranglement des ressources et résoudre l’erreur. Vous pouvez également sélectionner une ressource de calcul différente qui peut prendre en charge la configuration minimale requise et évoluer lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.

Vérifiez dans le journal des événements **Microsoft-SMA** la présence d’un événement correspondant avec la description *Le processus Win32 s’est terminé avec le code [4294967295]*. La cause de cette erreur est que vous n’avez pas configuré l’authentification dans vos runbooks ou que vous n’avez pas spécifié les informations d’identification Exécuter en tant que pour le groupe Worker hybride. Veuillez consulter les [autorisations du runbook](../automation-hrw-run-runbooks.md#runbook-permissions) pour confirmer que l’authentification a été correctement configurée pour vos runbooks.

## <a name="linux"></a>Linux

Le Runbook Worker hybride Linux dépend de l’agent OMS pour Linux pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

###<a name="oms-agent-not-running"></a>Scénario : L’Agent OMS pour Linux n’est pas en cours d’exécution

Si l’agent OMS pour Linux n’est pas en cours d’exécution, le Runbook Worker hybride Linux ne peut pas communiquer avec Azure Automation. Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante : `ps -ef | grep python`. Vous devez voir une sortie similaire à celle qui suit, les processus python avec le compte d’utilisateur **nxautomation**. Si les solutions Update Management ou Azure Automation ne sont pas activées, aucun des processus suivants n’est activé.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

La liste suivante présente les processus démarrés pour un Runbook Worker hybride Linux. Ils se trouvent tous dans l’annuaire `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** : processus du gestionnaire de Workers, directement démarré à partir de DSC.

* **worker.conf** : processus Worker hybride automatiquement inscrit, démarré par le gestionnaire de Workers. Ce processus est utilisé par Update Management et il est transparent pour l’utilisateur. Ce processus n’est pas présent si la solution Update Management n’est pas activée sur la machine.

* **diy/worker.conf** : processus Worker hybride personnalisé. Le processus Worker hybride personnalisé est utilisé pour exécuter des runbooks utilisateur sur le Runbook Worker hybride. Il diffère uniquement du processus Worker hybride automatiquement inscrit par le fait qu’il utilise une configuration différente. Ce processus n’est pas présent si la solution Azure Automation n’est pas activée et si le Worker hybride Linux personnalisé n’est pas inscrit.

Si l’agent OMS pour Linux n’est pas en cours d’exécution, exécutez la commande suivante pour démarrer le service : `sudo /opt/microsoft/omsagent/bin/service_control restart`.

###<a name="class-does-not-exist"></a>Scénario : La classe spécifiée n’existe pas

Si vous voyez l’erreur **La classe spécifiée n’existe pas...** dans `/var/opt/microsoft/omsconfig/omsconfig.log`, alors l’agent OMS pour Linux a besoin d’une mise à jour. Exécutez la commande suivante pour réinstaller l’agent OMS :

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Le Runbook Worker hybride Windows dépend de l’agent Microsoft Monitoring Agent pour communiquer avec votre compte Automation, et ainsi enregistrer le Worker, recevoir des travaux de Runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

###<a name="mma-not-running"></a>Scénario : Microsoft Monitoring Agent n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

Le service `healthservice` n’est pas exécuté sur la machine où se trouve le Runbook Worker hybride.

#### <a name="cause"></a>Cause :

Si le service Windows de Microsoft Monitoring Agent n’est pas en cours d’exécution, le Runbook Worker hybride ne peut pas communiquer avec Azure Automation.

#### <a name="resolution"></a>Résolution :

Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante dans PowerShell : `Get-Service healthservice`. Si le service est arrêté, entrez la commande suivante dans PowerShell pour démarrer le service : `Start-Service healthservice`.

###<a name="event-4502"></a> Événement 4502 dans le journal Operations Manager

#### <a name="issue"></a>Problème

Dans le journal des événements **Application et journaux des services\Operations Manager**, vous voyez l’événement 4502 et l’EventMessage contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** avec la description suivante : *le certificat présenté par le service \<wsid\>.oms.opinsights.azure.com n’a pas été émis par une autorité de certification utilisée par les services Microsoft. Veuillez contacter votre administrateur réseau pour déterminer si un proxy en cours d’exécution intercepte la communication TLS/SSL. L’article KB3126513 contient des informations supplémentaires pour la résolution des problèmes de connectivité.*

#### <a name="cause"></a>Cause :

Une cause possible est que votre proxy ou votre pare-feu réseau bloque les communications vers Microsoft Azure. Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur les ports 443.

#### <a name="resolution"></a>Résolution :

Les journaux sont stockés localement sur chaque Worker hybride à l’emplacement C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Vous pouvez vérifier s’il existe des événements d’avertissement ou d’erreur écrits dans les journaux des évènements **Application et journal des services\Microsoft SMA\gestionnaire des opérations** et **Application et journal des services\gestionnaire des opérations** qui indiqueraient une connectivité ou tout autre problème affectant l’intégration du rôle à Azure Automation ou un problème lors de l’exécution d’opérations normales.

[Sortie et messages de Runbooks](../automation-runbook-output-and-messages.md) : ils sont envoyés à Azure Automation à partir de Workers hybrides tout comme les tâches de Runbook exécutées dans le cloud. Vous pouvez également activer les flux Détaillé et Progression comme vous le feriez pour d’autres runbooks.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez l’un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
