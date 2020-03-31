---
title: Détecter un problème d’intégration des solutions de gestion Azure Automation
description: Découvrez comment résoudre les erreurs d’intégration des solutions Update Management, Change Tracking et Inventory.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c949556949e0c187d7c23c4dd32436e245bfbb95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75889330"
---
# <a name="troubleshoot-errors-when-onboarding-update-management-change-tracking-and-inventory"></a>Résolution des erreurs d’intégration des solutions Update Management, Change Tracking et Inventory

Vous pouvez rencontrer des erreurs lors de l’intégration d’une solution telle que Update Management, Change Tracking ou Inventory. Cet article décrit les erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="known-issues"></a>Problèmes connus

### <a name="scenario-renaming-a-registered-node-requires-unregister--register-again"></a><a name="node-rename"></a>Scénario : La modification du nom d’un nœud inscrit requiert l’annulation de l’inscription et une réinscription

#### <a name="issue"></a>Problème

Un nœud est inscrit sur Azure Automation, puis le nom d’ordinateur du système d’exploitation est modifié.  Les rapports du nœud continuent à s’afficher avec le nom d’origine.

#### <a name="cause"></a>Cause

La modification du nom d’un nœud inscrit n’a pas pour effet de modifier son nom dans Azure Automation.

#### <a name="resolution"></a>Résolution

Annulez l’inscription du nœud dans le service State Configuration d’Azure Automation, puis réinscrivez-le.  Les rapports publiés sur le service avant ce délai ne seront plus disponibles.


### <a name="scenario-re-signing-certificates-via-https-proxy-is-not-supported"></a><a name="resigning-cert"></a>Scénario : La nouvelle signature de certificats via un proxy HTTPS n’est pas prise en charge

#### <a name="issue"></a>Problème

Des clients ont signalé que, quand ils se connectent via une solution proxy qui met fin au trafic HTTPS, puis re-chiffre le trafic à l’aide d’un nouveau certificat, le service n’autorise pas la connexion.

#### <a name="cause"></a>Cause

Azure Automation ne prend pas en charge la nouvelle signature de certificats utilisés pour chiffrer le trafic.

#### <a name="resolution"></a>Résolution

Il n’existe aucune solution de contournement pour ce problème.

## <a name="general-errors"></a>Erreurs générales.

### <a name="scenario-onboarding-fails-with-the-message---the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scénario : L'intégration échoue avec le message : La solution ne peut pas être activée

#### <a name="issue"></a>Problème

L’un des messages suivants s’affiche lorsque vous essayez d’intégrer une machine virtuelle à une solution :

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Cause

Cette erreur est due à des autorisations incorrectes ou manquantes sur la machine virtuelle, l’espace de travail ou pour l’utilisateur.

#### <a name="resolution"></a>Résolution

