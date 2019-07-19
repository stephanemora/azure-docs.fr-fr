---
title: Résoudre les problèmes avec la Configuration de l’état souhaité Azure Automation (DSC)
description: Cet article fournit des informations sur la résolution des problèmes de la Configuration de l’état souhaité
services: automation
ms.service: automation
ms.subservice: ''
author: bobbytreed
ms.author: robreed
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53fef426c927c690a3b697055f467f6cd35c532c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477524"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Dépanner la Configuration de l’état souhaité

Cet article fournit des informations sur la résolution des problèmes de la Configuration de l’état souhaité.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erreurs courantes avec la Configuration d’état souhaité (DSC)

### <a name="unsupported-characters"></a>Scénario : Une configuration contenant des caractères spéciaux ne peut pas être supprimée à partir du portail

#### <a name="issue"></a>Problème

Lorsque vous tentez de supprimer une configuration DSC à partir du portail, vous rencontrez l’erreur suivante :

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Cause :

Cette erreur est due à un problème temporaire dont la résolution est prévue.

#### <a name="resolution"></a>Résolution :

* Utilisez l’applet de commande Az « Remove-AzAutomationDscConfiguration » pour supprimer la configuration.
* La documentation relative à cette applet de commande n’a pas encore été mise à jour.  En attendant, reportez-vous à la documentation du module AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scénario : Impossible d’inscrire l’agent DSC

#### <a name="issue"></a>Problème

Lorsque vous tentez d’exécuter `Set-DscLocalConfigurationManager` ou une autre applet de commande DSC, vous recevez l’erreur :

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

#### <a name="cause"></a>Cause :

Cette erreur est habituellement due à un pare-feu, la machine se trouvant derrière un serveur proxy, ou à d’autres erreurs de réseau.

#### <a name="resolution"></a>Résolution :

Vérifiez que votre machine a accès aux points de terminaison appropriés pour Azure Automation DSC et réessayez. Pour obtenir la liste des ports et adresses nécessaires, consultez [Planification réseau](../automation-dsc-overview.md#network-planning).

### <a name="failed-not-found"></a>Scénario : Le nœud est en état d’échec avec une erreur « Introuvable »

#### <a name="issue"></a>Problème

Un rapport pour le nœud indique un état **Échec** et contient l’erreur :

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Cause :

Cette erreur se produit généralement quand le nœud est affecté à un nom de configuration (par exemple ABC) au lieu d’un nom de configuration de nœud (par exemple ABC.WebServer).

#### <a name="resolution"></a>Résolution :

* Assurez-vous d’affecter le nœud avec « nom de configuration de nœud » et non pas le « nom de configuration ».
* Vous pouvez affecter une configuration de nœud à un nœud à l'aide du portail Azure ou d’une applet de commande PowerShell.

  * Pour affecter une configuration de nœud à un nœud à l’aide du Portail Azure, ouvrez la page **Nœuds DSC**, sélectionnez un nœud, puis cliquez sur le bouton **Attribuer une configuration de nœud**.  
  * Pour affecter une configuration de nœud à un nœud à l’aide d’une applet de commande PowerShell, utilisez l’applet de commande **Set-AzureRmAutomationDscNode**.

### <a name="no-mof-files"></a>Scénario : Aucune configuration de nœud (fichiers MOF) n’a été produite au cours d’une compilation de configuration

#### <a name="issue"></a>Problème

Votre tâche de compilation DSC s’interrompt avec l’erreur :

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Cause :

Quand l’expression qui suit le mot-clé **Node** dans la configuration DSC s’évalue à `$null`, aucune configuration de nœud n’est générée.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Vérifiez que l’expression en regard du mot clé **Node** dans la définition de la configuration n’est pas $null.
* Si vous effectuez une transmission de ConfigurationData pendant la compilation de la configuration, vérifiez que vous transmettez les valeurs attendues nécessaires à la configuration depuis [ConfigurationData](../automation-dsc-compile.md#configurationdata).

### <a name="dsc-in-progress"></a>Scénario : Le rapport du nœud DSC se bloque à l’état « en cours »

#### <a name="issue"></a>Problème

La sortie de l’agent DSC est la suivante :

```error
No instance found with given property values
```

#### <a name="cause"></a>Cause :

Vous avez mis à niveau votre version de WMF et endommagé WMI.

#### <a name="resolution"></a>Résolution :

Pour résoudre ce problème, suivez les instructions fournies dans l’article [Problèmes connus liés à la Configuration d’état souhaité (DSC)](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc).

### <a name="issue-using-credential"></a>Scénario : Il est impossible d’utiliser des informations d’identification dans une configuration DSC

#### <a name="issue"></a>Problème

Votre tâche de compilation DSC a été interrompue avec l’erreur :

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Cause :

Vous avez utilisé des informations d’identification dans une configuration, mais n’avez pas fourni la bonne valeur de **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud.

#### <a name="resolution"></a>Résolution :

* Assurez-vous de transmettre la bonne valeur **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud mentionnée dans la configuration. Pour plus d’informations, consultez les [ressources d’Azure Automation DSC](../automation-dsc-compile.md#assets).

### <a name="failure-processing-extension"></a>Scénario : Intégration à partir de l’extension dsc, erreur « Échec lors du traitement de l’extension »

#### <a name="issue"></a>Problème

Lors de l’intégration à l’aide de l’extension DSC, un échec se produit avec l’erreur :

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Cause :

Cette erreur se produit généralement quand le nœud est affecté à un nom de configuration de nœud qui n’existe pas dans le service.

#### <a name="resolution"></a>Résolution :

* Assurez-vous que vous affectez le nœud avec un nom de configuration de nœud qui correspond exactement au nom dans le service.
* Vous pouvez choisir de ne pas inclure le nom de configuration de nœud, ce qui entraîne l’intégration du nœud,sans affectation de configuration de nœud

### <a name="failure-linux-temp-noexec"></a>Scénario : Pendant l’application d’une configuration dans Linux, un échec se produit avec une erreur générale

#### <a name="issue"></a>Problème

Pendant l’application d’une configuration dans Linux, un échec se produit avec l’erreur :

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Cause :

Les clients ont déterminé que si l’emplacement /tmp est défini sur noexec, la version actuelle de DSC ne parvient pas à appliquer les configurations.

#### <a name="resolution"></a>Résolution :

* Supprimez l’option noexec de l’emplacement /tmp.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
