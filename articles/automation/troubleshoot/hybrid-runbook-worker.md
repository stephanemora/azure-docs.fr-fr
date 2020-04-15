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
ms.openlocfilehash: d2587af0ada18b5c4271e7411783fe60211a3479
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637855"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Résoudre les problèmes liés aux Runbooks Workers hybrides

Cet article fournit des informations sur la résolution des problèmes liés aux Runbooks Workers hybrides.

## <a name="general"></a>Général

Le Runbook Worker hybride dépend d’un agent pour communiquer avec votre compte Automation et ainsi enregistrer le worker, recevoir des travaux de runbook et signaler l’état. Pour Windows, cet agent est l’agent Log Analytics pour Windows, également appelé Microsoft Monitoring Agent (MMA). Pour Linux, il s’agit de l’agent Log Analytics pour Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scénario : Échec de l’exécution d’un runbook

#### <a name="issue"></a>Problème

L’exécution du runbook échoue et vous recevez le message d’erreur ci-après.

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Le runbook est interrompu peu après sa tentative d’exécution à trois reprises. Certaines conditions peuvent interrompre la bonne exécution du runbook. Le message d’erreur associé peut ne pas inclure d’informations supplémentaires.

#### <a name="cause"></a>Cause

Les causes possibles sont les suivantes :

* Les runbooks ne peuvent pas s’authentifier auprès des ressources locales.

* Le worker hybride est derrière un pare-feu ou un proxy.

* L’ordinateur configuré pour exécuter la fonctionnalité Runbook Worker hybride ne satisfait pas à la configuration matérielle minimale requise.

#### <a name="resolution"></a>Résolution

Vérifiez que l’ordinateur dispose d’un accès sortant à * **.azure-automation.net** sur le port 443.

Les ordinateurs qui exécutent les Runbooks Worker hybrides doivent respecter la configuration matérielle minimale requise pour pouvoir héberger cette fonctionnalité. Les runbooks et le processus d’arrière-plan qu’ils utilisent peuvent entraîner la sur-utilisation du système et des retards ou expirations de travaux de runbooks.

Vérifiez que l’ordinateur qui exécute la fonctionnalité Runbook Worker hybride possède la configuration matérielle minimale requise. Si c’est le cas, surveillez l’utilisation du processeur et de la mémoire pour déterminer toute corrélation entre les performances des processus Runbook Worker hybride et Windows. Toute pression sur la mémoire ou les ressources processeur peut indiquer la nécessité de mettre à niveau des ressources. Vous pouvez également sélectionner une ressource de calcul différente qui prend en charge la configuration minimale requise et effectuer une mise à l’échelle lorsque les demandes en matière de charge de travail indiquent qu’une augmentation est nécessaire.

Vérifiez dans le journal des événements **Microsoft-SMA** la présence d’un événement correspondant avec la description `Win32 Process Exited with code [4294967295]`. La cause de cette erreur est que vous n’avez pas configuré l’authentification dans vos runbooks ou que vous n’avez pas spécifié les informations de connexion du compte d’identification du groupe Runbook Worker hybride. Consultez les autorisations du runbook dans la section [Exécution de runbooks sur un Runbook Worker hybride](../automation-hrw-run-runbooks.md) pour vérifier que l’authentification a été correctement configurée pour vos runbooks.

### <a name="scenario-event-15011-in-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scénario : Événement 15011 dans le Runbook Worker hybride

#### <a name="issue"></a>Problème

Le Runbook Worker hybride reçoit l’événement 15011, indiquant qu’un résultat de requête n’est pas valide. L’erreur suivante s’affiche lorsque le Worker tente d’ouvrir une connexion avec le [serveur Signalr](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.1).

```error
[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
```

#### <a name="cause"></a>Cause

Le Runbook Worker hybride n’a pas été configuré correctement pour la solution de déploiement automatisé. Cette solution contient un composant qui connecte la machine virtuelle à l’espace de travail Log Analytics. Le script PowerShell recherche l’espace de travail dans l’abonnement avec le nom fourni. Dans ce cas, l’espace de travail Log Analytics se trouve dans un autre abonnement. Le script ne trouve pas l’espace de travail et tente d’en créer un, mais le nom est déjà utilisé. Par conséquent, le déploiement échoue.

#### <a name="resolution"></a>Résolution

Deux options s’offrent à vous pour résoudre ce problème :

* Modifiez le script PowerShell pour qu’il recherche l’espace de travail Log Analytics dans un autre abonnement. C’est une bonne solution si vous envisagez de déployer de nombreuses machines Runbook Worker hybrides à l’avenir.

