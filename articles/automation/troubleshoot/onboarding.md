---
title: Résolution des erreurs d’intégration des solutions Update Management, Change Tracking et Inventory
description: Découvrez comment résoudre les erreurs d’intégration des solutions Update Management, Change Tracking et Inventory.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: eaafee304f606ae4d511a6cea1824c26db838635
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119127"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Résolution des erreurs d’intégration des solutions

Vous pouvez rencontrer des erreurs lors de l’intégration d’une solution telle que Update Management, Change Tracking ou Inventory. Cet article décrit les erreurs qui peuvent se produire et explique comment les résoudre.

## <a name="general-errors"></a>Erreurs générales

### <a name="missing-write-permissions"></a>Scénario : L'intégration échoue avec le message : La solution ne peut pas être activée

#### <a name="issue"></a>Problème

Vous recevez un des messages suivants lorsque vous essayez d’intégrer une machine virtuelle à une solution :

```error
The solution cannot be enabled due to missing permissions for the virtual machine or deployments
```

```error
The solution cannot be enabled on this VM because the permission to read the workspace is missing
```

#### <a name="cause"></a>Cause :

Cette erreur est due à des autorisations incorrectes ou manquantes sur la machine virtuelle, l’espace de travail, ou pour l’utilisateur.

#### <a name="resolution"></a>Résolution :

Vérifiez que vous disposez des autorisations appropriées pour intégrer la machine virtuelle. Passez en revue les [autorisations nécessaires pour intégrer des machines](../automation-role-based-access-control.md#onboarding), puis essayez à nouveau d'intégrer la solution. Si vous recevez l’erreur `The solution cannot be enabled on this VM because the permission to read the workspace is missing`, assurez-vous d’avoir le `Microsoft.OperationalInsights/workspaces/read` autorisé à être en mesure de trouver si la machine virtuelle est intégrée à un espace de travail.

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

Consultez les notifications dans l’angle supérieur droit du portail Azure ou accédez au groupe de ressources qui contient votre compte automation, puis sélectionnez **déploiements** sous **paramètres** pour afficher l’ayant échoué déploiement. Pour en apprendre davantage sur Azure Policy, consultez : [Présentation d’Azure Policy](../../governance/policy/overview.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Échecs d’extension MMA

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 

Lorsque vous déployez une solution, de nombreuses ressources connexes sont déployées en même temps. L’une de ces ressources est l’extension Microsoft Monitoring Agent, ou l’Agent Log Analytics pour Linux. Il s’agit d’Extensions de Machine virtuelle installé par l’Agent invité de la machine virtuelle qui est chargé de communiquer avec l’espace de travail Analytique de journal configuré, à des fins de coordination ultérieure du téléchargement de fichiers binaires et autres fichiers dont le solution que vous êtes dépendent de l’intégration une fois qu’il commence l’exécution.
En général, vous découvrez les erreurs d’installation de MMA ou de l’Agent Log Analytics pour Linux par l’affichage d’une notification dans le hub Notifications. En cliquant sur cette notification, vous obtenez davantage d’informations sur l’échec. L’accès à la ressource Groupes de ressources, puis à l’élément Déploiements qu’elle contient, permet également d’obtenir des informations détaillées sur les échecs de déploiement.
L’installation de l’extension MMA ou de l’Agent Log Analytics pour Linux peut échouer pour diverses raisons, et les étapes à suivre pour résoudre ces échecs varient en fonction du problème. Les étapes de résolution spécifiques sont décrites ci-dessous.

La section suivante décrit différents problèmes que vous pouvez rencontrer lors de l’intégration qui provoquent une défaillance dans le déploiement de l’extension MMA.

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

* Un proxy est configuré dans la machine virtuelle, ce qui autorise uniquement des ports spécifiques.

* Un paramètre du pare-feu a bloqué l’accès aux adresses et aux ports nécessaires.

#### <a name="resolution"></a>Résolution :

Vérifiez que les bons ports et adresses sont ouverts à la communication. Pour obtenir la liste des ports et adresses, consultez [Planification de votre réseau](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Scénario : Échec de l’installation en raison d’un problème d’environnement temporaires

L’installation de l’extension Microsoft Monitoring Agent a échoué au cours du déploiement en raison d’une autre installation ou action bloquent l’installation

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
* Le système est déclenché pour redémarrer au cours du déploiement de modèle

#### <a name="resolution"></a>Résolution :

Cette erreur est de nature temporaire. Recommencez le déploiement pour installer l’extension.

### <a name="installation-timeout"></a>Scénario : Expiration du délai d’installation

L’installation de l’extension MMA n’a pas terminé en raison d’un délai d’expiration.

#### <a name="issue"></a>Problème

L’exemple suivant est d’un message d’erreur qui peut-être être retourné :

```error
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Cause :

Cette erreur se produit parce que la machine virtuelle en cours sous une charge importante lors de l’installation.

### <a name="resolution"></a>Résolution :

Essayez d’installer l’extension MMA lorsque la machine virtuelle sera moins surchargée.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
