---
title: Résolution des problèmes avec le suivi des modifications Azure
description: Découvrez comment détecter et résoudre les problèmes liés à Azure Automation Change Tracking et à la fonctionnalité d’inventaire.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198528"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Résolution des problèmes avec la fonction Suivi des modifications et inventaire

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scénario : Les enregistrements Change Tracking n’apparaissent pas pour les ordinateurs Windows

#### <a name="issue"></a>Problème

Vous ne voyez pas les résultats Change Tracking et Inventaire pour les ordinateurs Windows qui intègrent cette fonction.

#### <a name="cause"></a>Cause :

Cette erreur peut avoir les causes suivantes :

* Microsoft Monitoring Agent n’est pas en cours d’exécution.
* La communication avec le compte Automation est bloquée.
* Les packs d’administration pour Change Tracking n’ont pas été téléchargés.
* La machine virtuelle embarquée peut provenir d’un ordinateur cloné qui n’a pas été préparé avec Sysprep avec Microsoft Monitoring Agent installé.

#### <a name="resolution"></a>Résolution

Les solutions décrites ci-dessous peuvent vous aider à résoudre votre problème. Si vous avez encore besoin d’aide, vous pouvez recueillir des informations de diagnostic et contacter le support. Sur l’ordinateur agent, accédez à C:\Program Files\Microsoft Monitoring Agent\Agent\Tools et exécutez les commandes suivantes :

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Par défaut, le suivi des erreurs est activé. Pour activer les messages d’erreur détaillés comme dans l’exemple précédent, utilisez le paramètre *VER*. Pour les suivis d’informations, utilisez *INF* lors de l’appel de **StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent n’est pas en cours d’exécution

Vérifiez si Microsoft Monitoring Agent (HealthService.exe) est en cours d’exécution sur l’ordinateur.

##### <a name="communication-to-automation-account-blocked"></a>Communication bloquée vers le compte Automation

Dans l’observateur d’événements sur la machine, recherchez les événements comportant le mot « changetracking ».

Pour en savoir plus sur les adresses et les ports qui doivent être autorisés pour le fonctionnement de Change Tracking, consultez [Automatiser les ressources de votre centre de données ou de votre cloud à l’aide d’un Runbook Worker hybride](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Packs d’administration non téléchargés

Vérifiez que les packs d’administration Change Tracking et Inventaire suivants sont installés localement :

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Machine virtuelle issue d’un ordinateur cloné qui n’a pas été préparé avec Sysprep

Si vous utilisez une image clonée, commencez par exécuter Sysprep sur l’image et installez ensuite Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scénario : Aucun résultat Change Tracking et Inventaire sur les ordinateurs Linux

#### <a name="issue"></a>Problème

Vous ne voyez pas les résultats Change Tracking et Inventaire pour les ordinateurs Linux qui intègrent cette fonction. 

#### <a name="cause"></a>Cause :
Voici les causes possibles de ce problème :
* L’agent Log Analytics pour Linux n’est pas en cours d’exécution.
* L’agent Log Analytics pour Linux n’est pas configuré correctement.
* Il existe des conflits de supervision de l’intégrité des fichiers (FIM).

#### <a name="resolution"></a>Résolution 

##### <a name="log-analytics-agent-for-linux-not-running"></a>L’agent Log Analytics pour Linux n’est pas en cours d’exécution

Vérifiez si le démon de l’agent Log Analytics pour Linux (omsagent) est en cours d’exécution sur votre machine. Exécutez la requête suivante dans l’espace de travail Log Analytics lié à votre compte Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Si votre ordinateur ne figure pas dans les résultats de la requête, il n’a pas été enregistré récemment. Il y a probablement un problème de configuration locale et vous devez réinstaller l’agent. Pour plus d’informations sur l’installation et la configuration, consultez [Collecter des données de journal avec l’agent Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Si votre ordinateur s’affiche dans les résultats de la requête, vérifiez la configuration de l’étendue. Consultez [Ciblage des solutions de supervision dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Pour plus d’informations sur ce problème, reportez-vous à [Problème : vous ne pouvez pas voir de données Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>L’agent Log Analytics pour Linux n’est pas configuré correctement

L’agent Log Analytics pour Linux n’est peut-être pas configuré correctement pour la collecte des journaux et des sorties en ligne de commande à l’aide de l’outil Log Collector d’OMS. Consultez [Suivre les modifications apportées à votre environnement grâce à la solution Change Tracking](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflits FIM

La fonctionnalité FIM d’Azure Security Center peut ne pas valider correctement l’intégrité de vos fichiers Linux. Vérifiez que FIM est opérationnel et correctement configuré pour l’analyse des fichiers Linux. Consultez [Suivre les modifications apportées à votre environnement grâce à la solution Change Tracking](../change-tracking.md).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide.

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