* Configurez manuellement l’ordinateur Worker pour qu’il s’exécute dans un bac à sable (sandbox) Orchestrator. Exécutez ensuite un runbook créé dans le compte Azure Automation sur le Worker pour tester la fonctionnalité.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scénario : Les machines virtuelles Windows Azure sont automatiquement supprimées du groupe Worker hybride

#### <a name="issue"></a>Problème

Vous ne pouvez pas voir les machines virtuelles ou les Runbook Worker hybrides lorsque l’ordinateur Worker a été mis hors tension pendant un certain temps.

#### <a name="cause"></a>Cause

L’ordinateur Runbook Worker hybride n’a pas interrogé Azure Automation depuis plus de 30 jours. Par conséquent, Automation a vidé le groupe Runbook Worker hybride ou le groupe System Worker. 

#### <a name="resolution"></a>Résolution

Démarrez l’ordinateur Worker, puis réinscrivez-le avec Azure Automation. Pour obtenir des instructions sur l’installation de l’environnement runbook et la connexion à Azure Automation, consultez [Déployer un Runbook Worker hybride Windows](../automation-windows-hrw-install.md).

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scénario : Aucun certificat n’a été trouvé dans le magasin de certificats sur Runbook Worker hybride

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
#### <a name="cause"></a>Cause

Cette erreur se produit lorsque vous essayez d’utiliser un [compte d’identification](../manage-runas-account.md) dans un runbook qui s’exécute sur un Runbook Worker hybride où le certificat de compte d’identification n’est pas présent. Les Runbooks Worker hybrides n’ont pas la ressource de certificat en local par défaut, ce qui est obligatoire pour que le compte d’identification fonctionne correctement.

#### <a name="resolution"></a>Résolution

Si votre Runbook Worker hybride est une machine virtuelle Azure, vous pouvez utiliser des [identités managées pour les ressources Azure](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) à la place. Ce scénario simplifie l’authentification en vous permettant de vous authentifier auprès des ressources Azure à l’aide de l’identité managée de la machine virtuelle Azure au lieu du compte d’identification. Lorsque le Runbook Worker hybride est une machine locale, vous devez installer le certificat de compte d’identification sur cette machine. Pour découvrir comment installer le certificat, consultez les étapes de l’exécution du runbook PowerShell Export-RunAsCertificateToHybridWorker dans [Exécution de runbooks sur un Runbook Worker hybride](../automation-hrw-run-runbooks.md).

### <a name="scenario-error-403-during-registration-of-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scénario : Erreur 403 lors de l’inscription du Runbook Worker hybride

#### <a name="issue"></a>Problème

La phase d’inscription initiale du Worker échoue et vous recevez le message erreur suivant (403).

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>Cause

Les causes possibles sont les suivantes :

* Un ID ou une clé (primaire) d’espace de travail est mal orthographié(e) dans les paramètres de l’agent. 

* Le Runbook Worker hybride ne peut pas télécharger la configuration, ce qui entraîne une erreur de liaison de compte. Quand Azure active des solutions, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un compte Automation. Il est également possible qu’une date et/ou une heure incorrectes soient définies sur l’ordinateur. Si l’heure varie de +/-15 minutes par rapport à l’heure actuelle, l’intégration échoue.

#### <a name="resolution"></a>Résolution

