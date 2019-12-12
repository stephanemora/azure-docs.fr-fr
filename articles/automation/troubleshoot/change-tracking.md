---
title: Résolution des problèmes avec le suivi des modifications Azure
description: Cet article fournit des informations sur la résolution des problèmes rencontrés avec le suivi des modifications
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 482b263b7c8427276d4fdb8681372c2be1ae69ee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849392"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Résolution des problèmes avec la fonction Suivi des modifications et inventaire

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scénario : Les enregistrements Change Tracking n’apparaissent pas pour les ordinateurs Windows

#### <a name="issue"></a>Problème

Vous ne voyez pas les résultats de la fonction Change Tracking et inventaire pour les ordinateurs Windows qui intègrent cette fonction.

#### <a name="cause"></a>Cause :

Cette erreur peut être due aux raisons suivantes :

1. **Microsoft Monitoring Agent** n’est pas en cours d’exécution
2. La communication avec le compte Automation est bloquée.
3. Les packs d’administration pour le suivi des modifications n’ont pas été téléchargés.
4. La machine virtuelle embarquée peut provenir d’une machine clonée qui n’a pas été synchronisée avec la version de Microsoft Monitoring Agent installée.

#### <a name="resolution"></a>Résolution :

1. Vérifiez si **Microsoft Monitoring Agent** (HealthService.exe) est en cours d’exécution sur l’ordinateur.
1. Dans l’**observateur d'événements** sur la machine, recherchez les événements comportant le mot `changetracking`.
1. Pour savoir quelles adresses et quels ports doivent être autorisés pour le fonctionnement du suivi des modifications, consultez [Planification du réseau](../automation-hybrid-runbook-worker.md#network-planning).
1. Vérifiez que les packs d'administration Suivi des modifications et inventaire suivants existent localement :
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Si vous utilisez une image clonée, commencez par exécuter sysprep sur l’image et installez ensuite l’agent Microsoft Monitoring Agent.

Si ces solutions ne résolvent pas votre problème et que vous contactez le support technique, vous pouvez exécuter les commandes suivantes pour collecter le diagnostic sur l’agent

Sur l’ordinateur agent, accédez à `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` et exécutez les commandes suivantes :

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Par défaut, le suivi des erreurs est activé. Si vous souhaitez afficher des messages d’erreur détaillés comme dans l’exemple précédent, utilisez le paramètre `VER`. Pour un suivi des informations, utilisez `INF` lorsque vous appelez `StartTracing.cmd`.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
