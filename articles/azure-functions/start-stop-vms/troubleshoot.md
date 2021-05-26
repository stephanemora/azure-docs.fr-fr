---
title: Résoudre les problèmes de démarrage/d’arrêt des machines virtuelles (préversion)
description: Cet article explique comment résoudre les problèmes rencontrés avec la fonctionnalité de démarrage/d’arrêt des machines virtuelles Azure (préversion).
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 83633b6f107aee995c19e1cda94f4f93a96e84e2
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076777"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Résoudre les problèmes courants de démarrage/d’arrêt des machines virtuelles (préversion)

Cet article fournit des informations sur la résolution des problèmes pouvant survenir durant la tentative d’installation et de configuration de la fonctionnalité de démarrage/d’arrêt des machines virtuelles (préversion). Pour obtenir des informations générales, consultez la [Vue d’ensemble du démarrage/de l’arrêt des machines virtuelles](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Validation et résolution des problèmes d’ordre général

Cette section explique comment résoudre les problèmes généraux liés aux scénarios de planification et vous aide à identifier la cause racine.

### <a name="azure-dashboard"></a>Tableau de bord Azure

Vous pouvez commencer par passer en revue le tableau de bord partagé Azure. Le tableau de bord partagé Azure déployé dans le cadre de la fonctionnalité de démarrage/d’arrêt des machines virtuelles v2 (préversion) est un moyen rapide et facile de vérifier l’état de chaque opération effectuée sur vos machines virtuelles. Consultez la vignette des **actions tentées sur les machines virtuelles** pour voir toutes les opérations récentes exécutées sur vos machines virtuelles. Il existe une certaine latence. Environ cinq minutes sont nécessaires à l’affichage des données dans le rapport, car elles sont extraites de la ressource Application Insights.

### <a name="logic-apps"></a>Logic Apps

Selon ce que vous avez activé dans Logic Apps pour prendre en charge le scénario de démarrage/d’arrêt, vous pouvez consulter l’historique des exécutions afin d’identifier la raison pour laquelle le scénario de démarrage/d’arrêt planifié ne s’est pas effectué correctement sur une ou plusieurs machines virtuelles cibles. Pour savoir comment examiner cela en détail, consultez [Historique des exécutions de Logic Apps](../../logic-apps/monitor-logic-apps.md#review-runs-history).

### <a name="azure-storage"></a>Stockage Azure

Vous pouvez consulter les détails des opérations effectuées sur les machines virtuelles. Ces opérations sont écrites dans la table **requestsstoretable** au sein du compte Stockage Azure utilisé pour la fonctionnalité de démarrage/d’arrêt des machines virtuelles v2 (préversion). Effectuez les étapes suivantes pour voir ces enregistrements.

1. Accédez au compte de stockage dans le portail Azure. Au sein du compte, dans le volet gauche, sélectionnez **Explorateur Stockage (préversion).
1. Sélectionnez **TABLES**, puis **requeststoretable**.
1. Chaque enregistrement de la table représente l’action de démarrage/d’arrêt effectuée sur une machine virtuelle Azure en fonction de l’étendue cible définie dans le scénario d’application logique. Vous pouvez filtrer les résultats en fonction de l’une des propriétés d’enregistrement (par exemple TIMESTAMP, ACTION ou TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Azure Functions

Vous pouvez passer en revue les derniers détails d’appel des fonctions Azure responsables du démarrage et de l’arrêt de la machine virtuelle. Commençons par examiner le flux d’exécution.

Le flux d’exécution des scénarios **Scheduled** et **Sequenced** est contrôlé par la même fonction. Le schéma de charge utile détermine le scénario effectué. Pour le scénario **Scheduled**, le flux d’exécution est HTTP **Scheduled** > File d’attente **VirtualMachineRequestOrchestrator** > **File d’attente VirtualMachineRequestExecutor**.

À partir de l’application logique, la fonction HTTP **Scheduled** est appelée avec le schéma de charge utile. Une fois que la fonction HTTP **Scheduled** reçoit la requête, elle envoie les informations à la fonction de file d’attente **Orchestrator**, qui à son tour crée plusieurs files d’attente pour que chaque machine virtuelle effectue l’action.

Effectuez les étapes suivantes pour voir les détails de l’appel.

1. Dans le portail Azure, accédez à **Azure Functions**.
1. Sélectionnez l’application de fonction pour la fonctionnalité de démarrage/d’arrêt des machines virtuelles v2 (préversion) dans la liste.
1. Sélectionnez **Fonctions** dans le volet gauche.
1. Dans la liste, vous voyez plusieurs fonctions associées à chaque scénario. Sélectionnez la fonction HTTP **Scheduled**.
1. Sélectionnez **Superviser** dans le volet gauche.
1. Sélectionnez la dernière trace d’exécution pour voir les détails de l’appel, et accéder à une journalisation détaillée dans la section des messages.
1. Répétez les mêmes étapes pour chaque fonction décrite dans le cadre de l’analyse du flux d’exécution évoquée plus haut.

Pour en savoir plus sur la supervision d’Azure Functions, consultez [Analyser la télémétrie d’Azure Functions dans Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail la supervision d’Azure Functions et des applications logiques :

* [Superviser Azure Functions](../../azure-functions/functions-monitoring.md).

* [Guide pratique pour configurer la supervision d’Azure Functions](../../azure-functions/configure-monitoring.md).

* [Superviser les applications logiques](../../logic-apps/monitor-logic-apps.md).