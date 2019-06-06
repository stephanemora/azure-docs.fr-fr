---
title: Résoudre les erreurs avec Update Management
description: Découvrez comment résoudre les problèmes rencontrés avec Update Management.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 9bcc871ecc9413f02545e6aec4caa6342d563b44
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474575"
---
# <a name="troubleshooting-issues-with-update-management"></a>Résolution des problèmes rencontrés avec Update Management

Cet article traite des solutions pour résoudre les problèmes que vous pouvez rencontrer lors de l’utilisation d’Update Management.

Il existe un utilitaire de résolution des problèmes qui permet à l’agent Worker hybride de déterminer le problème sous-jacent. Pour en savoir plus sur l’utilitaire de résolution des problèmes, consultez [Résoudre les problèmes de l’agent de mise à jour](update-agent-issues.md). Pour tous les autres problèmes, reportez-vous aux informations détaillées ci-dessous concernant des problèmes éventuels.

## <a name="general"></a>Généralités

### <a name="components-enabled-not-working"></a>Scénario : Les composants de la solution « Update Management » ont été activés et cette machine virtuelle est en cours de configuration

#### <a name="issue"></a>Problème

Le message suivant continue de s’afficher sur une machine virtuelle 15 minutes après l’intégration :

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

#### <a name="cause"></a>Cause :

Cette erreur peut être due aux raisons suivantes :

1. La communication avec le compte Automation est bloquée.
2. La machine virtuelle embarquée peut provenir d’une machine clonée qui n’a pas été synchronisée avec la version de Microsoft Monitoring Agent installée.

#### <a name="resolution"></a>Résolution :