##### <a name="mistyped-workspace-idkey"></a>ID/clé d’espace de travail mal orthographié(e)
Pour vérifier si l’ID ou la clé d’espace de travail de l’agent est mal orthographié(e), consultez [Ajout ou suppression d’un espace de travail - Agent Windows](../../azure-monitor/platform/agent-manage.md#windows-agent) pour l’agent Windows ou [Ajout ou suppression d’un espace de travail - Agent Linux](../../azure-monitor/platform/agent-manage.md#linux-agent) pour l’agent Linux.  Veillez à sélectionner la chaîne complète à partir du portail Azure, puis à la copier et à la coller soigneusement.

##### <a name="configuration-not-downloaded"></a>Configuration non téléchargée

Votre espace de travail Log Analytics et votre compte Automation doivent se trouver dans une région liée. Pour obtenir la liste des régions prises en charge, consultez [Mappages d’espaces de travail Azure Automation et Log Analytics](../how-to/region-mappings.md).

Vous devrez peut-être également mettre à jour la date et/ou le fuseau horaire de votre ordinateur. Si vous sélectionnez un intervalle de temps personnalisé, vérifiez qu’il est au format UTC, lequel peut être différent de votre fuseau horaire local.

## <a name="linux"></a>Linux

Le Runbook Worker hybride Linux dépend de l’[agent Log Analytics pour Linux](../../azure-monitor/platform/log-analytics-agent.md) pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du worker échoue, voici les causes possibles de l’erreur :

### <a name="scenario-the-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scénario : L’agent Log Analytics pour Linux n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

L’agent Log Analytics pour Linux n’est pas en cours d’exécution

#### <a name="cause"></a>Cause

Si l’agent n’est pas en cours d’exécution, le Runbook Worker hybride Linux ne peut pas communiquer avec Azure Automation. L’agent peut ne pas s’exécuter pour diverses raisons.

#### <a name="resolution"></a>Résolution

 Vérifiez que l’agent est en cours d’exécution en entrant la commande `ps -ef | grep python`. Vous devez voir une sortie similaire à celle qui suit, les processus Python avec le compte d’utilisateur **nxautomation**. Si la solution Update Management ou Azure Automation n’est pas activée, aucun des processus suivants ne fonctionne.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

La liste suivante présente les processus démarrés pour un Runbook Worker hybride Linux. Ils se trouvent tous dans le répertoire **/var/opt/Microsoft/omsagent/State/automationworker/** .

* **oms.conf** : processus du gestionnaire de Workers. Vous pouvez le démarrer directement à partir de DSC.

* **worker.conf** : processus Worker hybride automatiquement inscrit. Il est démarré par le gestionnaire de Worker. Ce processus est utilisé par Update Management et il est transparent pour l’utilisateur. Ce processus n’est pas présent si la solution Update Management n’est pas activée sur l’ordinateur.

* **diy/worker.conf** : processus Worker hybride personnalisé. Le processus Worker hybride personnalisé est utilisé pour exécuter des runbooks utilisateur sur le Runbook Worker hybride. Il diffère uniquement du processus Worker hybride automatiquement inscrit par le fait qu’il utilise une configuration différente. Ce processus n’est pas présent si la solution Azure Automation est désactivée et si le Worker hybride Linux personnalisé n’est pas inscrit.

Si l’agent n’est pas en cours d’exécution, exécutez la commande suivante pour démarrer le service : `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scénario : La classe spécifiée n’existe pas.

Si vous voyez l’erreur `The specified class does not exist..` dans **/var/opt/Microsoft/omsconfig/omsconfig.log**, l’agent Log Analytics pour Linux doit être mis à jour. Exécutez la commande suivante pour réinstaller l’agent :

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Le Runbook Worker hybride Linux dépend de l’[agent Log Analytics pour Windows](../../azure-monitor/platform/log-analytics-agent.md) pour communiquer avec votre compte Automation et ainsi enregistrer le Worker, recevoir des travaux de runbook et signaler l’état. Si l’inscription du Worker échoue, cette section présente certaines raisons possibles.

### <a name="scenario-the-microsoft-monitoring-agent-isnt-running"></a><a name="mma-not-running"></a>Scénario : Microsoft Monitoring Agent n’est pas en cours d’exécution

#### <a name="issue"></a>Problème

Le service `healthservice` n’est pas en cours d’exécution sur l’ordinateur où se trouve le Runbook Worker hybride.

#### <a name="cause"></a>Cause

Si le service Microsoft Monitoring Agent n’est pas en cours d’exécution, le Runbook Worker hybride ne peut pas communiquer avec Azure Automation.

#### <a name="resolution"></a>Résolution

Vérifiez que l’agent est en cours d’exécution en entrant la commande suivante dans PowerShell : `Get-Service healthservice`. Si le service est arrêté, entrez la commande suivante dans PowerShell pour démarrer le service : `Start-Service healthservice`.

### <a name="scenario-event-4502-in-operations-manager-log"></a><a name="event-4502"></a>Scénario : Événement 4502 dans le journal Operations Manager

#### <a name="issue"></a>Problème

Dans le journal des événements **Journaux des applications et des services\Operations Manager**, vous voyez l’événement 4502 et l’EventMessage contenant `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` avec la description suivante :<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Cause

Une cause possible de ce problème est que votre proxy ou votre pare-feu réseau bloque les communications vers Microsoft Azure. Vérifiez que l’ordinateur dispose d’un accès sortant à * **.azure-automation.net** sur le port 443. 

#### <a name="resolution"></a>Résolution

Les journaux d’activité sont stockés localement sur chaque Worker hybride à l’emplacement **C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes**. Vous pouvez vérifier s’il existe des événements d’avertissement ou d’erreur dans les journaux des événements **Journaux des applications et des services\Microsoft-SMA\Operations** et **Journaux des applications et des services\Operations Manager**. Ces journaux indiquent une connectivité ou un autre type de problème qui affecte l’intégration du rôle à Azure Automation ou un problème rencontré dans le cadre d’opérations normales. Pour obtenir de l’aide supplémentaire concernant la résolution des problèmes liés à l’agent Log Analytics, consultez [Résoudre les problèmes liés à l’agent Log Analytics Windows](../../azure-monitor/platform/agent-windows-troubleshoot.md).

Les Workers hybrides envoient une [sortie et des messages de runbook](../automation-runbook-output-and-messages.md) à Azure Automation de la même façon que les travaux Runbook qui s’exécutent dans le cloud envoient une sortie et des messages. Vous pouvez activer les flux détaillés et de progression de la même façon que pour les runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-office-365-through-proxy"></a><a name="no-orchestrator-sandbox-connect-O365"></a>Scénario : Orchestrator.Sandbox.exe ne peut pas se connecter à Office 365 via un proxy

#### <a name="issue"></a>Problème

Un script exécuté sur un Runbook Worker hybride Windows ne peut pas se connecter comme prévu à Office 365 sur un bac à sable Orchestrator. Le script utilise [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) pour la connexion. 

Le bac à sable ne se connecte toujours pas correctement si vous modifiez le fichier **Orchestrator.Sandbox.exe.config** de manière à définir le proxy et la liste de contournement. Un fichier **Powershell_ise.exe.config** comportant les mêmes paramètres de proxy et de liste de contournement semble fonctionner comme prévu. Les journaux SMA (Service Management Automation) et PowerShell ne fournissent aucune information concernant le proxy.

#### <a name="cause"></a>Cause

La connexion aux services de fédération Active Directory (AD FS) sur le serveur ne peut pas contourner le proxy. Souvenez-vous qu'un bac à sable PowerShell s'exécute en tant qu'utilisateur connecté. Toutefois, un bac à sable Orchestrator est fortement personnalisé et peut ignorer les paramètres du fichier **Orchestrator.Sandbox.exe.config**. Il dispose d'un code spécial pour gérer les paramètres de proxy de l'ordinateur ou de l'agent MMA, mais pas pour gérer d'autres paramètres de proxy personnalisés. 

#### <a name="resolution"></a>Résolution

Vous pouvez résoudre le problème du bac à sable Orchestrator en migrant votre script afin d'utiliser les modules Azure AD au lieu du module MSOnline pour les cmdlets PowerShell. Consultez [Migration d'Orchestrator vers Azure Automation (bêta)](https://docs.microsoft.com/azure/automation/automation-orchestrator-migration).

Si vous souhaitez continuer à utiliser les cmdlets du module MSOnline, modifiez votre script pour utiliser [Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7). Spécifiez les valeurs des paramètres `ComputerName` et `Credential`. 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Cette modification du code démarre une toute nouvelle session PowerShell dans le contexte des informations d'identification spécifiées. Elle doit permettre au trafic de passer par un serveur proxy qui authentifie l'utilisateur actif.

>[!NOTE]
>Cette solution rend inutile la manipulation du fichier de configuration du bac à sable. Même si vous parvenez à faire fonctionner le fichier de configuration avec votre script, le fichier est effacé à chaque mise à jour de l'agent Runbook Worker hybride.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scénario : Runbook Worker hybride ne créant pas de rapports

#### <a name="issue"></a>Problème

Votre ordinateur Runbook Worker hybride est en cours d’exécution, mais vous ne voyez pas les données de pulsation de l’ordinateur dans l’espace de travail.

L’exemple de requête suivant montre les ordinateurs d’un espace de travail et leur dernière pulsation :

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Cause

Ce problème peut être lié à un cache endommagé sur le Runbook Worker hybride.

#### <a name="resolution"></a>Résolution

Pour résoudre ce problème, connectez-vous au Runbook Worker hybride et exécutez le script suivant. Ce script arrête l’agent Microsoft Monitoring Agent supprime son cache et redémarre le service. Cette action force le Runbook Worker hybride à télécharger de nouveau sa configuration à partir d’Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-hybrid-runbook-worker"></a><a name="already-registered"></a>Scénario : Vous ne pouvez pas ajouter un Runbook Worker hybride

#### <a name="issue"></a>Problème

Le message suivant s’affiche lorsque vous tentez d’ajouter un Runbook Worker hybride à l’aide de la cmdlet `Add-HybridRunbookWorker`.

```error
Machine is already registered
```

#### <a name="cause"></a>Cause

Ce problème peut venir du fait que l’ordinateur est déjà inscrit avec un autre compte Automation ou que vous essayez peut-être de rajouter le Runbook Worker hybride après l’avoir supprimé d’un ordinateur.

#### <a name="resolution"></a>Résolution

Pour résoudre ce problème, supprimez la clé de Registre suivante, puis redémarrez `HealthService` et réessayez la cmdlet `Add-HybridRunbookWorker`.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.