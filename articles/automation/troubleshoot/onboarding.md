---
title: Résolution des erreurs d’intégration des solutions Update Management, Change Tracking et Inventory
description: Découvrez comment résoudre les erreurs d’intégration des solutions Update Management, Change Tracking et Inventory.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 52ff52ffb558278507bb24e1b1e2054c251b2512
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879636"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Résolution des erreurs d’intégration des solutions

Vous pouvez rencontrer des erreurs lors de l’intégration d’une solution telle que Update Management, Change Tracking ou Inventory. Cet article décrit les erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="general-errors"></a>Erreurs générales

### <a name="computer-group-query-format-error"></a>Scénario : ComputerGroupQueryFormatError

#### <a name="issue"></a>Problème

Ce code d’erreur signifie que la requête de recherche de groupe d’ordinateurs enregistrée utilisée pour cibler la solution n’a pas été formatée correctement. 

#### <a name="cause"></a>Cause :

Il est possible que vous ayez modifié la requête, ou elle peut avoir été modifiée par le système.

#### <a name="resolution"></a>Résolution :

Vous pouvez supprimer la requête pour cette solution et réintégrer la solution, ce qui recrée la requête. La requête se trouve dans votre espace de travail, sous **Recherches enregistrées**. Le nom de la requête est **MicrosoftDefaultComputerGroup**, et la catégorie de la requête est le nom de la solution associée à cette requête. Si plusieurs solutions sont activées, **MicrosoftDefaultComputerGroup** s’affiche plusieurs fois sous **Recherches enregistrées**.

### <a name="policy-violation"></a>Scénario : PolicyViolation

#### <a name="issue"></a>Problème

Ce code d’erreur signifie que le déploiement a échoué en raison d’une violation d’une ou de plusieurs stratégies.

#### <a name="cause"></a>Cause : 

Une stratégie actuellement en place empêche l’opération de se terminer.

#### <a name="resolution"></a>Résolution :

Pour déployer correctement la solution, vous devez envisager de modifier la stratégie indiquée. Étant donné qu’il existe plusieurs types de stratégies différents qui peuvent être définis, les modifications spécifiques requises dépendent de la stratégie violée. Par exemple, si une stratégie a été définie sur un groupe de ressources qui interdisait la modification du contenu de certains types de ressources au sein de ce groupe de ressources, vous pourriez, par exemple, effectuer les opérations suivantes :

* Retirer la stratégie.
* Essayer d’effectuer une intégration à un groupe de ressources différent.
* Réviser la stratégie, par exemple:
  * En reciblant la stratégie vers une ressource spécifique (par exemple, vers un compte Automation)
  * en révisant l’ensemble de ressources que cette stratégie a été configurée pour refuser.

Consultez les notifications dans le coin supérieur droit du portail Azure ou accédez au groupe de ressources comportant votre compte Automation, puis sélectionnez **Déploiements** sous **Paramètres** pour afficher le déploiement mis en échec. Pour en apprendre davantage sur Azure Policy, consultez : [Présentation d’Azure Policy](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Échecs d’extension MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Lorsque vous déployez une solution, de nombreuses ressources connexes sont déployées en même temps. L’une de ces ressources est l’extension Microsoft Monitoring Agent, ou l’Agent Log Analytics pour Linux. Il s’agit d’extensions de machine virtuelle installées par l’agent invité de la machine virtuelle, qui est chargé de communiquer avec l’espace de travail Log Analytics configuré, en vue d’une coordination ultérieure du téléchargement des fichiers binaires et des autres fichiers sur lesquels dépend la solution que vous intégrez, une fois son exécution lancée.
En général, vous découvrez les erreurs d’installation de MMA ou de l’Agent Log Analytics pour Linux par l’affichage d’une notification dans le hub Notifications. En cliquant sur cette notification, vous obtenez davantage d’informations sur l’échec. L’accès à la ressource Groupes de ressources, puis à l’élément Déploiements qu’elle contient, permet également d’obtenir des informations détaillées sur les échecs de déploiement.
L’installation de l’extension MMA ou de l’Agent Log Analytics pour Linux peut échouer pour diverses raisons, et les étapes à suivre pour résoudre ces échecs varient en fonction du problème. Les étapes de résolution spécifiques sont décrites ci-dessous.

La section suivante décrit différents problèmes que vous pouvez rencontrer lors de l’intégration et qui provoquent l’échec du déploiement de l’extension MMA.

### <a name="webclient-exception"></a>Scénario : Une exception s’est produite lors d’une requête WebClient

L’extension MMA de la machine virtuelle ne peut pas communiquer avec les ressources externes, et le déploiement échoue.

#### <a name="issue"></a>Problème

Voici quelques exemples des messages d’erreur qui peuvent s’afficher :

```error
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```error
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Cause :

Voici les causes possibles de cette erreur :

* Un proxy configuré dans la machine virtuelle autorise uniquement certains ports.

* Un paramètre du pare-feu a bloqué l’accès aux adresses et aux ports nécessaires.

#### <a name="resolution"></a>Résolution :

Vérifiez que les bons ports et adresses sont ouverts à la communication. Pour obtenir la liste des ports et adresses, consultez [Planification de votre réseau](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scénario : L’installation a échoué en raison de problèmes temporaires liés à l’environnement

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

#### <a name="cause"></a>Cause :

Voici les causes possibles de cette erreur :

* Une autre installation est en cours d’exécution.
* Le redémarrage du système a été déclenché pendant le déploiement du modèle.

#### <a name="resolution"></a>Résolution :

Cette erreur est de nature temporaire. Recommencez le déploiement pour installer l’extension.

### <a name="installation-timeout"></a>Scénario : Expiration du délai d’installation

L’installation de l’extension MMA n’a pas pu se terminer, car le délai d’attente a expiré.

#### <a name="issue"></a>Problème

Voici un exemple de message d’erreur pouvant s’afficher :

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Cause :

Cette erreur est due au fait que la machine virtuelle était surchargée pendant l’installation.

### <a name="resolution"></a>Résolution :

Essayez d’installer l’extension MMA lorsque la machine virtuelle sera moins surchargée.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
