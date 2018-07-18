---
title: Utiliser l’interface de ligne de commande Azure multiplateforme pour créer des alertes classiques pour les services Azure | Microsoft Docs
description: Déclenchez des e-mails ou des notifications, ou appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions spécifiées sont remplies.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287096"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Utiliser l’interface de ligne de commande Azure multiplateforme pour créer des alertes de métriques classiques dans Azure Monitor pour les services Azure 

> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> Cet article décrit comment créer des alertes de métriques classiques plus anciennes. Désormais, Azure Monitor prend en charge des [alertes de métriques plus récentes et plus performantes](monitoring-near-real-time-metric-alerts.md). Ces alertes peuvent surveiller plusieurs métriques, et permettent d’obtenir des alertes sur des métriques dimensionnelles. L’interface de ligne de commande Azure sera bientôt prise en charge pour des alertes de métriques plus récentes.
>
>

Cet article montre comment configurer des alertes de métriques classiques Azure à l’aide de l’interface de ligne de commande (Azure CLI) multiplateforme.

> [!NOTE]
> Azure Monitor est le nouveau nom de ce qui était appelé « Azure Insights » jusqu’au 25 septembre 2016. Toutefois, les espaces de noms et donc les commandes qui sont décrits ici contiennent toujours le mot « insights ».

Vous pouvez recevoir une alerte en fonction des métriques pour vos services Azure, ou en fonction des événements qui se produisent dans Azure.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois quand la condition est remplie et une fois que la condition n’est plus remplie.    

* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou lorsqu’un événement particulier se produit. Pour en savoir plus sur les journaux d’activité, consultez [Créer des alertes de journal d’activité (classiques)](monitoring-activity-log-alerts.md). 

Vous pouvez configurer une alerte de métrique classique pour effectuer les opérations suivantes lors de son déclenchement :

* Envoyer des notifications par e-mail à l’administrateur du service et aux coadministrateurs. 
* Envoyer un e-mail aux adresses e-mail que vous spécifiez.
* Appeler un webhook.
* Démarrer l’exécution d’un runbook Azure (uniquement à partir du portail Azure pour l’instant).

Vous pouvez configurer et obtenir des informations sur les règles d’alerte de métrique classique à l’aide de : 

* [Le portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de ligne de commande Azure](insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Vous pouvez également obtenir de l’aide sur les commandes en saisissant une commande avec **-help** à la fin. Vous trouverez ci-dessous un exemple : 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Créer des règles d’alerte à l’aide de l’interface de ligne de commande Azure
1. Une fois les composants requis installés, connectez-vous à Azure. Consultez les [exemples d’interface de ligne de commande Azure Monitor](insights-cli-samples.md) pour les commandes dont vous avez besoin pour commencer. Ces commandes vous aident à vous connecter, indiquent quel abonnement vous utilisez et préparent l’exécution des commandes Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Pour répertorier les règles existantes sur un groupe de ressources, utilisez le format suivant : 

   **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Pour créer une règle, vous avez d’abord besoin de différentes informations importantes.
    * L’**ID de la ressource** pour laquelle vous souhaitez définir une alerte.
    * Les **définitions de métriques** qui sont disponibles pour cette ressource.

     Pour obtenir l’ID de la ressource, vous pouvez utiliser le Portail Azure. À supposer que la ressource soit déjà créée, sélectionnez-la dans le portail. Puis, dans le panneau suivant, dans la section **Paramètres**, sélectionnez **Propriétés**. **ID DE RESSOURCE** est un champ du panneau suivant. 
     
     Vous pouvez également obtenir l’ID de la ressource à l’aide d’[Azure Resource Explorer](https://resources.azure.com/).

     Voici un exemple d’ID de ressource d’une application web :

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Pour obtenir la liste des métriques disponibles et de leurs unités dans l’exemple de ressource précédent, utilisez la commande Azure CLI suivante :  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* est la granularité de la mesure disponible (à intervalles d’une minute). Vous disposez de différentes options de métriques lorsque vous utilisez différentes granularités.
     
4. Pour créer une règle d’alerte basée sur une métrique, utilisez une commande au format suivant :

    **azure insights alerts rule metric set***[options] &lt;ruleName&gt;&lt;location&gt;&lt;resourceGroup&gt;&lt;windowSize&gt;&lt;operator&gt;&lt;threshold&gt;&lt;targetResourceId&gt;&lt;metricName&gt;&lt;timeAggregationOperator&gt;*

    L’exemple suivant configure une alerte sur une ressource de site web. L’alerte se déclenche chaque fois qu’il reçoit constamment du trafic pendant cinq minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant cinq minutes.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Pour créer un webhook ou envoyer un e-mail lorsqu’une alerte de métrique classique se déclenche, commencez par créer l’e-mail ou le webhook. Puis créez la règle immédiatement après. Vous ne pouvez pas associer des webhooks ou e-mails à des règles qui ont déjà été créées.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Vous pouvez vérifier que vos alertes ont été correctement créées en examinant une règle en particulier.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Pour supprimer des règles, utilisez commande au format suivant :

    **insights alerts rule delete** [options] &lt;resourceGroup&gt;&lt;ruleName&gt;

    Ces commandes suppriment les règles qui ont été créées précédemment dans cet article.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md), notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez plus en détail la [configuration des webhooks dans les alertes](insights-webhooks-alerts.md).
* En savoir plus sur la [configuration des alertes sur les événements de journal d’activité](monitoring-activity-log-alerts.md).
* En savoir plus sur les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble de la collecte des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) pour collecter des métriques détaillées à fréquence élevée pour votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
