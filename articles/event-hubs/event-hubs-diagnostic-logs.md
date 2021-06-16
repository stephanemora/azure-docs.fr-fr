---
title: Configurer des journaux de diagnostic - Azure Event Hubs | Microsoft Docs
description: Découvrez comment configurer les journaux d’activité et de diagnostic pour Event Hubs dans Azure.
ms.topic: article
ms.date: 06/13/2021
ms.openlocfilehash: d981f1d01579719ecd055307f8a9877be94072d9
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112060798"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Configurer les journaux de diagnostic pour un Event Hub Azure.

Vous pouvez afficher deux types de journaux d’activité pour Azure Event Hubs :

* **[Journaux d’activité](../azure-monitor/essentials/platform-logs-overview.md)** : Ces journaux comportent des informations sur les opérations effectuées sur un travail. Les journaux d’activité sont toujours activés. Vous pouvez consulter les entrées des journaux d’activité en sélectionnant **Journal des activités** dans le volet gauche de votre espace de noms Event Hub dans le portail Azure. Par exemple : « Créer ou mettre à jour un espace de noms », « Créer ou mettre à jour un hub d’événements ».

    ![Journal d’activité d’un espace de noms Event Hubs](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Journaux de diagnostic](../azure-monitor/essentials/platform-logs-overview.md)** : Les journaux de diagnostic fournissent des informations plus détaillées sur les opérations et les actions qui sont effectuées en relation avec votre espace de noms à l’aide de l’API ou via les clients de gestion sur le Kit de développement logiciel (SDK) du langage. 
    
    La section suivante montre comment activer les journaux de diagnostic pour un espace de noms Event Hubs.

## <a name="enable-diagnostic-logs"></a>Activer les journaux de diagnostic
Les journaux de diagnostic sont désactivés par défaut. Pour activer les journaux de diagnostic, effectuez les étapes suivantes :

1.  Dans le [portail Azure](https://portal.azure.com), accédez à votre espace de noms Event Hubs. 
2. Sélectionnez **Paramètres de diagnostic** sous **Supervision** dans le volet gauche, puis sélectionnez **+ Ajouter un paramètre de diagnostic**. 

    ![Page Paramètres de diagnostic, ajouter un paramètre de diagnostic](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. Dans la section **Détails de la catégorie**, sélectionnez les **types de journaux de diagnostic** que vous souhaitez activer. Vous trouverez plus d’informations sur ces catégories plus loin dans cet article. 
5. Dans la section **Détails de la destination**, définissez la cible d’archivage (destination) de votre choix, par exemple, un compte de stockage, un hub d’événements ou un espace de travail Log Analytics.

    ![Page Ajouter des paramètres de diagnostic](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres de diagnostic.

    Les nouveaux paramètres prennent effet au bout de 10 minutes environ. Après cela, les journaux d’activité apparaissent dans la cible d’archivage configurée, dans le volet **Journaux de diagnostic**.

    Pour plus d’informations sur la configuration des diagnostics, consultez la [vue d’ensemble des journaux de diagnostic Azure](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Catégories de journaux de diagnostic
[!INCLUDE [event-hubs-diagnostic-log-schema](../../includes/event-hubs-diagnostic-log-schema.md)]



## <a name="next-steps"></a>Étapes suivantes
- [Présentation d’Event Hubs](./event-hubs-about.md)
- [Exemples de hubs d’événements](sdks.md)
- Prise en main des hubs d’événements
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-node-get-started-send.md)