Vérifiez que vous disposez des autorisations appropriées pour intégrer la machine virtuelle. Passez en revue les [autorisations nécessaires pour intégrer des machines](../automation-role-based-access-control.md#onboarding), puis essayez à nouveau d'intégrer la solution. Si vous rencontrez l’erreur `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, assurez-vous de disposer de l’autorisation `Microsoft.OperationalInsights/workspaces/read` pour pouvoir déterminer si la machine virtuelle est intégrée à un espace de travail.

### <a name="scenario-onboarding-fails-with-the-message---failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scénario : L’intégration échoue avec le message : Échec de la configuration du compte Automation pour la journalisation des diagnostics

#### <a name="issue"></a>Problème

Le message suivant s'affiche lorsque vous essayez d'intégrer une machine virtuelle à une solution :

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Cause

Cette erreur peut survenir si le niveau tarifaire ne correspond pas au modèle de facturation de l’abonnement. Pour plus d’informations, consultez [Surveiller l’utilisation et l’estimation des coûts dans Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Résolution

Créez votre espace de travail Log Analytics manuellement et répétez la procédure d’intégration pour sélectionner l’espace de travail créé.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scénario : ComputerGroupQueryFormatError

#### <a name="issue"></a>Problème

Ce code d’erreur signifie que la requête de recherche de groupe d’ordinateurs enregistrée utilisée pour cibler la solution n’a pas été formatée correctement. 

#### <a name="cause"></a>Cause

Il est possible que vous ayez modifié la requête, ou elle peut avoir été modifiée par le système.

#### <a name="resolution"></a>Résolution

Vous pouvez supprimer la requête pour cette solution et réintégrer la solution, ce qui recrée la requête. La requête se trouve dans votre espace de travail, sous **Recherches enregistrées**. Le nom de la requête est **MicrosoftDefaultComputerGroup**, et la catégorie de la requête est le nom de la solution associée à cette requête. Si plusieurs solutions sont activées, **MicrosoftDefaultComputerGroup** s’affiche plusieurs fois sous **Recherches enregistrées**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scénario : PolicyViolation

#### <a name="issue"></a>Problème

Ce code d’erreur signifie que le déploiement a échoué en raison d’une violation d’une ou de plusieurs stratégies.

#### <a name="cause"></a>Cause 

Une stratégie actuellement en place empêche l’opération de se terminer.

#### <a name="resolution"></a>Résolution

Pour déployer correctement la solution, vous devez envisager de modifier la stratégie indiquée. Étant donné qu’il existe plusieurs types de stratégies différents qui peuvent être définis, les modifications spécifiques requises dépendent de la stratégie violée. Par exemple, si une stratégie a été définie sur un groupe de ressources qui interdisait la modification du contenu de certains types de ressources au sein de ce groupe de ressources, vous pourriez, par exemple, effectuer les opérations suivantes :

* Retirer la stratégie.
* Essayer d’effectuer une intégration à un groupe de ressources différent.
* Réviser la stratégie, par exemple:
  * En reciblant la stratégie vers une ressource spécifique (par exemple, vers un compte Automation)
  * en révisant l’ensemble de ressources que cette stratégie a été configurée pour refuser.

Consultez les notifications dans le coin supérieur droit du Portail Azure ou accédez au groupe de ressources comportant votre compte Automation, puis sélectionnez **Déploiements** sous **Paramètres** pour afficher le déploiement mis en échec. Pour en apprendre davantage sur Azure Policy, consultez : [Présentation d’Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scénario : Erreurs lors de la tentative de dissociation d’un espace de travail

#### <a name="issue"></a>Problème

L’erreur suivante s’affiche lorsque vous tentez de dissocier un espace de travail :

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Cause

Cette erreur se produit lorsque des solutions de votre espace de travail Log Analytics qui dépendent de votre compte Automation et de votre espace de travail Log Analytics associé sont toujours actives.

### <a name="resolution"></a>Résolution

Pour résoudre ce problème, vous devrez supprimer les solutions suivantes de votre espace de travail si vous les utilisez :

* Update Management
* Suivi des modifications
* Démarrer/arrêter des machines virtuelles pendant les heures creuses

Une fois les solutions supprimées, vous pouvez dissocier votre espace de travail. De même, il est important de supprimer tous les artefacts existants de ces solutions dans votre espace de travail et votre compte Automation.  

* Update Management
  * Supprimer les déploiements de mises à jour (planifications) de votre compte Automation
* Démarrer/arrêter des machines virtuelles pendant les heures creuses
  * Supprimer tous les verrous sur les composants de solution dans votre compte Automation sous **Paramètres** > **Verrous**.
  * Pour obtenir des instructions supplémentaires concernant la suppression de la solution Démarrer/arrêter des machines virtuelles pendant les heures creuses, consultez la page [Supprimer la solution de démarrage/arrêt des machines virtuelles pendant les heures creuses](../automation-solution-vm-management.md#remove-the-solution).

## <a name="mma-extension-failures"></a><a name="mma-extension-failures"></a>Échecs d’extension MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Lorsque vous déployez une solution, de nombreuses ressources connexes sont déployées en même temps. L’une de ces ressources est l’extension Microsoft Monitoring Agent, ou l’Agent Log Analytics pour Linux. Il s’agit d’extensions de machine virtuelle installées par l’agent invité de la machine virtuelle, qui est chargé de communiquer avec l’espace de travail Log Analytics configuré, en vue d’une coordination ultérieure du téléchargement des fichiers binaires et des autres fichiers sur lesquels dépend la solution que vous intégrez, une fois son exécution lancée.
En général, vous découvrez les erreurs d’installation de MMA ou de l’Agent Log Analytics pour Linux par l’affichage d’une notification dans le hub Notifications. En cliquant sur cette notification, vous obtenez davantage d’informations sur l’échec. L’accès à la ressource Groupes de ressources, puis à l’élément Déploiements qu’elle contient, permet également d’obtenir des informations détaillées sur les échecs de déploiement.
L’installation de l’extension MMA ou de l’Agent Log Analytics pour Linux peut échouer pour diverses raisons, et les étapes à suivre pour résoudre ces échecs varient en fonction du problème. Les étapes de résolution spécifiques sont décrites ci-dessous.

La section suivante décrit différents problèmes que vous pouvez rencontrer lors de l’intégration et qui provoquent l’échec du déploiement de l’extension MMA.

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scénario : Une exception s’est produite lors d’une requête WebClient

L’extension MMA de la machine virtuelle ne peut pas communiquer avec les ressources externes, et le déploiement échoue.

#### <a name="issue"></a>Problème

Voici quelques exemples des messages d’erreur qui peuvent s’afficher :

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Cause

Voici les causes possibles de cette erreur :

* Un proxy configuré dans la machine virtuelle autorise uniquement certains ports.

* Un paramètre du pare-feu a bloqué l’accès aux adresses et aux ports nécessaires.

#### <a name="resolution"></a>Résolution

Vérifiez que les bons ports et adresses sont ouverts à la communication. Pour obtenir la liste des ports et adresses, consultez [Planification de votre réseau](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-a-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scénario : L’installation a échoué en raison de problèmes temporaires liés à l’environnement

L’installation de l’extension Microsoft Monitoring Agent a échoué pendant le déploiement, car une autre installation ou action bloque l’installation.

#### <a name="issue"></a>Problème

Voici quelques exemples des messages d’erreur qui peuvent s’afficher :

```error
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```error
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Cause

Voici les causes possibles de cette erreur :

* Une autre installation est en cours d’exécution.
* Le redémarrage du système est déclenché pendant le déploiement du modèle.

#### <a name="resolution"></a>Résolution

Cette erreur est de nature temporaire. Recommencez le déploiement pour installer l’extension.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scénario : Expiration du délai d’installation

L’installation de l’extension MMA n’a pas pu se terminer, car le délai d’attente a expiré.

#### <a name="issue"></a>Problème

Voici un exemple de message d’erreur pouvant s’afficher :

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Cause

Cette erreur se produit car la machine virtuelle était surchargée pendant l’installation.

### <a name="resolution"></a>Résolution

Essayez d’installer l’extension MMA lorsque la machine virtuelle sera moins surchargée.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
