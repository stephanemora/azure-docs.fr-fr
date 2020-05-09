---
title: Résolution des problèmes avec Change Tracking et l’inventaire
description: Découvrez comment détecter et résoudre les problèmes avec Azure Automation Change Tracking et la solution d’inventaire.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582148"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Résoudre les problèmes rencontrés avec Change Tracking et d’inventaire

Cet article explique comment résoudre les problèmes rencontrés avec Change Tracking et l’inventaire dans Azure Automation.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

## <a name="windows"></a> Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scénario : Les enregistrements Change Tracking et de l’inventaire n’apparaissent pas pour les ordinateurs Windows

#### <a name="issue"></a>Problème

Vous ne voyez pas les résultats de Change Tracking et de l’inventaire pour les ordinateurs Windows intégrés.

#### <a name="cause"></a>Cause

Cette erreur peut avoir les causes suivantes :

* L’agent Azure Log Analytics pour Windows n’est pas en cours d’exécution.
* La communication avec le compte Automation est bloquée.
* Les packs d’administration de Change Tracking et de l’inventaire n’ont pas été téléchargés.
* La machine virtuelle embarquée peut provenir d’un ordinateur cloné qui n’a pas été préparé avec Sysprep avec l’agent Log Analytics pour Windows installé.

#### <a name="resolution"></a>Résolution

Sur l’ordinateur agent Log Analytics, accédez à **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** et exécutez les commandes suivantes :

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Si vous avez encore besoin d’aide, vous pouvez recueillir des informations de diagnostic et contacter le support.

> [!NOTE]
> L’agent log Analytics active le suivi des erreurs par défaut. Pour activer les messages d’erreur détaillés comme dans l’exemple précédent, utilisez le paramètre `VER`. Pour les suivis d’informations, utilisez `INF` lorsque vous appelez `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>L’agent Log Analytics pour Windows n’est pas exécuté

Vérifiez que l’agent Log Analytics pour Windows (**HealthService.exe**) est en cours d’exécution sur l’ordinateur.

##### <a name="communication-to-automation-account-blocked"></a>Communication bloquée vers le compte Automation

Dans l’observateur d’événements sur la machine, recherchez les événements comportant le mot `changetracking`.

Pour en savoir plus sur les adresses et les ports qui doivent être autorisés pour le fonctionnement de Change Tracking et de l’inventaire, consultez [Automatiser les ressources de votre centre de données ou de votre cloud à l’aide d’un Runbook Worker hybride](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Packs d’administration non téléchargés

Vérifiez que les packs d’administration Change Tracking et Inventaire suivants sont installés localement :

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Machine virtuelle issue d’un ordinateur cloné qui n’a pas été préparé avec Sysprep

Si vous utilisez une image clonée, commencez par exécuter Sysprep sur l’image et installez ensuite l’agent Log Analytics pour Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scénario : Aucun résultat de Change Tracking ni de l’inventaire sur les ordinateurs Linux

#### <a name="issue"></a>Problème

Vous ne voyez pas les résultats de Change Tracking et de l’inventaire pour les ordinateurs Linux intégrés pour la solution. 

#### <a name="cause"></a>Cause
Voici les causes possibles de ce problème :
* L’agent Log Analytics pour Linux n’est pas en cours d’exécution.
* L’agent Log Analytics pour Linux n’est pas configuré correctement.
* Il existe des conflits de supervision de l’intégrité des fichiers (FIM).

#### <a name="resolution"></a>Résolution 

##### <a name="log-analytics-agent-for-linux-not-running"></a>L’agent Log Analytics pour Linux n’est pas en cours d’exécution

Vérifiez si le démon de l’agent Log Analytics pour Linux (**omsagent**) est en cours d’exécution sur votre machine. Exécutez la requête suivante dans l’espace de travail Log Analytics lié à votre compte Automation.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Si votre ordinateur ne figure pas dans les résultats de la requête, il n’a pas été enregistré récemment. Il y a probablement un problème de configuration locale et vous devez réinstaller l’agent. Pour plus d’informations sur l’installation et la configuration, consultez [Collecter des données de journal avec l’agent Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Si votre ordinateur s’affiche dans les résultats de la requête, vérifiez la configuration de l’étendue. Consultez [Ciblage des solutions de supervision dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Pour mieux résoudre ce problème, consultez [Problème : vous ne pouvez pas voir de données Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>L’agent Log Analytics pour Linux n’est pas configuré correctement

L’agent Log Analytics pour Linux n’est peut-être pas configuré correctement pour la collecte des journaux et des sorties en ligne de commande à l’aide de l’outil Log Collector d’OMS. Consultez [Suivre les modifications apportées à votre environnement grâce à la solution Change Tracking et inventaire](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflits FIM

La fonctionnalité FIM d’Azure Security Center peut ne pas valider correctement l’intégrité de vos fichiers Linux. Vérifiez que FIM est opérationnel et correctement configuré pour l’analyse des fichiers Linux. Consultez [Suivre les modifications apportées à votre environnement grâce à la solution Change Tracking et inventaire](../change-tracking.md).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas ici ou que vous ne pouvez pas le résoudre, utilisez un des canaux suivants pour obtenir une aide supplémentaire :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Le Support Azure fournit à la communauté Azure des réponses, un support technique et des experts.
* Signaler un incident au support Azure Accédez au [site du Support Azure](https://azure.microsoft.com/support/options/), puis sélectionnez **Obtenir de l’aide**.
