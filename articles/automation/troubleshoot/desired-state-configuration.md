---
title: Résoudre les problèmes avec la Configuration de l’état souhaité Azure Automation (DSC)
description: Cet article fournit des informations sur la résolution des problèmes de la Configuration de l’état souhaité
services: automation
ms.service: automation
ms.subservice: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 997f332e14fd1accf32d8cc3f51557fe005acab5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421642"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Dépanner la Configuration de l’état souhaité

Cet article fournit des informations sur la résolution des problèmes de la Configuration de l’état souhaité.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Erreurs courantes avec la Configuration d’état souhaité (DSC)

### <a name="failed-not-found"></a>Scénario : Le nœud est en état d’échec avec une erreur « Introuvable »

#### <a name="issue"></a>Problème

Un rapport pour le nœud indique un état **Échec** et contient l’erreur :

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Cause :

Cette erreur se produit généralement quand le nœud est affecté à un nom de configuration (par exemple ABC) au lieu d’un nom de configuration de nœud (par exemple ABC.WebServer).

#### <a name="resolution"></a>Résolution :

* Assurez-vous d'affecter le nœud avec « nom de configuration de nœud » et non pas le « nom de configuration ».
* Vous pouvez affecter une configuration de nœud à un nœud à l'aide du portail Azure ou d’une applet de commande PowerShell.

  * Pour affecter une configuration de nœud à un nœud à l’aide du portail Azure, ouvrez la page **Nœuds DSC**, sélectionnez un nœud, puis cliquez sur le bouton **Attribuer une configuration de nœud**.  
  * Pour affecter une configuration de nœud à un nœud à l’aide d’une applet de commande PowerShell, utilisez l’applet de commande **Set-AzureRmAutomationDscNode** .

### <a name="no-mof-files"></a>Scénario : Aucune configuration de nœud (fichiers MOF) n’a été produite au cours d’une compilation de configuration

#### <a name="issue"></a>Problème

Votre tâche de compilation DSC s’interrompt avec l’erreur :

```
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

```
No instance found with given property values
```

#### <a name="cause"></a>Cause :

Vous avez mis à niveau votre version de WMF et endommagé WMI.

#### <a name="resolution"></a>Résolution :

Pour résoudre ce problème, suivez les instructions fournies dans l’article [Problèmes connus liés à la Configuration d’état souhaité (DSC)](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc).

### <a name="issue-using-credential"></a>Scénario : Il est impossible d’utiliser des informations d’identification dans une configuration DSC

#### <a name="issue"></a>Problème

Votre tâche de compilation DSC a été interrompue avec l’erreur :

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Cause :

Vous avez utilisé des informations d’identification dans une configuration, mais n’avez pas fourni la bonne valeur de **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud.

#### <a name="resolution"></a>Résolution :

* Assurez-vous de transmettre la bonne valeur **ConfigurationData** pour définir **PSDscAllowPlainTextPassword** sur true pour chaque configuration de nœud mentionnée dans la configuration. Pour plus d’informations, consultez les [ressources d’Azure Automation DSC](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
