---
title: Azure Event Grid - Activer les journaux de diagnostic pour une rubrique
description: Cet article fournit des instructions pas à pas sur l’activation des journaux de diagnostic pour une rubrique Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960202"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Journaux de diagnostic pour un rubrique Azure Event Grid
Les paramètres de diagnostic permettent aux utilisateurs Event Grid de capturer et d’afficher les journaux d’échec de publication et de remise dans l’un des emplacements suivants : compte Stockage Azure, Event Hub ou espace de travail Log Analytics. Cet article fournit des instructions pas à pas pour activer les journaux de diagnostic pour une rubrique Event Grid.

## <a name="prerequisites"></a>Conditions préalables requises

- Rubrique Event Grid approvisionnée
- Destination approvisionnée pour la capture de journaux de diagnostic Il peut s'agir d'une des destinations suivantes :
    - Compte Azure Storage
    - Event Hub
    - Espace de travail Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Étapes d’activation des journaux de diagnostic pour une rubrique

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez à la rubrique Event Grid pour laquelle vous souhaitez activer les paramètres de journal de diagnostic. 
3. Sous **Supervision**, sélectionnez **Paramètres de diagnostic** dans le menu de gauche.
4. Dans la page **Paramètres de diagnostic**, sélectionnez **Ajouter un nouveau paramètre de diagnostic**. 
    
    ![Bouton Ajouter un paramètre de diagnostic](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Donnez un **nom** au paramètre de diagnostic. 

    ![Paramètres de diagnostic - nom](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Activez une ou plusieurs destinations de capture pour les journaux, puis configurez-les en sélectionnant une ressource de capture créée précédemment. 
    - Si vous sélectionnez **Archiver dans un compte de stockage**, sélectionnez **Compte de stockage - Configurer**, puis le compte de stockage dans votre abonnement Azure. 

        ![Archiver dans un compte de stockage Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Si vous sélectionnez **Diffuser vers un hub d'événements**, sélectionnez **Hub d'événements - Configurer**, puis l’espace de noms Event Hubs, le hub d'événements et la stratégie d’accès. 
        ![Diffuser vers un hub d’événements](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Si vous sélectionnez **Envoyer à Log Analytics**, sélectionnez un espace de travail Log Analytics.
        ![Envoyer à Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Sélectionnez les options **DeliveryFailures** et **PublishFailures** dans la section **Journal**. 
    ![Sélectionner les défaillances](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Sélectionnez **Enregistrer**. Sélectionnez **X** dans l’angle droit pour fermer la page. 
9. À présent, dans la page **Paramètres de diagnostic**, vérifiez la présence d'une nouvelle entrée dans la table **Paramètres de diagnostic**. 
    ![Paramètre de diagnostic dans la liste](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Vous pouvez également activer la collecte de toutes les métriques de la rubrique. 

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’une aide supplémentaire, publiez votre problème sur le forum [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) ou ouvrez un [ticket de support](https://azure.microsoft.com/support/options/). 
