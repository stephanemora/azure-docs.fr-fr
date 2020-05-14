---
title: Résolution d’un problème d’intégration des solutions de gestion Azure Automation
description: Découvrez comment résoudre des erreurs d’intégration de solution Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/22/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 371094ecba5168fd32a7af9fb81a71eb722efc91
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836527"
---
# <a name="troubleshoot-solution-onboarding"></a>Résolution des problèmes d’intégration de solution

Vous pouvez recevoir des messages d’erreur lors de l’intégration de la solution Azure Automation Update Management ou de la solution Change Tracking and Inventory. Cet article décrit les erreurs qui pourraient se produire et explique comment les résoudre.

## <a name="known-issues"></a>Problèmes connus

### <a name="scenario-renaming-a-registered-node-requires-unregister-or-register-again"></a><a name="node-rename"></a>Scénario : La modification du nom d’un nœud inscrit requiert l’annulation de l’inscription ou une réinscription

#### <a name="issue"></a>Problème

Un nœud est inscrit sur Azure Automation, puis le nom d’ordinateur du système d’exploitation est modifié. Les rapports du nœud continuent à s’afficher avec le nom d’origine.

#### <a name="cause"></a>Cause

La modification du nom d’un nœud inscrit n’a pas pour effet de modifier son nom dans Azure Automation.

#### <a name="resolution"></a>Résolution

Annulez l’inscription du nœud dans le service State Configuration d’Azure Automation, puis réinscrivez-le. Les rapports publiés sur le service avant ce délai ne seront plus disponibles.

### <a name="scenario-re-signing-certificates-via-https-proxy-isnt-supported"></a><a name="resigning-cert"></a>Scénario : La nouvelle signature de certificats via un proxy HTTPS n’est pas prise en charge

#### <a name="issue"></a>Problème

Quand vous vous connectez via une solution proxy qui met fin au trafic HTTPS, puis chiffre à nouveau le trafic à l’aide d’un nouveau certificat, le service n’autorise pas la connexion.

#### <a name="cause"></a>Cause

Azure Automation ne prend pas en charge la nouvelle signature de certificats utilisés pour chiffrer le trafic.

#### <a name="resolution"></a>Résolution

Il n’existe actuellement aucun moyen de contourner ce problème.

## <a name="general-errors"></a>Erreurs générales.

### <a name="scenario-onboarding-fails-with-the-message-the-solution-cannot-be-enabled"></a><a name="missing-write-permissions"></a>Scénario : L’intégration échoue avec le message : « La solution ne peut pas être activée »

#### <a name="issue"></a>Problème

L’un des messages suivants s’affiche lorsque vous essayez d’intégrer une machine virtuelle à une solution :

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Cause

Cette erreur est due à des autorisations incorrectes ou manquantes sur la machine virtuelle ou dans l’espace de travail, ou bien pour l'utilisateur.

#### <a name="resolution"></a>Résolution

