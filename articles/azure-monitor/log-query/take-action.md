---
title: Action de runbook d’Azure Automation initiée par l’utilisateur dans Log Analytics | Documents Microsoft
description: Cet article décrit comment exécuter un runbook Automation à partir d’un résultat de recherche Log Analytics à la demande.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 9194d5fe6553607ac5a0bb4e133da97f53790984
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424730"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Utilisation d’un runbook Automation sur un résultat de recherche dans les journaux Log Analytics

> [!NOTE]
> Le démarrage d’un runbook à partir des résultats de la recherche est une fonctionnalité du portail de recherche dans les journaux classique qui sera déconseillée le 15 février 2019. Vous pouvez configurer un groupe d’actions capable de démarrer un runbook en plus d'autres actions à partir d’une [règle d’alerte](../platform/alerts-log.md) dans Azure Monitor.

À partir d’un résultat de recherche de journal dans Azure Log Analytics, vous pouvez maintenant sélectionner **Agir** pour exécuter un runbook Automation.  Le runbook peut être utilisé pour résoudre le problème ou effectuer une autre action telle que la collecte d’informations de résolution de problèmes, l’envoi d’un e-mail ou la création d’une demande de service. 


## <a name="components-and-features-used"></a>Composants et fonctionnalités utilisés
* [Compte Azure Automation](../../automation/automation-quickstart-create-account.md)
* [Espace de travail Log Analytics](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Lancement d’un runbook à partir d’une recherche dans les journaux

Pour agir sur un événement et lancer un runbook à partir des résultats de votre recherche dans les journaux, vous créez une recherche dans les journaux, et à partir des résultats, vous devez appeler un runbook à la demande. Vous pouvez réaliser ces opérations à partir de la fonctionnalité de recherche dans les journaux (classiques) du [portail Azure](../../azure-monitor/log-query/log-query-overview.md). Dans cet exemple, nous effectuons une recherche dans les journaux à partir du portail Azure avec une démonstration de base de cette fonctionnalité.

1. Dans le portail Azure, cliquez sur **Tous les services** et sélectionnez **Log Analytics**.  
2. Sélectionnez votre espace de travail Log Analytics.
3. Dans l’espace de travail, sélectionnez **Journaux d’activité (classiques)**.  
4. Dans la page Recherche dans les journaux, effectuez une recherche dans les journaux.  
5. À partir des résultats de la recherche dans les journaux, cliquez sur l’ellipse à gauche de l’un des champs, et dans le menu contextuel, sélectionnez **Agir sur**.<br><br> ![Sélectionner Agir à partir des résultats de recherche](./media/take-action/log-search-takeaction-menuoption.png) 
6. Sélectionnez **Exécuter un runbook** et sélectionnez un runbook à exécuter.  Vous pouvez sélectionner n’importe quel runbook dans le compte Automation qui est lié à l’espace de travail Log Analytics.  Notez les points suivants :

    * Les runbooks sont organisés par balise
    * Les valeurs de paramètre d’entrée de runbook peuvent être sélectionnées directement à partir des champs de résultat de la recherche.  Une liste déroulante apparaît et affiche tous les champs disponibles dans le résultat à partir duquel effectuer la sélection.  
    * Vous pouvez choisir d’exécuter le runbook sur un [Runbook Worker hybride](../../automation/automation-hybrid-runbook-worker.md) installé sur la machine qui rencontre le problème si cette machine constitue le seul membre du groupe de Runbook Worker hybride correspondant.  Si le nom du groupe de Worker hybride correspond au nom de l’ordinateur qui se trouve dans les résultats de la recherche dans les journaux, le groupe est automatiquement sélectionné.    

6. Après que vous avez cliqué sur **Exécuter**, la page Tâches de runbook s’ouvre pour vous permettre de vérifier l’état de la tâche.   

Si vous sélectionnez un runbook qui a été configuré pour être [appelé à partir d’une alerte Log Analytics](../../automation/automation-create-alert-triggered-runbook.md), celui-ci possède un paramètre d’entrée appelé **WebhookData** qui est de type **objet**.  Si le paramètre d’entrée est obligatoire, vous devez transmettre les résultats de recherche au runbook pour qu’il puisse convertir la chaîne au format JSON en un type d’objet vous permettant de filtrer sur des éléments spécifiques que vous référencerez dans les activités de runbook.  Pour ce faire, sélectionnez **Résultats de la recherche (Objet)** dans la liste déroulante.<br><br> ![Sélectionner un objet de données Webhook pour le paramètre de runbook](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Étapes suivantes

* Pour connaître tous les champs de recherche et facettes disponibles dans Log Analytics, consultez les [informations de référence sur la recherche de journal avec Log Analytics](../../azure-monitor/log-query/log-query-overview.md) .
* Pour découvrir comment appeler un runbook Automation automatiquement, consultez [Appeler un runbook Automation Azure à partir d’une alerte Log Analytics](../../automation/automation-create-alert-triggered-runbook.md).  
