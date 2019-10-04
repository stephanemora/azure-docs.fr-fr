---
title: Résoudre les erreurs avec Update Management
description: Découvrez comment résoudre les problèmes rencontrés avec Update Management.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 48d2463eee2caeaae36118bf736d00eed84c897a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186211"
---
# <a name="troubleshooting-issues-with-update-management"></a>Résolution des problèmes rencontrés avec Update Management

Cet article traite des solutions pour résoudre les problèmes que vous pouvez rencontrer lors de l’utilisation d’Update Management.

Il existe un utilitaire de résolution des problèmes qui permet à l’agent Worker hybride de déterminer le problème sous-jacent. Pour en savoir plus sur l’utilitaire de résolution des problèmes, consultez [Résoudre les problèmes de l’agent de mise à jour](update-agent-issues.md). Pour tous les autres problèmes, reportez-vous aux informations détaillées ci-dessous concernant des problèmes éventuels.

## <a name="general"></a>Généralités

### <a name="rp-register"></a>Scénario : Impossible d’inscrire le fournisseur de ressources Automation pour les abonnements

#### <a name="issue"></a>Problème

Vous pouvez recevoir l’erreur suivante lors de l’utilisation de solutions dans votre compte Automation.

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

#### <a name="cause"></a>Cause :

Le fournisseur de ressources Automation n’est pas inscrit dans l’abonnement.

#### <a name="resolution"></a>Résolution :

Vous pouvez inscrire les fournisseurs de ressources Automation en effectuant les étapes suivantes dans le portail Azure :

1. Cliquez sur **Tous les services** en bas de la liste des services Azure, puis sélectionnez **Abonnements** dans le groupe de services _Général_.
2. Sélectionnez votre abonnement.
3. Cliquez sur **Fournisseurs de ressources** sous _Paramètres_.
4. Dans la liste des fournisseurs de ressources, vérifiez que le fournisseur de ressources **Microsoft.Automation** est inscrit.
5. Si le fournisseur n’est pas listé, inscrivez le fournisseur **Microsoft.Automation** avec les étapes listées sous [](/azure/azure-resource-manager/resource-manager-register-provider-errors).

### <a name="mw-exceeded"></a>Scénario : La gestion des mises à jour planifiée a échoué avec l’erreur MaintenanceWindowExceeded

#### <a name="issue"></a>Problème

La fenêtre de maintenance par défaut pour les mises à jour est de 120 minutes. Vous pouvez augmenter la taille de la fenêtre de maintenance à un maximum de six (6) heures, soit 360 minutes.

#### <a name="resolution"></a>Résolution :

Modifiez tous les déploiements de mise à jour planifiés ayant échoué et augmentez la taille de la fenêtre de maintenance.