Vérifiez que vous avez les [autorisations nécessaires requises pour intégrer des machines](../automation-role-based-access-control.md#onboarding-permissions), puis réessayez d’intégrer la solution. Si le message d’erreur `The solution cannot be enabled on this VM because the permission to read the workspace is missing` s’affiche, assurez-vous que vous disposez de l’autorisation `Microsoft.OperationalInsights/workspaces/read` pour pouvoir déterminer si la machine virtuelle est intégrée à un espace de travail.

### <a name="scenario-onboarding-fails-with-the-message-failed-to-configure-automation-account-for-diagnostic-logging"></a><a name="diagnostic-logging"></a>Scénario : L’intégration échoue avec le message : « Échec de la configuration du compte Automation pour la journalisation des diagnostics »

#### <a name="issue"></a>Problème

Le message suivant s'affiche lorsque vous essayez d'intégrer une machine virtuelle à une solution :

```error
Failed to configure automation account for diagnostic logging
```

#### <a name="cause"></a>Cause

Cette erreur peut survenir si le niveau tarifaire ne correspond pas au modèle de facturation de l’abonnement. Pour plus d’informations, consultez [Surveiller l’utilisation et l’estimation des coûts dans Azure Monitor](https://aka.ms/PricingTierWarning).

#### <a name="resolution"></a>Résolution

Créez votre espace de travail Log Analytics manuellement, puis répétez la procédure d’intégration pour sélectionner l’espace de travail créé.

### <a name="scenario-computergroupqueryformaterror"></a><a name="computer-group-query-format-error"></a>Scénario : ComputerGroupQueryFormatError

#### <a name="issue"></a>Problème

Ce code d’erreur signifie que la requête de recherche de groupe d’ordinateurs enregistrée utilisée pour cibler la solution n’est pas formatée correctement. 

#### <a name="cause"></a>Cause

Vous avez peut-être modifié la requête, ou le système l’a peut-être modifiée.

#### <a name="resolution"></a>Résolution

Vous pouvez supprimer la requête pour cette solution, puis réintégrer celle-ci, ce qui a pour effet de recréer la requête. La requête se trouve dans votre espace de travail, sous **Recherches enregistrées**. Le nom de la requête est **MicrosoftDefaultComputerGroup**, et la catégorie de la requête est le nom de la solution associée. Si plusieurs solutions sont activées, la requête **MicrosoftDefaultComputerGroup** s’affiche plusieurs fois sous **Recherches enregistrées**.

### <a name="scenario-policyviolation"></a><a name="policy-violation"></a>Scénario : PolicyViolation

#### <a name="issue"></a>Problème

Ce code d’erreur indique que le déploiement a échoué en raison d’une violation d’une ou de plusieurs stratégies.

#### <a name="cause"></a>Cause 

Une stratégie empêche l’opération de se terminer.

#### <a name="resolution"></a>Résolution

Pour déployer correctement la solution, vous devez envisager de modifier la stratégie indiquée. Étant donné qu’il existe plusieurs types de stratégies peuvent être définis, les modifications requises dépendent de la stratégie enfreinte. Par exemple, si une stratégie est définie sur un groupe de ressources qui refuse l’autorisation de modifier le contenu de certaines ressources contenues, vous pouvez choisir l’un de ces correctifs :

* Retirer la stratégie.
* Essayer d’intégrer la solution dans un groupe de ressources différent.
* Ciblez de nouveau la stratégie sur une ressource spécifique, par exemple un compte Automation.
* Révisez l’ensemble de ressources pour le refus desquelles cette stratégie a été configurée.

Consultez les notifications dans l’angle supérieur droit du portail Azure, ou accédez au groupe de ressources comportant votre compte Automation, puis sélectionnez **Déploiements** sous **Paramètres** pour afficher le déploiement en échec. Pour en savoir plus sur Azure Policy, consultez [Présentation d’Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

### <a name="scenario-errors-trying-to-unlink-a-workspace"></a><a name="unlink"></a>Scénario : Erreurs lors de la tentative de dissociation d’un espace de travail

#### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant lorsque vous tentez de dissocier un espace de travail :

```error
The link cannot be updated or deleted because it is linked to Update Management and/or ChangeTracking Solutions.
```

#### <a name="cause"></a>Cause

Cette erreur se produit lorsque des solutions de votre espace de travail Log Analytics qui dépendent de votre compte Automation et de votre espace de travail Log Analytics associé sont toujours actives.

### <a name="resolution"></a>Résolution

Supprimez les solutions suivantes de votre espace de travail si vous les utilisez :

* Update Management
* Suivi des modifications et inventaire
* Démarrer/arrêter des machines virtuelles pendant les heures creuses

Une fois les solutions supprimées, vous pouvez dissocier votre espace de travail. Il est important de supprimer tous les artefacts existants de ces solutions dans votre espace de travail et votre compte Automation.

* Pour Update Management, supprimez les **déploiements de mises à jour (planifications)** de votre compte Automation.
* Pour Start/Stop VMs during off-hours, supprimez tous les verrous sur les composants de solution dans votre compte Automation sous **Paramètres** > **Verrous**. Pour plus d’informations, consultez [Supprimer la solution Start/Stop VMs during off-hours](../automation-solution-vm-management.md#remove-the-solution).

## <a name="log-analytics-for-windows-extension-failures"></a><a name="mma-extension-failures"></a>Log Analytics pour les échecs de l’extension Windows

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Une installation de l’agent Log Analytics pour l’extension Windows peut échouer pour diverses raisons. La section suivante décrit les problèmes d’intégration qui peuvent provoquer des échecs lors du déploiement de l’agent Log Analytics pour l’extension Windows.

>[!NOTE]
>L’agent Log Analytics pour Windows est le nom utilisé actuellement dans Azure Automation pour l’agent Microsoft Monitoring Agent (MMA).

### <a name="scenario-an-exception-occurred-during-a-webclient-request"></a><a name="webclient-exception"></a>Scénario : Une exception s’est produite lors d’une requête WebClient

Log Analytics pour l’extension Windows de la machine virtuelle ne peut pas communiquer avec les ressources externes, et le déploiement échoue.

#### <a name="issue"></a>Problème

Voici quelques exemples des messages d’erreur qui peuvent s’afficher :

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Cause

Voici des causes possibles de cette erreur :

* Un proxy configuré dans la machine virtuelle autorise uniquement certains ports.
* Un paramètre du pare-feu a bloqué l’accès aux adresses et aux ports nécessaires.

#### <a name="resolution"></a>Résolution

Vérifiez que les bons ports et adresses sont ouverts à la communication. Pour obtenir la liste des ports et adresses, consultez [Planification de votre réseau](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="scenario-install-failed-because-of-transient-environment-issues"></a><a name="transient-environment-issue"></a>Scénario : L’installation a échoué en raison de problèmes temporaires liés à l’environnement

L’installation de l’extension Log Analytics pour Windows a échoué pendant le déploiement, car une autre installation ou action bloque l’installation.

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

Voici des causes possibles de cette erreur :

* Une autre installation est en cours d’exécution.
* Le redémarrage du système est déclenché pendant le déploiement du modèle.

#### <a name="resolution"></a>Résolution

Cette erreur est temporaire par nature. Recommencez le déploiement pour installer l’extension.

### <a name="scenario-installation-timeout"></a><a name="installation-timeout"></a>Scénario : Expiration du délai d’installation

L’installation de l’extension Log Analytics pour Windows n’a pas pu se terminer, car le délai d’attente a expiré.

#### <a name="issue"></a>Problème

Voici un exemple de message d’erreur qui pourrait s’afficher :

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Cause

Ce type d’erreur se produit car la machine virtuelle est surchargée pendant l’installation.

### <a name="resolution"></a>Résolution

Essayez d’installer l’agent Log Analytics pour l’extension Windows quand la machine virtuelle est moins sollicitée.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas ici ou que vous ne pouvez pas le résoudre, utilisez un des canaux suivants pour obtenir une aide supplémentaire :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Le Support Azure fournit à la communauté Azure des réponses, un support technique et des experts.
* Signaler un incident au support Azure Accédez au [site du support Azure](https://azure.microsoft.com/support/options/), puis sélectionnez **Obtenir de l’aide**.
