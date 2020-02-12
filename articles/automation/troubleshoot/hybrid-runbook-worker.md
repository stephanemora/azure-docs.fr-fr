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
ms.openlocfilehash: 4d804499116631be6f922f67f8b8f6c7063a6d5c
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030725"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Résoudre les problèmes liés aux Runbooks Workers hybrides

Cet article fournit des informations sur la résolution des problèmes liés aux Runbooks Workers hybrides.

## <a name="general"></a>Général

Le Runbook Worker hybride dépend d’un agent pour communiquer avec votre compte Automation et ainsi enregistrer le worker, recevoir des travaux de runbook et signaler l’état. Pour Windows, cet agent est l’agent Log Analytics pour Windows, également appelé Microsoft Monitoring Agent (MMA). Pour Linux, il s’agit de l’agent Log Analytics pour Linux.

### <a name="runbook-execution-fails"></a>Scénario : Échec de l’exécution d’un runbook

#### <a name="issue"></a>Problème

L’exécution du runbook échoue et vous recevez le message d’erreur ci-après.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Le runbook est interrompu peu après sa tentative d’exécution à trois reprises. Certaines conditions peuvent interrompre la bonne exécution du runbook. Le message d’erreur associé peut ne pas inclure d’informations supplémentaires.

#### <a name="cause"></a>Cause :

Les causes possibles sont les suivantes :

* Les runbooks ne peuvent pas s’authentifier auprès des ressources locales.

* Le worker hybride est derrière un pare-feu ou un proxy.

* L’ordinateur configuré pour exécuter la fonctionnalité Runbook Worker hybride ne satisfait pas à la configuration matérielle minimale requise.

#### <a name="resolution"></a>Résolution

Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur le port 443.

Les ordinateurs qui exécutent les Runbooks Worker hybrides doivent respecter la configuration matérielle minimale requise pour pouvoir héberger cette fonctionnalité. Les runbooks et le processus d’arrière-plan qu’ils utilisent peuvent entraîner la sur-utilisation du système et des retards ou expirations de travaux de runbooks.

Vérifiez que l’ordinateur qui exécute la fonctionnalité Runbook Worker hybride possède la configuration matérielle minimale requise. Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et Windows. Toute pression sur la mémoire ou les ressources processeur peut indiquer la nécessité de mettre à niveau des ressources. Vous pouvez également sélectionner une ressource de calcul différente qui prend en charge la configuration minimale requise et effectuer une mise à l’échelle lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.