1. Pour savoir quelles adresses et quels ports doivent être autorisés pour le fonctionnement d’Update Management, consultez [Planification du réseau](../automation-hybrid-runbook-worker.md#network-planning).
2. Si vous utilisez une image clonée :
   1. Dans votre espace de travail Analytique de journal, supprimez la machine virtuelle à partir de la recherche enregistrée pour la Configuration d’étendue `MicrosoftDefaultScopeConfig-Updates` si elle est affichée. Les recherches enregistrées se trouvent sous la section **Général** de votre espace de travail.
   2. Exécutez `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Exécutez `Restart-Service HealthService` pour redémarrer `HealthService`. Cela recréera la clé et générera un nouvel UUID.
   4. Si cela ne fonctionne pas, l’image de sysprep premier et installer l’agent MMA après coup.

### <a name="multi-tenant"></a>Scénario : Vous recevez une erreur d’abonnement lié lors de la création d’un déploiement de mise à jour pour les machines d’un autre client Azure.

#### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lorsque vous tentez de créer un déploiement de mise à jour pour les machines d’un autre client Azure :

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

#### <a name="cause"></a>Cause :

Cette erreur se produit lorsque vous créez un déploiement de mise à jour dans lequel des machines virtuelles d’un autre client sont incluses.

#### <a name="resolution"></a>Résolution :

Vous devez utiliser la solution de contournement suivante pour planifier la mise à jour. Vous pouvez utiliser la cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) avec le commutateur `-ForUpdate` pour créer une planification et utiliser la cmdlet [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) et faire passer les machines de l’autre client au paramètre `-NonAzureComputer`. L’exemple suivant vous montre comment procéder :

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

### <a name="nologs"></a>Scénario : Machines n’apparaissent pas dans le portail de gestion de la mise à jour

#### <a name="issue"></a>Problème

Vous pouvez exécuter sur les scénarios suivants :

* Votre ordinateur s’affiche **ne pas configuré** à partir de la vue de gestion de la mise à jour d’une machine virtuelle

* Vos machines sont manquants dans la vue de gestion de la mise à jour de votre compte Automation

* Vous avez des machines avec la mention **non évalué** sous **conformité**, mais vous voyez les données de pulsation dans les journaux Azure Monitor pour les workers hybrides, mais pas Update Management.

#### <a name="cause"></a>Cause :

Cela peut résulter des éventuels problèmes de configuration local ou de Configuration d’étendue configurés incorrectement.

Il est possible que le Runbook Worker hybride doive être réinscrit et réinstallé.

Vous avez défini un quota dans votre espace de travail qui a été atteints et arrêt des données du stockage.

#### <a name="resolution"></a>Résolution :

* Assurez-vous que votre ordinateur est signalé à l’espace de travail correct. Vérifiez quel espace de travail rapports à votre ordinateur. Pour obtenir des instructions sur la façon de vérifier cela, consultez [vérifier la connectivité de l’agent pour l’Analytique de journal](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Ensuite, assurez-vous qu’est l’espace de travail qui est lié à votre compte Azure Automation. Pour vérifier cela, accédez à votre compte Automation, puis cliquez sur **espace de travail lié** sous **les ressources associées**.

* Vérifiez que les ordinateurs apparaissent dans votre espace de travail Analytique de journal. Exécutez la requête suivante dans votre espace de travail Analytique de journal qui est lié à votre compte Automation. Si vous ne voyez pas votre ordinateur dans les résultats de requête, votre ordinateur n’est pas pulsation, ce qui signifie qu’il est très probablement un problème de configuration local. Vous pouvez exécuter la résolution des problèmes pour [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline) selon le système d’exploitation, ou vous pouvez [réinstaller l’agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Si votre ordinateur s’affiche dans les résultats de requête, vous devrez très la configuration de l’étendue spécifiée dans la puce suivante.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Recherchez les problèmes de configuration d’étendue. [Configuration de l’étendue](../automation-onboard-solutions-from-automation-account.md#scope-configuration) détermine quels ordinateurs obtient configurées pour la solution. Si votre ordinateur ne s’affichent dans votre espace de travail mais n’est pas affichaient vous devez configurer la configuration d’étendue pour cibler les ordinateurs. Pour savoir comment procéder, consultez [intégrer des machines dans l’espace de travail](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Si les étapes ci-dessus ne résolvent pas votre problème, suivez les étapes indiquées dans [déployer un Runbook Worker hybride de Windows](../automation-windows-hrw-install.md) pour réinstaller le Worker hybride pour Windows ou [déployer un Runbook Worker hybride de Linux](../automation-linux-hrw-install.md) pour Linux.

* Dans votre espace de travail, exécutez la requête suivante. Si vous voyez le résultat `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` disposer d’un quota défini sur votre espace de travail qui a été atteinte et a cessé de données ne sont pas enregistrées. Dans votre espace de travail, accédez à **l’utilisation et estimation des coûts** > **gestion du volume de données** et vérifier votre quota ou supprimer le quota que vous avez.

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```

## <a name="windows"></a>Windows

Si vous rencontrez des problèmes quand vous essayez d’intégrer la solution sur une machine virtuelle, recherchez dans le journal des évènements **Operations Manager** sous **Journaux des applications et des services\** sur la machine locale des événements avec l’ID d’événement **4502** et un message d’événement contenant **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

La section suivante met en évidence des messages d’erreur spécifiques et une résolution possible pour chacun d’eux. Pour d’autres problèmes d’intégration, consultez [Résoudre les problèmes d’intégration des solutions](onboarding.md).

### <a name="machine-already-registered"></a>Scénario : La machine est déjà inscrite sur un autre compte

#### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant :

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Cause :

La machine est déjà intégrée à un autre espace de travail pour Update Management.

#### <a name="resolution"></a>Résolution :

Éliminez les anciens artefacts sur la machine en [supprimant le groupe de Runbooks hybrides](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), puis réessayez.

### <a name="machine-unable-to-communicate"></a>Scénario : La machine ne peut pas communiquer avec le service

#### <a name="issue"></a>Problème

Vous recevez un des messages d’erreur suivants :

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Cause :

Il est possible qu’un proxy, une passerelle ou un pare-feu bloque la communication réseau.

#### <a name="resolution"></a>Résolution :

Passez en revue votre réseau, et vérifiez que les ports et les adresses appropriés sont autorisés. Consultez [Configuration réseau requise](../automation-hybrid-runbook-worker.md#network-planning) pour obtenir la liste des ports et des adresses nécessaires pour Update Management et pour les Runbooks Worker hybrides.

### <a name="unable-to-create-selfsigned-cert"></a>Scénario : Impossible de créer un certificat auto-signé

#### <a name="issue"></a>Problème

Vous recevez un des messages d’erreur suivants :

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Cause :

Le Runbook Worker hybride n’a pas pu générer de certificat auto-signé.

#### <a name="resolution"></a>Résolution :

Vérifiez que le compte système a accès en lecture au dossier **C:\ProgramData\Microsoft\Crypto\RSA**, puis réessayez.

### <a name="failed-to-start"></a>Scénario : Un ordinateur indique l’échec du démarrage dans un déploiement de mises à jour

#### <a name="issue"></a>Problème

Un ordinateur a le statut **Échec du démarrage** pour un ordinateur. Lorsque vous affichez les détails spécifiques de la machine affiche l’erreur suivante :

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Cause :

Cette erreur peut se produire en raison d’une des raisons suivantes :

* L’ordinateur n’existe plus.
* L’ordinateur est désactivé hors tension et inaccessible.
* L’ordinateur a un problème de connectivité réseau et le worker hybride sur l’ordinateur est inaccessible.
* Il a été mise à jour l’Agent de surveillance de Microsoft qui a modifié le SourceComputerId
* Exécution de votre mise à jour peut avoir été limitée si vous atteignez la limite de 2 000 tâches simultanées dans un compte Automation. Chaque déploiement est considéré comme une tâche et chaque ordinateur dans un nombre de déploiements de mise à jour en tant que tâche. N’importe quel autre travail ou mise à jour déploiement automation en cours d’exécution dans votre nombre de compte Automation dans le nombre maximal de tâches simultanées.

#### <a name="resolution"></a>Résolution :

Lorsque applicables à l’utilisation [groupes dynamiques](../automation-update-management.md#using-dynamic-groups) pour vos déploiements de mise à jour.

* Vérifiez que l’ordinateur existe toujours et est accessible. Si elle n’existe pas, modifiez votre déploiement et supprimez l’ordinateur.
* Consultez la section sur [planification réseau](../automation-update-management.md#ports) pour obtenir la liste des ports et des adresses qui sont nécessaires pour la gestion de la mise à jour et vérifiez que votre ordinateur répond à ces exigences.
* Exécutez la requête suivante dans le journal Analytique pour rechercher les ordinateurs dans votre environnement dont `SourceComputerId` modifié. Recherchez les ordinateurs qui ont le même `Computer` valeur, mais différentes `SourceComputerId` valeur. Une fois que vous trouvez les ordinateurs affectés, vous devez modifier les déploiements de mises à jour qui ciblent ces machines et supprimer et rajouter les machines afin que la `SourceComputerId` reflète la valeur correcte.

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```

### <a name="hresult"></a>Scénario : La machine s’affiche comme non évaluée et présente une exception HResult

#### <a name="issue"></a>Problème

Certaines de vos machines portent la mention **non évaluée** sous **Conformité**, et vous voyez un message d’exception.

#### <a name="cause"></a>Cause :

Windows Update ou WSUS n’est pas correctement configuré sur la machine. Update Management s’appuie sur Windows Update ou WSUS pour fournir les mises à jour nécessaires, l’état du correctif et les résultats des correctifs déployés. Sans ces informations, Update Management ne peut pas générer de rapport exact concernant les correctifs nécessaires ou installés.

#### <a name="resolution"></a>Résolution :

Double-cliquez sur l’exception affichée en rouge pour voir le message d’exception. Consultez le tableau suivant pour connaître les solutions possibles ou les actions appliquer :

|Exception  |Résolution ou action  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Recherchez le code d’erreur pertinent dans la [liste des codes d’erreur de Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) pour obtenir des détails supplémentaires sur la cause de l’exception.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ces erreurs sont des problèmes de connectivité réseau. Assurez-vous que votre machine dispose de la connectivité réseau appropriée pour Update Management. Consultez la section sur la [planification réseau](../automation-update-management.md#ports) pour obtenir la liste des ports et des adresses nécessaires.        |
|`0x8024001E`| L’opération de mise à jour a échoué, car le service ou le système était en cours d’arrêt.|
|`0x8024002E`| Service de mise à jour de Windows est désactivé.|
|`0x8024402C`     | Si vous utilisez un serveur WSUS, assurez-vous que les valeurs de Registre de `WUServer` et `WUStatusServer` sous la clé de Registre `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` utilisent le bon serveur WSUS.        |
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assurez-vous que le service Windows Update (wuauserv) est en cours d’exécution et qu’il n’est pas désactivé.        |
|Toute autre exception générique     | Faites une recherche sur Internet pour découvrir les solutions possibles et contactez votre support technique local.         |

Examiner les `windowsupdate.log` peut vous aider à essayer de déterminer la cause possible ainsi. Pour plus d’informations sur la façon de lire le journal, consultez [comment lire le fichier Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

En outre, vous pouvez télécharger et exécuter l’[utilitaire de résolution des problèmes de Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) pour vérifier si l’ordinateur présente des problèmes Windows Update.

> [!NOTE]
> L’[utilitaire de résolution des problèmes Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) stipule qu’il est destiné aux clients Windows, mais il fonctionne tout aussi bien sur Windows Server.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Scénario : Le démarrage de l’exécution de la mise à jour échoue

#### <a name="issue"></a>Problème

Le démarrage de l’exécution d’une mise à jour échoue sur une machine Linux.

#### <a name="cause"></a>Cause :

Le Worker hybride Linux est défectueux.

#### <a name="resolution"></a>Résolution :

Faites une copie du fichier journal suivant et conservez-le pour résoudre les problèmes :

```bash
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Scénario : L’exécution de la mise à jour démarre, mais rencontre des erreurs

#### <a name="issue"></a>Problème

L’exécution d’une mise à jour démarre, mais rencontre des erreurs pendant l’exécution.

#### <a name="cause"></a>Cause :

Les causes possibles sont :

* Le Gestionnaire de package n’est pas sain
* Des packages spécifiques peuvent interférer avec la mise à jour corrective cloud
* Autres raisons

#### <a name="resolution"></a>Résolution :

Si des échecs se produisent pendant l’exécution d’une mise à jour après qu’elle a démarré avec succès sur Linux, vérifiez la sortie du travail de la machine affectée dans l’exécution. Vous pouvez trouver des messages d’erreur spécifiques provenant du Gestionnaire de package de votre machine, effectuer des recherches sur ces erreurs et entreprendre des actions pour les résoudre. Update Management nécessite que le Gestionnaire de package soit sain pour que les déploiements des mises à jour réussisse.

Dans certains cas, les mises à jour de package peuvent interférer avec Update Management, empêchant le déploiement correct d’une mise à jour. Si vous constatez cela, vous devez exclure ces packages des exécutions de mise à jour futures ou les installer manuellement vous-même.

Si vous ne pouvez pas résoudre un problème de mise à jour corrective, pour pouvoir résoudre le problème, faites une copie du fichier journal suivant et enregistrez-le **avant** le démarrage du déploiement de mise à jour suivant :

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
