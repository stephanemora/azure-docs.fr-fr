---
title: Résolution des problèmes relatifs aux Runbooks Workers hybrides Azure Automation
description: Cet article fournit des informations sur la résolution des problèmes liés aux Runbooks Workers hybrides Azure Automation.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 31d81c6946fc256f5c22b93674469d7b87500173
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480719"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Résoudre les problèmes liés aux Runbooks Workers hybrides

Cet article fournit des informations sur la résolution des problèmes liés aux Runbooks Workers hybrides.

## <a name="general"></a>Généralités

Le Runbook Worker hybride dépend d’un agent pour communiquer avec votre compte Automation et ainsi enregistrer le worker, recevoir des travaux de runbook et signaler l’état. Pour Windows, cet agent est l’agent Log Analytics pour Windows (également appelé Microsoft Monitoring Agent [MMA]). Pour Linux, il s’agit de l’agent Log Analytics pour Linux.

### <a name="runbook-execution-fails"></a>Scénario : Échec de l’exécution d’un runbook

#### <a name="issue"></a>Problème

L’exécution du Runbook échoue et l’erreur suivante s’affiche :

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Le runbook est interrompu peu après la tentative de l’exécuter trois fois. Il existe des conditions susceptibles d’interrompre l’exécution du runbook. Dans ces cas-là, le message d’erreur associé ne peut pas inclure d’informations supplémentaires qui vous indiquent le motif de l’erreur.

#### <a name="cause"></a>Cause :

Voici quelques causes possibles :

* Les Runbooks ne peuvent pas s’authentifier auprès des ressources locales.

* Le worker hybride est derrière un pare-feu ou un proxy

* Les Runbooks ne peuvent pas s’authentifier auprès des ressources locales.

* L'ordinateur configuré pour exécuter la fonctionnalité Runbook Worker hybride ne répond pas à la configuration matérielle minimale requise.

#### <a name="resolution"></a>Résolution :

Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur le port 443.

Les ordinateurs qui exécutent les Runbook Worker hybrides doivent respecter la configuration matérielle minimale requise avant de pouvoir héberger cette fonctionnalité. Les runbooks et les processus d’arrière-plan qu’ils utilisent peuvent entraîner la surcharge du système et des retards ou expirations de travaux de runbooks.

Vérifiez que l’ordinateur qui exécute la fonctionnalité Runbook Worker hybride possède la configuration matérielle minimale requise. Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et Windows. S’il existe une pression sur la mémoire ou les ressources processeur, cela peut indiquer la nécessité de mettre à niveau des ressources. Vous pouvez également sélectionner une ressource de calcul différente qui peut prendre en charge la configuration minimale requise et effectuer une mise à l’échelle lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.