Vérifiez dans le journal des événements **Microsoft-SMA** la présence d’un événement correspondant avec la description *Le processus Win32 s’est terminé avec le code [4294967295]* . La cause de cette erreur est que vous n’avez pas configuré l’authentification dans vos runbooks ou que vous n’avez pas spécifié les informations de connexion du compte d’identification du groupe Worker hybride. Consultez les [autorisations du runbook](../automation-hrw-run-runbooks.md#runbook-permissions) pour vérifier que l’authentification a été correctement configurée pour vos runbooks.

### <a name="no-cert-found"></a>Scénario : Aucun certificat n’a été trouvé dans le magasin de certificats sur Runbook Worker hybride

#### <a name="issue"></a>Problème

Un runbook en cours d’exécution sur un Runbook Worker hybride échoue avec le message d’erreur suivant.

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>Cause :

Cette erreur se produit lorsque vous essayez d’utiliser un [compte d’identification](../manage-runas-account.md) dans un runbook qui s’exécute sur un Runbook Worker hybride où le certificat de compte d’identification n’est pas présent. Les Runbooks Worker hybrides n’ont pas la ressource de certificat en local par défaut, ce qui est obligatoire pour que le compte d’identification fonctionne correctement.

#### <a name="resolution"></a>Résolution

Si votre Runbook Worker hybride est une machine virtuelle Azure, vous pouvez utiliser des [identités managées pour les ressources Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) à la place. Ce scénario simplifie l’authentification en vous permettant de vous authentifier auprès des ressources Azure à l’aide de l’identité managée de la machine virtuelle Azure au lieu du compte d’identification. Lorsque le Runbook Worker hybride est une machine locale, vous devez installer le certificat de compte d’identification sur cette machine. Pour découvrir comment installer le certificat, consultez les étapes de l’exécution du runbook PowerShell Export-RunAsCertificateToHybridWorker dans [Exécution de runbooks sur un Runbook Worker hybride](../automation-hrw-run-runbooks.md).

### <a name="error-403-on-registration"></a>Scénario : Erreur 403 lors de l’inscription du Runbook Worker hybride

#### <a name="issue"></a>Problème

La phase d’inscription initiale du Worker échoue et vous recevez le message erreur suivant (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Cause :

Les causes possibles sont les suivantes :
* Un ID ou une clé (primaire) d’espace de travail est mal orthographié(e) dans les paramètres de l’agent. 
* Le Runbook Worker hybride ne peut pas télécharger la configuration, ce qui entraîne une erreur de liaison de compte. Quand Azure active des solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Il est également possible qu’une date et/ou une heure incorrectes soient définies sur l’ordinateur. Si l’heure varie de +/-15 minutes par rapport à l’heure actuelle, l’intégration échoue.

#### <a name="resolution"></a>Résolution

##### <a name="mistyped-workspace-idkey"></a>ID/clé d’espace de travail mal orthographié(e)
Pour vérifier si l’ID ou la clé d’espace de travail de l’agent est mal orthographié(e), consultez [Ajout ou suppression d’un espace de travail - Agent Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) pour l’agent Windows ou [Ajout ou suppression d’un espace de travail - Agent Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) pour l’agent Linux.  Veillez à sélectionner la chaîne complète à partir du portail Azure, puis à la copier et à la coller soigneusement.

##### <a name="configuration-not-downloaded"></a>Configuration non téléchargée

Votre espace de travail Log Analytics et votre compte Automation doivent se trouver dans une région liée. Pour obtenir la liste des régions prises en charge, consultez [Mappages d’espaces de travail Azure Automation et Log Analytics](../how-to/region-mappings.md).

Vous devrez peut-être également mettre à jour la date et le fuseau horaire de votre ordinateur. Si vous sélectionnez un intervalle de temps personnalisé, vérifiez qu’il est au format UTC, lequel peut être différent de votre fuseau horaire local.

## <a name="linux"></a>Linux

Le Runbook Worker hybride Linux dépend de l’[agent Log Analytics pour Linux](../../azure-monitor/platform/log-analytics-agent.md) pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

### <a name="oms-agent-not-running"></a>Scénario : L’agent Log Analytics pour Linux n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

L’agent Log Analytics pour Linux n’est pas en cours d’exécution

#### <a name="cause"></a>Cause :

Si l’agent n’est pas en cours d’exécution, le Runbook Worker hybride Linux ne peut pas communiquer avec Azure Automation. L’agent peut ne pas s’exécuter pour diverses raisons.

#### <a name="resolution"></a>Résolution

 Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante : `ps -ef | grep python`. Vous devez voir une sortie similaire à celle qui suit, les processus python avec le compte d’utilisateur **nxautomation**. Si les solutions Update Management ou Azure Automation ne sont pas activées, aucun des processus suivants n’est activé.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

La liste suivante présente les processus démarrés pour un Runbook Worker hybride Linux. Ils se trouvent tous dans le répertoire `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** : processus du gestionnaire de Workers. Vous pouvez le démarrer directement à partir de DSC.

* **worker.conf** : processus Worker hybride automatiquement inscrit. Il est démarré par le gestionnaire de Worker. Ce processus est utilisé par Update Management et il est transparent pour l’utilisateur. Ce processus n’est pas présent si la solution Update Management n’est pas activée sur l’ordinateur.

* **diy/worker.conf** : processus Worker hybride personnalisé. Le processus Worker hybride personnalisé est utilisé pour exécuter des runbooks utilisateur sur le Runbook Worker hybride. Il diffère uniquement du processus Worker hybride automatiquement inscrit par le fait qu’il utilise une configuration différente. Ce processus n’est pas présent si la solution Azure Automation est désactivée et si le Worker hybride Linux personnalisé n’est pas inscrit.

Si l’agent n’est pas en cours d’exécution, exécutez la commande suivante pour démarrer le service : `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="class-does-not-exist"></a>Scénario : La classe spécifiée n’existe pas.

Si vous voyez l’erreur **La classe spécifiée n’existe pas** dans `/var/opt/microsoft/omsconfig/omsconfig.log`, alors l’agent Log Analytics pour Linux a besoin d’une mise à jour. Exécutez la commande suivante pour réinstaller l’agent :

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Le Runbook Worker hybride Linux dépend de l’[agent Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md) pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

### <a name="mma-not-running"></a>Scénario : Microsoft Monitoring Agent n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

Le service `healthservice` n’est pas en cours d’exécution sur l’ordinateur où se trouve le Runbook Worker hybride.

#### <a name="cause"></a>Cause :

Si le service Microsoft Monitoring Agent n’est pas en cours d’exécution, cet état empêche le Runbook Worker hybride de communiquer avec Azure Automation.

#### <a name="resolution"></a>Résolution

Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante dans PowerShell : `Get-Service healthservice`. Si le service est arrêté, entrez la commande suivante dans PowerShell pour démarrer le service : `Start-Service healthservice`.

### <a name="event-4502"></a>Scénario : Événement 4502 dans le journal Operations Manager

#### <a name="issue"></a>Problème

Dans le journal des événements **Journaux des applications et des services\Gestionnaire des opérations**, vous voyez l’événement 4502 et l’EventMessage contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** avec la description suivante : *Le certificat présenté par le service \<wsid\>.oms.opinsights.azure.com n’a pas été émis par une autorité de certification utilisée par les services Microsoft. Veuillez contacter votre administrateur réseau pour déterminer si un proxy en cours d’exécution intercepte la communication TLS/SSL.*

#### <a name="cause"></a>Cause :

Une cause possible de ce problème est que votre proxy ou votre pare-feu réseau bloque les communications vers Microsoft Azure. Vérifiez que l’ordinateur dispose d’un accès sortant à *.azure-automation.net sur les ports 443. 

#### <a name="resolution"></a>Résolution

Les journaux d’activité sont stockés localement sur chaque Worker hybride à l’emplacement C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Vous pouvez vérifier s’il existe des événements d’avertissement ou d’erreur dans les journaux des évènements **Journaux des applications et des services\Microsoft-SMA\Operations** et **Journaux des applications et des services\Operations Manager** qui indiquent un problème de connectivité ou tout autre problème affectant l’intégration du rôle à Azure Automation ou un problème dans le cadre d’un fonctionnement normal. Pour obtenir de l’aide supplémentaire concernant la résolution des problèmes liés à l’agent Log Analytics, consultez [Résoudre les problèmes liés à l’agent Log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

La [sortie et les messages de runbooks](../automation-runbook-output-and-messages.md) sont envoyés à Azure Automation à partir de Workers hybrides tout comme les tâches de runbook qui sont exécutées dans le cloud. Vous pouvez également activer les flux Détaillé et Progression comme vous le feriez pour d’autres runbooks.

### <a name="corrupt-cache"></a>Scénario : Runbook Worker hybride ne créant pas de rapports

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

#### <a name="resolution"></a>Résolution

Pour résoudre ce problème, connectez-vous au Runbook Worker hybride et exécutez le script suivant. Ce script arrête l’agent Microsoft Monitoring Agent supprime son cache et redémarre le service. Cette action force le Runbook Worker hybride à retélécharger sa configuration à partir d’Azure Automation.

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

Ce problème peut venir du fait que l’ordinateur est déjà inscrit avec un autre compte Automation ou que vous essayez peut-être de rajouter le Runbook Worker hybride après l’avoir supprimé d’un ordinateur.

#### <a name="resolution"></a>Résolution

Pour résoudre ce problème, supprimez la clé de Registre suivante, puis redémarrez `HealthService` et réessayez la cmdlet `Add-HybridRunbookWorker` :

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.