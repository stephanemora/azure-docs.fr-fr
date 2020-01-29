---
title: Détecter un problème avec Azure Update Management
description: Découvrez comment détecter et résoudre les problèmes rencontrés avec la solution Update Management dans Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5ee1a20d4a3c46cab484b03b5fcc212a79d19047
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513267"
---
# <a name="troubleshooting-issues-with-update-management"></a>Résolution des problèmes rencontrés avec Update Management

Cet article traite des solutions aux problèmes que vous pourriez rencontrer en utilisant Update Management.

Il existe un utilitaire de résolution des problèmes qui permet à l’agent Worker hybride de déterminer le problème sous-jacent. Pour en savoir plus sur l’utilitaire de résolution des problèmes, consultez [Résoudre les problèmes de l’agent de mise à jour](update-agent-issues.md). Pour tous les autres problèmes, utilisez les conseils de dépannage suivants.

Si vous rencontrez des problèmes quand vous essayez d’intégrer la solution sur une machine virtuelle, recherchez dans le journal **Operations Manager** sous **Journaux des applications et des services** sur l’ordinateur local des événements avec l’ID d’événement 4502 et des détails sur les événements qui contiennent **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

La section suivante met en évidence des messages d’erreur spécifiques et des résolutions possibles pour chacun d’eux. Pour d’autres problèmes d’intégration, consultez [Résoudre les problèmes d’intégration des solutions](onboarding.md).

## <a name="nologs"></a>Scénario : Les machines ne s’affichent pas dans le portail sous Update Management

### <a name="issue"></a>Problème

Vous êtes confronté aux symptômes suivants :

* Votre ordinateur affiche l’état **Non configuré** dans la vue Update Management d’une machine virtuelle.

* Vos ordinateurs ne s’affichent pas dans la vue Update Management de votre compte Azure Automation.

* Vous avez des ordinateurs qui s’affichent comme **Non évaluées** sous **Conformité**. Toutefois, les données de pulsation s’affichent dans les journaux d’Azure Monitor pour le Runbook Worker hybride, mais pas pour Update Management.

### <a name="cause"></a>Cause :

Cela peut provenir de problèmes de configuration locaux ou d’une configuration d’étendue incorrecte.

Vous devrez peut-être réinscrire et réinstaller le Runbook Worker hybride.

Vous avez peut-être défini dans votre espace de travail un quota qui a été atteint et empêche le stockages d’autres données.

### <a name="resolution"></a>Résolution

* Exécutez l’utilitaire de résolution des problèmes pour [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), selon le système d’exploitation utilisé.

* Assurez-vous que votre ordinateur est associé à l’espace de travail approprié. Pour obtenir des conseils sur la façon de vérifier cet aspect, consultez l’article [Vérifier la connectivité de l’agent à Log Analytics](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-log-analytics). Assurez-vous également que cet espace de travail est associé à votre compte Azure Automation. Pour le confirmer, accédez à votre compte Automation et sélectionnez **Espace de travail lié** sous **Ressources associées**.

* Vérifiez que les ordinateurs apparaissent dans votre espace de travail Log Analytics. Exécutez la requête suivante dans l’espace de travail Log Analytics lié à votre compte Automation :

  ```loganalytics
  Heartbeat
  | summarize by Computer, Solutions
  ```
  Si vous ne voyez pas votre ordinateur dans les résultats de la requête, c’est qu’il n’a pas été archivé récemment, ce qui signifie qu’il y a probablement un problème de configuration local et que vous devriez [réinstaller l’agent](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows). Si votre ordinateur s’affiche dans les résultats de requête, vérifiez la configuration d’étendue spécifiée dans l’élément de liste suivant.