Vérifiez dans le journal des événements **Microsoft-SMA** la présence d’un événement correspondant avec la description *Le processus Win32 s’est terminé avec le code [4294967295]* . La cause de cette erreur est que vous n’avez pas configuré l’authentification dans vos runbooks ou que vous n’avez pas spécifié les informations d’identification Exécuter en tant que pour le groupe Worker hybride. Veuillez consulter les [autorisations du runbook](../automation-hrw-run-runbooks.md#runbook-permissions) pour confirmer que l’authentification a été correctement configurée pour vos runbooks.

### <a name="no-cert-found"></a>Scénario : Aucun certificat n’a été trouvé dans le magasin de certificats sur Runbook Worker hybride

#### <a name="issue"></a>Problème

Un runbook en cours d’exécution sur un Runbook Worker hybride échoue avec le message d’erreur suivant :

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Cause :

Cette erreur se produit lorsque vous essayez d’utiliser un [compte d’identification](../manage-runas-account.md) dans un runbook qui s’exécute sur un Runbook Worker hybride où le certificat de compte d’identification n’est pas présent. Les Runbooks Worker hybrides n’ont pas la ressource de certificat localement par défaut, ce qui est requis pour que le compte d’identification fonctionne correctement.

#### <a name="resolution"></a>Résolution :

Si votre Runbook Worker hybride est une machine virtuelle Azure, vous pouvez utiliser des [identités managées pour les ressources Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) à la place. Ce scénario vous permet de vous authentifier auprès des ressources Azure à l’aide de l’identité managée de la machine virtuelle Azure au lieu du compte d’identification, ce qui simplifie l’authentification. Lorsque le Runbook Worker hybride est une machine locale, vous devez installer le certificat de compte d’identification sur cette machine. Pour savoir comment installer le certificat, consultez les étapes pour exécuter le runbook [Export-RunAsCertificateToHybridWorker](../automation-hrw-run-runbooks.md#runas-script).

## <a name="linux"></a>Linux

Le Runbook Worker hybride Linux dépend de l’[agent Log Analytics pour Linux](../../azure-monitor/platform/log-analytics-agent.md) pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

### <a name="oms-agent-not-running"></a>Scénario : L’agent Log Analyics pour Linux n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

L’agent Log Analytics pour Linux n’est pas en cours d’exécution.

#### <a name="cause"></a>Cause :

Si l’agent n’est pas en cours d’exécution, le Runbook Worker hybride Linux ne peut pas communiquer avec Azure Automation. L'agent peut ne pas fonctionner pour diverses raisons.

#### <a name="resolution"></a>Résolution :

 Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante : `ps -ef | grep python`. Vous devez voir une sortie similaire à celle qui suit, les processus python avec le compte d’utilisateur **nxautomation**. Si les solutions Update Management ou Azure Automation ne sont pas activées, aucun des processus suivants n’est activé.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

La liste suivante présente les processus démarrés pour un Runbook Worker hybride Linux. Ils se trouvent tous dans le répertoire `/var/opt/microsoft/omsagent/state/automationworker/`.


* **oms.conf** : il s’agit du processus de gestionnaire de Worker. Vous pouvez le démarrer directement à partir de DSC.

* **worker.conf** : processus Worker hybride automatiquement inscrit, démarré par le gestionnaire de workers. Ce processus est utilisé par Update Management et il est transparent pour l’utilisateur. Ce processus n’est pas présent si la solution Update Management n’est pas activée sur l’ordinateur.

* **diy/worker.conf** : processus Worker hybride personnalisé. Le processus Worker hybride personnalisé est utilisé pour exécuter des runbooks utilisateur sur le Runbook Worker hybride. Il diffère uniquement du processus Worker hybride automatiquement inscrit par le fait qu’il utilise une configuration différente. Ce processus n’est pas présent si la solution Azure Automation est désactivée et si le Worker hybride Linux personnalisé n’est pas inscrit.

Si l’agent n’est pas en cours d’exécution, exécutez la commande suivante pour démarrer le service : `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scénario : La classe spécifiée n’existe pas

Si vous recevez l’erreur : **La classe spécifiée n’existe pas.** dans `/var/opt/microsoft/omsconfig/omsconfig.log`, alors l’agent Log Analytics pour Linux a besoin d’une mise à jour. Exécutez la commande suivante pour réinstaller l’agent :

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Le Runbook Worker hybride Linux dépend de l’[agent Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md) pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

### <a name="mma-not-running"></a>Scénario : Microsoft Monitoring Agent n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

Le service `healthservice` n’est pas en cours d’exécution sur l’ordinateur où se trouve le Runbook Worker hybride.

#### <a name="cause"></a>Cause :

Si le service Windows Microsoft Monitoring Agent n’est pas en cours d’exécution, cet état empêche le Runbook Worker hybride de communiquer avec Azure Automation.

#### <a name="resolution"></a>Résolution :

Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante dans PowerShell : `Get-Service healthservice`. Si le service est arrêté, entrez la commande suivante dans PowerShell pour démarrer le service : `Start-Service healthservice`.

### <a name="event-4502"></a> Événement 4502 dans le journal Operations Manager

#### <a name="issue"></a>Problème

Dans le journal des événements **Journaux des applications et des services\Gestionnaire des opérations**, vous voyez l’événement 4502 et l’EventMessage contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** avec la description suivante : *Le certificat présenté par le service \<wsid\>.oms.opinsights.azure.com n’a pas été émis par une autorité de certification utilisée par les services Microsoft. Veuillez contacter votre administrateur réseau pour déterminer si un proxy en cours d’exécution intercepte la communication TLS/SSL.*

#### <a name="cause"></a>Cause :

Une cause possible de ce problème est que votre proxy ou votre pare-feu réseau bloque les communications vers Microsoft Azure. Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur les ports 443. 

#### <a name="resolution"></a>Résolution :

Les journaux d’activité sont stockés localement sur chaque Worker hybride à l’emplacement C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Vous pouvez vérifier s’il existe des événements d’avertissement ou d’erreur dans les journaux des évènements **Journaux des applications et des services\Microsoft-SMA\Operations** et **Journaux des applications et des services\Operations Manager** qui indiqueraient une connectivité ou tout autre problème affectant l’intégration du rôle à Azure Automation ou un problème dans le cadre d’un fonctionnement normal. Pour obtenir de l’aide supplémentaire concernant la résolution des problèmes liés à l’agent Log Analytics, consultez [Résoudre les problèmes liés à l’agent Log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

La [sortie et les messages de runbooks](../automation-runbook-output-and-messages.md) sont envoyés à Azure Automation à partir de Workers hybrides tout comme les tâches de runbook qui sont exécutées dans le cloud. Vous pouvez également activer les flux Détaillé et Progression comme vous le feriez pour d’autres runbooks.

### <a name="corrupt-cache"></a> Runbook Worker hybride ne créant pas de rapports

#### <a name="issue"></a>Problème

Votre ordinateur Runbook Worker hybride est en cours d’exécution, mais vous ne voyez pas les données de pulsation de l’ordinateur dans l’espace de travail.

L’exemple de requête suivant montre les ordinateurs d’un espace de travail et leur dernière pulsation :

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Cause :

Ce problème peut être lié à un cache endommagé sur le Runbook Worker hybride.

#### <a name="resolution"></a>Résolution :

Pour résoudre ce problème, connectez-vous au Runbook Worker hybride et exécutez le script suivant. Ce script arrête l’agent Microsoft Monitoring Agent supprime son cache et redémarre le service. Cette action force le Runbook Worker hybride à télécharger de nouveau sa configuration à partir d’Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>Scénario : Vous ne pouvez pas ajouter un Runbook Worker hybride

#### <a name="issue"></a>Problème

Le message suivant s’affiche lorsque vous tentez d’ajouter un Runbook Worker hybride à l’aide de la cmdlet `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Cause :

L’ordinateur est peut-être déjà inscrit avec un autre compte Automation ou vous essayez peut-être de rajouter un Runbook Worker hybride après l’avoir supprimé d’un ordinateur.

#### <a name="resolution"></a>Résolution :

Pour résoudre ce problème, supprimez la clé de Registre suivante, puis redémarrez `HealthService` et réessayez la cmdlet `Add-HybridRunbookWorker` :

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.