Pour plus d’informations sur les fenêtres de maintenance, consultez [Installer des mises à jour](../automation-update-management.md#install-updates).

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
   1. Dans votre espace de travail Log Analytics, supprimez la machine virtuelle de la recherche enregistrée pour la configuration d’étendue `MicrosoftDefaultScopeConfig-Updates` si celle-ci apparaît. Les recherches enregistrées se trouvent sous la section **Général** de votre espace de travail.
   2. Exécutez `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`
   3. Exécutez `Restart-Service HealthService` pour redémarrer `HealthService`. Cela recréera la clé et générera un nouvel UUID.
   4. Si cela ne fonctionne pas, commencez par exécuter sysprep sur l’image et installez l’agent MMA ensuite.

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

### <a name="updates-nodeployment"></a>Scénario : Installation des mises à jour sans déploiement

### <a name="issue"></a>Problème

Lorsque vous inscrivez une machine Windows à Update Management, vous pouvez constater l’installation des mises à jour sans déploiement.

### <a name="cause"></a>Cause :

Sous Windows, les mises à jour sont installées automatiquement dès qu’elles sont disponibles. Cela peut provoquer une certaine confusion si vous n’avez pas planifié de déploiement de mise à jour sur la machine.

### <a name="resolution"></a>Résolution :

La clé de Registre Windows `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` est définie par défaut sur « 4 » - **téléchargement automatique et installation**.

Nous recommandons aux clients Update Management de définir cette clé sur « 3 » - **téléchargement automatique, mais ne pas installer automatiquement**.

Pour plus d’informations, consultez [Configuration des mises à jour automatiques](https://docs.microsoft.com/en-us/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

### <a name="nologs"></a>Scénario : Les machines ne s’affichent pas dans le portail sous Update Management

#### <a name="issue"></a>Problème

Vous pourriez rencontrer les scénarios suivants :

* Votre machine affiche l’état **Non configuré** dans la vue Update Management d’une machine virtuelle

* Vos machines ne s’affichent pas dans la vue Update Management de votre compte Automation

* Vous avez des machines avec la mention **Non évaluée** sous **Conformité**, par contre vous voyez les données de pulsation dans les journaux Azure Monitor pour le Runbook Worker hybride, mais pas pour Update Management.

#### <a name="cause"></a>Cause :

Cela peut provenir de problèmes de configuration locaux éventuels ou d’une configuration d’étendue incorrecte.

Il est possible que le Runbook Worker hybride doive être réinscrit et réinstallé.

Vous avez peut-être défini un quota dans votre espace de travail qui a été atteint et a provoqué l’arrêt du stockage des données.

#### <a name="resolution"></a>Résolution :

* Assurez-vous que votre machine est associée à l’espace de travail approprié. Vérifiez l’espace de travail auquel votre machine est associée. Pour savoir comment faire, consultez l’article [Vérifier la connectivité de l’agent à Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Ensuite, assurez-vous qu’il s’agit de l’espace de travail associé à votre compte Azure Automation. Pour vérifier cela, rendez-vous dans votre compte Automation et sous **Ressources connexes**, sélectionnez **Espace de travail lié**.

* Vérifiez que les machines apparaissent dans votre espace de travail Log Analytics. Exécutez la requête suivante dans l’espace de travail Log Analytics lié à votre compte Automation. Si vous ne voyez pas votre machine dans les résultats de requête, c’est qu’elle n’a pas de pulsation, ce qui signifie qu’il existe très probablement un problème de configuration local. Vous pouvez exécuter l’utilitaire de résolution des problèmes pour [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline) selon le système d’exploitation utilisé, ou vous pouvez [réinstaller l’agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Si votre machine s’affiche dans les résultats de requête, vérifiez la configuration d’étendue, tel qu’indiqué dans l’élément de liste suivant.

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```

* Recherchez les problèmes de configuration d’étendue. La [configuration d’étendue](../automation-onboard-solutions-from-automation-account.md#scope-configuration) détermine les machines configurées pour la solution. Si votre machine s’affiche dans votre espace de travail, mais pas ailleurs, vous devrez configurer les étendues pour qu’elles ciblent les machines concernées. Pour savoir comment procéder, consultez [Intégrer des machines dans l’espace de travail](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Si les étapes ci-dessus ne résolvent pas votre problème, suivez les étapes de la section [Déployer un Runbook Worker hybride Windows](../automation-windows-hrw-install.md) pour réinstaller le Worker hybride sous Windows ou celles de la section [Déployer un Runbook Worker hybride Linux](../automation-linux-hrw-install.md) pour Linux.

* Dans votre espace de travail, exécutez la requête suivante. Si vous voyez le résultat `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, un quota défini sur votre espace de travail a été atteint, entraînant l’arrêt de l’enregistrement des données. Dans votre espace de travail, accédez à la section **Utilisation et estimation des coûts** > **Gestion du volume de données** et vérifiez le quota défini ou supprimez-le.

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

### <a name="failed-to-start"></a>Scénario : Une machine indique l’échec du démarrage dans un déploiement de mise à jour

#### <a name="issue"></a>Problème

Une machine présente l’état **Échec du démarrage**. Lorsque vous affichez les détails spécifiques de la machine, vous voyez l’erreur suivante :

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

#### <a name="cause"></a>Cause :

Cette erreur peut se produire pour l’une des raisons suivantes :

* La machine n’existe plus.
* La machine est éteinte et injoignable.
* La machine présente un problème de connectivité réseau et le worker hybride de cette dernière est injoignable.
* Microsoft Monitoring Agent a été mis à jour, ce qui a modifié le paramètre SourceComputerId
* L’exécution de la mise à jour peut avoir été limitée si vous avez atteint la limite de 2 000 tâches simultanées dans un compte Automation. Chaque déploiement est considéré comme une tâche et chaque machine dans un déploiement de mise à jour est considéré comme une tâche également. Toutes les autres tâches d’automatisation ou de déploiement de mise à jour en cours d’exécution dans votre compte Automation comptent dans la limite des tâches qu’il est possible d’effectuer simultanément.

#### <a name="resolution"></a>Résolution :

Lorsque c’est possible, utilisez les [groupes dynamiques](../automation-update-management.md#using-dynamic-groups) pour vos déploiements de mise à jour.

* Vérifiez que la machine existe toujours et qu’elle est accessible. Si elle n’existe pas, modifiez votre déploiement et supprimez-la.
* Pour obtenir une liste des ports et adresses requis avec Update Management et savoir comment vérifier si votre machine répond à ces exigences, consultez la section sur la [planification réseau](../automation-update-management.md#ports).
* Exécutez la requête suivante dans Log Analytics pour rechercher les machines de votre environnement dont le paramètre `SourceComputerId` a été modifié. Recherchez les ordinateurs présentant la même valeur `Computer`, mais une valeur `SourceComputerId` différente. Une fois les machines affectées identifiées, vous devez modifier les déploiements de mise à jour ciblant ces machines, puis supprimer et rajouter les machines afin que la valeur `SourceComputerId` soit correcte.

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
|`0x8024002E`| Le service Windows Update est désactivé.|
|`0x8024402C`     | Si vous utilisez un serveur WSUS, assurez-vous que les valeurs de Registre de `WUServer` et `WUStatusServer` sous la clé de Registre `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` utilisent le bon serveur WSUS.        |
|`0x80072EE2`|Problème de connectivité réseau ou problème de communication avec un serveur WSUS configuré. Vérifiez les paramètres WSUS et assurez-vous qu’il est accessible à partir du client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assurez-vous que le service Windows Update (wuauserv) est en cours d’exécution et qu’il n’est pas désactivé.        |
|Toute autre exception générique     | Faites une recherche sur Internet pour découvrir les solutions possibles et contactez votre support technique local.         |

Examiner le fichier `windowsupdate.log` peut également vous aider à essayer de déterminer la cause possible. Pour savoir comment lire le journal, consultez l’article [Comment faire pour lire le fichier Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

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

## <a name="patches-are-not-installed"></a>Les correctifs ne sont pas installés

### <a name="machines-do-not-install-updates"></a>Les machines n’installent pas les mises à jour

* Essayez d’exécuter les mises à jour directement sur la machine. Si la machine ne se met pas à jour, consultez la [liste des erreurs potentielles dans le guide de résolution des problèmes](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Si les mises à jour s’exécutent localement, essayez de supprimer et de réinstaller l’agent sur la machine en suivant les instructions dans [« Supprimer une machine virtuelle d’Update Management »](https://docs.microsoft.com/azure/automation/automation-update-management#remove-a-vm-from-update-management).

### <a name="i-know-updates-are-available-but-they-dont-show-as-needed-on-my-machines"></a>Je sais que des mises à jour sont disponibles, mais elles n’apparaissent pas comme étant nécessaires sur mes machines

* Cela se produit souvent si les machines sont configurées pour obtenir les mises à jour depuis WSUS/SCCM, mais que WSUS/SCCM n’a pas approuvé les mises à jour.
* Vous pouvez vérifier si les machines sont configurées pour WSUS/SCCM en [faisant une référence croisée de la clé de Registre « UseWUServer » avec les clés de Registre de la section « Configuration automatique des mises à jour par modification du Registre » de ce document](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>**Les mises à jour s’affichent comme étant installées, mais je ne les trouve pas sur ma machine**

* Les mises à jour sont souvent remplacées par d’autres mises à jour. Pour plus d’informations, consultez [« La mise à jour est remplacée » dans le guide de résolution des problèmes de mise à jour de Windows](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)

### <a name="installing-updates-by-classification-on-linux"></a>**Installation de mises à jour par classification sur Linux**

* Le déploiement de mises à jour sur Linux par classification (« Mises à jour critiques et de sécurité ») a des limitations importantes, en particulier pour CentOS. Ces [limitations sont documentées dans la page Vue d’ensemble d’Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2)

### <a name="kb2267602-is-consistently--missing"></a>**L’article KB2267602 est systématiquement manquant**

* L’article KB2267602 a trait à la [mise à jour de définitions Windows Defender](https://www.microsoft.com/wdsi/definitions). Il est mis à jour quotidiennement.

## <a name="other"></a>Scénario : Mon problème n’est pas listé ci-dessus

### <a name="issue"></a>Problème

Vous rencontrez un problème qui n’est pas résolu par les autres scénarios listés.

### <a name="cause"></a>Cause :

Des clés de Registre manquantes ou mal configurées peuvent entraîner des problèmes avec Update Management.

### <a name="resolution"></a>Résolution :

Supprimez la clé de Registre `HKLM:\SOFTWARE\Microsoft\HybridRunbookWorker` et redémarrez **HealthService**.

Vous pouvez également utiliser les commandes PowerShell suivantes :

```powershell
Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
Restart-Service healthservice
```

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