* Recherchez les problèmes de configuration d’étendue. La [configuration d’étendue](../automation-onboard-solutions-from-automation-account.md#scope-configuration) détermine les ordinateurs configurés pour la solution. Si votre ordinateur s’affiche dans votre espace de travail, mais pas dans le portail **Update Management**, vous devrez configurer les étendues pour qu’elles ciblent les ordinateurs concernés. Pour savoir comment procéder, consultez [Intégrer des machines dans l’espace de travail](../automation-onboard-solutions-from-automation-account.md#onboard-machines-in-the-workspace).

* Dans votre espace de travail, exécutez la requête suivante :

  ```loganalytics
  Operation
  | where OperationCategory == 'Data Collection Status'
  | sort by TimeGenerated desc
  ```
  Si vous obtenez un résultat `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, un quota défini sur votre espace de travail a été atteint, entraînant l’arrêt de l’enregistrement des données. Dans votre espace de travail, accédez à la section **Utilisation et estimation des coûts** > **Gestion du volume de données** et vérifiez le quota défini ou supprimez-le.

* Si ces étapes ne résolvent pas votre problème, suivez les étapes de la section [Déployer un Runbook Worker hybride Windows](../automation-windows-hrw-install.md) pour réinstaller le Worker hybride pour Windows. Ou, pour Linux, [Déployer un Runbook Worker hybride Linux](../automation-linux-hrw-install.md).

## <a name="rp-register"></a>Scénario : Impossible d’inscrire le fournisseur de ressources Automation pour les abonnements

### <a name="issue"></a>Problème

Lorsque vous travaillez avec des solutions dans votre compte Automation, vous rencontrez l’erreur suivante :

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Cause :

Le fournisseur de ressources Automation n’est pas inscrit dans l’abonnement.

### <a name="resolution"></a>Résolution

Pour inscrire le fournisseur de ressources Automation, effectuez les opérations suivantes dans le portail Azure :

1. Dans la liste de services Azure en bas du portail, sélectionnez **Tous les services**, puis sélectionnez **Abonnements** dans le groupe de services Général.
2. Sélectionnez votre abonnement.
3. Sous **Paramètres**, sélectionnez **Fournisseurs de ressources**.
4. Dans la liste des fournisseurs de ressources, vérifiez que le fournisseur de ressources **Microsoft.Automation** est inscrit.
5. S’il ne l’est pas, inscrivez le **fournisseur Microsoft.Automation** en suivant les étapes de la section [Résoudre les erreurs d’inscription du fournisseur de ressources](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="components-enabled-not-working"></a>Scénario : Les composants de la solution Update Management ont été activés et cette machine virtuelle est en cours de configuration

### <a name="issue"></a>Problème

Le message suivant continue de s’afficher sur une machine virtuelle 15 minutes après l’intégration :

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Cause :

Cette erreur peut se produire pour les raisons suivantes :

- La communication avec le compte Automation est bloquée.
- La machine virtuelle embarquée peut provenir d’un ordinateur cloné qui n’a pas été préparé avec Sysprep à l’aide de la version de Microsoft Monitoring Agent (MMA) installée.

### <a name="resolution"></a>Résolution

1. Accédez à [Planification réseau](../automation-hybrid-runbook-worker.md#network-planning) pour savoir quelles adresses et quels ports doivent être autorisés pour le fonctionnement d’Update Management.
2. Si vous utilisez une image clonée :
   1. Dans votre espace de travail Log Analytics, supprimez la machine virtuelle de la recherche enregistrée pour la configuration d’étendue `MicrosoftDefaultScopeConfig-Updates` si celle-ci apparaît. Les recherches enregistrées se trouvent sous la section **Général** de votre espace de travail.
   2. Exécutez `Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force`.
   3. Exécutez `Restart-Service HealthService` pour redémarrer `HealthService`. Cela recrée la clé et génère un nouvel UUID.
   4. Si cette approche ne fonctionne pas, commencez par exécuter Sysprep sur l’image, puis installez MMA.

## <a name="multi-tenant"></a>Scénario : Vous recevez une erreur d’abonnement lié lorsque vous créez un déploiement de mise à jour pour les ordinateurs d’un autre locataire Azure.

### <a name="issue"></a>Problème

Vous rencontrez l’erreur suivante lorsque vous tentez de créer un déploiement de mise à jour pour les ordinateurs d’un autre locataire Azure :

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Cause :

Cette erreur se produit lorsque vous créez un déploiement de mise à jour dans lequel des machines virtuelles Azure d’un autre locataire sont incluses.

### <a name="resolution"></a>Résolution

Utilisez la solution de contournement suivante pour planifier ces éléments. Vous pouvez utiliser l’applet de commande [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) avec le commutateur `-ForUpdate` pour créer une planification. Ensuite, utilisez l’applet de commande [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) et transférez les ordinateurs de l’autre locataire vers le paramètre `-NonAzureComputer`. L’exemple suivant vous montre comment procéder :

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="node-reboots"></a>Scénario : Redémarrages inexpliqués

### <a name="issue"></a>Problème

Même si vous avez défini l’option **Contrôle de redémarrage** sur **Ne jamais redémarrer**, les ordinateurs sont toujours redémarrés après l’installation de mises à jour.

### <a name="cause"></a>Cause :

Windows Update peut être modifié par plusieurs clés de Registre dont chacune peut modifier les comportements de redémarrage.

### <a name="resolution"></a>Résolution

Passez en revue les clés de Registre répertoriées sous [Configuration automatique des mises à jour par modification du Registre](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) et [Clés de Registre utilisées pour gérer le redémarrage](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) pour vous assurer que vos ordinateurs sont configurés correctement.

## <a name="failed-to-start"></a>Scénario : Un ordinateur indique « Échec du démarrage » dans un déploiement de mise à jour

### <a name="issue"></a>Problème

Un ordinateur affiche un état **Échec du démarrage**. Lorsque vous affichez les détails spécifiques de l’ordinateur, vous voyez l’erreur suivante :

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Cause :

Ce problème peut se produire pour l’une des raisons suivantes :

* La machine n’existe plus.
* La machine est éteinte et injoignable.
* Le Worker hybride sur l’ordinateur n’est pas joignable, car l’ordinateur a un problème de connectivité réseau.
* MMA a été mis à jour, ce qui a modifié le paramètre SourceComputerId.
* L’exécution de votre mise à jour a été limitée si vous avez atteint la limite de 2 000 tâches simultanées dans un compte Automation. Chaque déploiement est considéré comme une tâche, et chaque ordinateur dans un déploiement de mise à jour est également considéré comme une tâche. Toutes les autres tâches d’automatisation ou de déploiement de mise à jour en cours d’exécution dans votre compte Automation comptent dans la limite des tâches qu’il est possible d’effectuer simultanément.

### <a name="resolution"></a>Résolution

Lorsque c’est possible, utilisez les [groupes dynamiques](../automation-update-management-groups.md) pour vos déploiements de mise à jour. De plus :

* Vérifiez que l’ordinateur existe toujours et qu’il est accessible. S’il n’existe pas, modifiez votre déploiement et supprimez-le.
* Pour obtenir une liste des ports et adresses requis pour Update Management et savoir comment vérifier si votre ordinateur répond à ces exigences, consultez la section [Planification réseau](../automation-update-management.md#ports).
* Exécutez la requête suivante dans Log Analytics pour rechercher les ordinateurs de votre environnement dont le paramètre `SourceComputerId` a été modifié. Recherchez les ordinateurs présentant la même valeur `Computer`, mais une valeur `SourceComputerId` différente. 

   ```loganalytics
   Heartbeat | where TimeGenerated > ago(30d) | distinct SourceComputerId, Computer, ComputerIP
   ```
   Une fois les ordinateurs affectés identifiés, vous devez modifier les déploiements de mise à jour ciblant ces ordinateurs, puis les supprimer et les rajouter afin que `SourceComputerId` reflète la valeur correcte.

## <a name="updates-nodeployment"></a>Scénario : Les mises à jour sont installées sans déploiement

### <a name="issue"></a>Problème

Lorsque vous inscrivez un ordinateur Windows à Update Management, vous voyez les mises à jour installées sans déploiement.

### <a name="cause"></a>Cause :

Sur Windows, les mises à jour sont installées automatiquement dès qu’elles sont disponibles. Ce comportement peut être à l’origine d’une certaine confusion si vous n’avez pas planifié de déploiement de mise à jour sur l’ordinateur.

### <a name="resolution"></a>Résolution

La valeur par défaut de 4 est affectée à la clé de Registre `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` : **télécharger et installer automatiquement**.

Nous recommandons aux clients Update Management d’affecter la valeur 3 à cette clé : **télécharger automatiquement, mais ne pas installer automatiquement**.

Pour plus d’informations, consultez [Configuration des mises à jour automatiques](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="machine-already-registered"></a>Scénario : La machine est déjà inscrite sur un autre compte

### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant :

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Cause :

L’ordinateur est déjà intégré à un autre espace de travail pour Update Management.

### <a name="resolution"></a>Résolution

1. Suivez les étapes de [Machines n’apparaissent pas dans le portail sous Update Management](#nologs) pour vous assurer que l’ordinateur est associé au bon espace de travail.
2. Nettoyez les anciens artefacts sur l’ordinateur en [supprimant le groupe de runbooks hybrides](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group), puis réessayez.

## <a name="machine-unable-to-communicate"></a>Scénario : L’ordinateur ne peut pas communiquer avec le service

### <a name="issue"></a>Problème

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

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Cause :

Un proxy, une passerelle ou un pare-feu bloquent peut-être la communication réseau. 

### <a name="resolution"></a>Résolution

Passez en revue votre réseau, et vérifiez que les ports et les adresses appropriés sont autorisés. Consultez [Configuration réseau requise](../automation-hybrid-runbook-worker.md#network-planning) pour obtenir la liste des ports et des adresses nécessaires pour Update Management et pour les Runbooks Worker hybrides.

## <a name="unable-to-create-selfsigned-cert"></a>Scénario : Impossible de créer un certificat auto-signé

### <a name="issue"></a>Problème

Vous recevez un des messages d’erreur suivants :

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Cause :

Le Runbook Worker hybride n’a pas pu générer de certificat autosigné.

### <a name="resolution"></a>Résolution

Vérifiez que le compte système a accès en lecture au dossier **C:\ProgramData\Microsoft\Crypto\RSA**, puis réessayez.

## <a name="mw-exceeded"></a>Scénario : Échec de la mise à jour planifiée avec une erreur MaintenanceWindowExceeded

### <a name="issue"></a>Problème

La fenêtre de maintenance par défaut pour les mises à jour est de 120 minutes. Vous pouvez augmenter la taille de la fenêtre de maintenance à un maximum de 6 heures, soit 360 minutes.

### <a name="resolution"></a>Résolution

Modifiez tous les déploiements de mise à jour planifiés ayant échoué et augmentez la taille de la fenêtre de maintenance.

Pour plus d’informations sur les fenêtres de maintenance, consultez [Installer des mises à jour](../automation-tutorial-update-management.md#schedule-an-update-deployment).

## <a name="hresult"></a>Scénario : L’ordinateur s’affiche comme « Non évalué » et présente une exception HResult

### <a name="issue"></a>Problème

* Certaines de vos machines portent la mention **non évaluée** sous **Conformité**, et vous voyez un message d’exception.
* Vous avez des ordinateurs qui s’affichent comme non évalués.
* Vous voyez un code d’erreur HRESULT dans le portail.

### <a name="cause"></a>Cause :

L’agent de mise à jour (agent Windows Update sur Windows ; le gestionnaire de package pour une distribution Linux) n’est pas configuré correctement. Update Management s’appuie sur l’agent de mise à jour de l’ordinateur pour fournir les mises à jour nécessaires, l’état du correctif et les résultats des correctifs déployés. Sans ces informations, Update Management ne peut pas générer de rapport exact sur les correctifs nécessaires ou installés.

### <a name="resolution"></a>Résolution

Essayez d’exécuter des mises à jour localement sur l’ordinateur. En cas d’échec, cela signifie généralement qu’il y a une erreur de configuration de l’agent de mise à jour.

C’est souvent dû à des problèmes de configuration de réseau et de pare-feu. Essayez ce qui suit :

* Pour Linux, consultez la documentation appropriée pour vous assurer que vous pouvez atteindre le point de terminaison réseau de votre référentiel de packages.
* Pour Windows, vérifiez la configuration de votre agent, comme indiqué dans [Les mises à jour ne se téléchargent pas à partir du point de terminaison intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).
  * Si les ordinateurs sont configurés pour Windows Update, assurez-vous que vous pouvez atteindre les points de terminaison décrits dans [Problèmes liés à HTTP/au proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Si le ou les ordinateurs sont configurés pour Windows Server Update Services (WSUS), assurez-vous que vous pouvez atteindre le serveur WSUS configuré par la [clé de Registre WUServer](/windows/deployment/update/waas-wu-settings).

Si vous voyez un HRESULT, double-cliquez sur l’exception affichée en rouge pour voir le message d’exception. Consultez le tableau suivant pour connaître les solutions possibles ou les actions recommandées :

|Exception  |Résolution ou action  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Recherchez le code d’erreur pertinent dans la [liste des codes d’erreur de Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) pour obtenir des détails supplémentaires sur la cause de l’exception.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Ceci indique des problèmes de connectivité réseau. Assurez-vous que votre ordinateur dispose de la connectivité réseau pour Update Management. Consultez la section [Planification réseau](../automation-update-management.md#ports) pour obtenir la liste des ports et des adresses.        |
|`0x8024001E`| L’opération de mise à jour a échoué, car le service ou le système était en cours d’arrêt.|
|`0x8024002E`| Le service Windows Update est désactivé.|
|`0x8024402C`     | Si vous utilisez un serveur WSUS, assurez-vous que les valeurs de Registre de `WUServer` et `WUStatusServer` sous la clé de Registre `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` spécifient le bon serveur WSUS.        |
|`0x80072EE2`|Il y a un problème de connectivité réseau ou problème de communication avec un serveur WSUS configuré. Vérifiez les paramètres WSUS et assurez-vous que le service est accessible à partir du client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Assurez-vous que le service Windows Update (wuauserv) est en cours d’exécution et qu’il n’est pas désactivé.        |
|`0x80070005`| Une erreur d'accès refusé peut être due à l’une des raisons suivantes :<br> Ordinateur infecté<br> Les paramètres Windows Update ne sont pas correctement configurés<br> Erreur d’autorisation de fichier avec le dossier %WinDir%\SoftwareDistribution<br> Espace disque insuffisant sur le lecteur système (C:)
|Toute autre exception générique     | Effectuez une recherche sur Internet pour découvrir des solutions possibles et contactez votre support technique local.         |

L’examen du fichier %Windir%\Windowsupdate.log peut également vous aider à déterminer les causes possibles. Pour savoir comment lire le journal, consultez l’article [Comment lire le fichier Windowsupdate.log](https://support.microsoft.com/en-ca/help/902093/how-to-read-the-windowsupdate-log-file).

Vous pouvez également télécharger et exécuter l’[utilitaire de résolution des problèmes de Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) pour vérifier si l’ordinateur présente des problèmes liés à Windows Update.

> [!NOTE]
> La documentation de l’[outil de résolution des problèmes de Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indique qu’il est destiné à être utilisé sur des clients Windows, mais il fonctionne également sur Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scénario : L’exécution de la mise à jour retourne l’état « Échec » (Linux)

### <a name="issue"></a>Problème

Une mise à jour démarre mais rencontre des erreurs pendant l’exécution.

### <a name="cause"></a>Cause :

Causes possibles :

* Le Gestionnaire de package n’est pas sain.
* L’agent de mise à jour (WUA pour Windows, gestionnaire de package spécifique à la distribution pour Linux) est mal configuré.
* Des packages spécifiques interfèrent avec la mise à jour corrective informatique.
* L’ordinateur est inaccessible.
* Les mises à jour ont des dépendances qui n’ont pas été résolues.

### <a name="resolution"></a>Résolution

Si des échecs se produisent pendant l’exécution d’une mise à jour après qu’elle a démarré avec succès, [vérifiez la sortie du travail](../manage-update-multi.md#view-results-of-an-update-deployment) de la machine affectée dans l’exécution. Vous trouverez peut-être des messages d’erreur spécifiques provenant de votre machine, effectuer des recherches sur ces erreurs et entreprendre des actions pour les résoudre. Update Management nécessite que le Gestionnaire de package soit sain pour que les déploiements des mises à jour réussisse.

Si des correctifs, des packages ou des mises à jour spécifiques s’affichent immédiatement avant l’échec du travail, vous pouvez essayer [d’exclure](../automation-tutorial-update-management.md#schedule-an-update-deployment) ces éléments du déploiement de mise à jour suivant. Pour collecter des informations de journal à partir de Windows Update, consultez [Fichiers journaux de Windows Update](/windows/deployment/update/windows-update-logs).

Si vous ne pouvez pas résoudre un problème de mise à jour corrective, pour pouvoir le résoudre, faites une copie du fichier journal suivant et enregistrez-le *avant* le démarrage du déploiement de mise à jour suivant :

```bash
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="patches-arent-installed"></a>Les correctifs ne sont pas installés

### <a name="machines-dont-install-updates"></a>Les ordinateurs n’installent pas les mises à jour

* Essayez d’exécuter les mises à jour directement sur la machine. Si l’ordinateur ne parvient pas à se mettre à jour, consultez la [liste des erreurs potentielles dans le guide de résolution des problèmes](https://docs.microsoft.com/azure/automation/troubleshoot/update-management#hresult).
* Si les mises à jour s’exécutent localement, essayez de supprimer et de réinstaller l’agent sur l’ordinateur en suivant les instructions données dans [Supprimer une machine virtuelle d’Update Management](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-browse#clean-up-resources).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Je sais que des mises à jour sont disponibles, mais elles n’apparaissent pas comme étant disponibles sur mes ordinateurs

* Cela se produit souvent lorsque les machines sont configurées pour recevoir des mises à jour de WSUS ou de Microsoft Endpoint Configuration Manager, mais que WSUS et Configuration Manager n’ont pas approuvé les mises à jour.
* Vous pouvez vérifier si les machines sont configurées pour WSUS et SCCM en [faisant une référence croisée de la clé de Registre UseWUServer avec les clés de Registre de la section « Configuration automatique des mises à jour par modification du Registre » de cet article](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).
* Si les mises à jour ne sont pas approuvées dans WSUS, elles ne seront pas installées. Vous pouvez rechercher les mises à jour non approuvées dans Log Analytics en exécutant la requête suivante :

  ```loganalytics
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Les mises à jour s’affichent comme étant installées, mais je ne les trouve pas sur ma machine

* Les mises à jour sont souvent remplacées par d’autres mises à jour. Pour plus d’informations, consultez [La mise à jour est remplacée](https://docs.microsoft.com/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) dans le guide de résolution des problèmes liés à Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Installation de mises à jour par classification sur Linux

* Le déploiement de mises à jour sur Linux par classification (« Mises à jour critiques et de sécurité ») a des limitations importantes, en particulier pour CentOS. Ces limitations sont documentées à la [page Vue d’ensemble d’Update Management](https://docs.microsoft.com/azure/automation/automation-update-management#linux-2).

### <a name="kb2267602-is-consistently-missing"></a>L’article KB2267602 est systématiquement manquant

* L’article KB2267602 a trait à la [mise à jour de définitions Windows Defender](https://www.microsoft.com/wdsi/definitions). Il est mis à jour quotidiennement.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou que vous ne pouvez pas le résoudre, utilisez un des canaux suivants pour obtenir une l’aide supplémentaire :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées (réponses, support et experts).
* Signaler un incident au support Azure Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
