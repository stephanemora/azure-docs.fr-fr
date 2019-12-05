---
title: Utiliser le flux temps réel de chasse dans Azure Sentinel pour détecter les menaces | Microsoft Docs
description: Cet article décrit comment utiliser le flux temps réel de chasse dans Azure Sentinel pour effectuer le suivi des données.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2019
ms.author: cabailey
ms.openlocfilehash: e72b9012b41f50b36ba7d92582d0c697e6db0683
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546867"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Utiliser le flux temps réel de chasse dans Azure Sentinel pour détecter les menaces

> [!IMPORTANT]
> Le flux temps réel de chasse dans Azure Sentinel est actuellement disponible en préversion publique et progressivement déployé sur les locataires.
> Cette fonctionnalité est fournie sans contrat de niveau de service et est déconseillée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Utilisez le flux temps réel de chasse pour créer des sessions interactives dans lesquelles vous pouvez tester les nouvelles requêtes créées à mesure que des événements se produisent, recevoir les notifications des sessions quand une correspondance est trouvée et lancer des investigations si nécessaire. Vous pouvez créer rapidement une session de flux temps réel à l’aide d’une requête Log Analytics.

- **Tester les nouvelles requêtes créées à mesure que des événements se produisent**
    
    Vous pouvez tester et modifier des requêtes sans que cela provoque des conflits avec des règles actuelles qui sont activement appliquées aux événements. Une fois que vous avez vérifié que ces nouvelles requêtes fonctionnent comme prévu, vous pouvez facilement les promouvoir en règles d’alerte personnalisées, simplement en sélectionnant une option qui élève la session au niveau d’alerte.

- **Recevoir une notification en présence de menaces**
    
    Vous pouvez comparer les flux de données de menace aux données de journal agrégées et être averti quand une correspondance est établie. Les flux de données de menace sont des flux continus de données qui ont trait à des menaces potentielles ou actuelles. La notification peut donc indiquer une menace potentielle pour votre organisation. Créez une session de flux temps réel à la place d’une règle d’alerte personnalisée si vous souhaitez être averti d’un problème potentiel sans pour autant consacrer du temps à la gestion d’une règle d’alerte personnalisée.

- **Lancer des investigations**
    
    Si une investigation active existe déjà pour une ressource telle qu’un hôte ou un utilisateur, vous pouvez examiner une activité spécifique (ou toutes les activités) dans les données de journal à mesure que l’activité est effectuée sur cette ressource. Vous pouvez recevoir une notification quand cette activité se produit.


## <a name="create-a-livestream-session"></a>Créer une session de flux temps réel

Vous pouvez créer une session de flux temps réel à partir d’une requête de chasse existante ou bien créer votre session à partir de zéro.

1. Dans le portail Azure, accédez à **Sentinel** > **Gestion des menaces** > **Repérage**.

2. Pour créer une session de flux temps réel à partir d’une requête de chasse :
    
    1. Sous l’onglet **Requêtes**, recherchez la requête de chasse à utiliser.
    2. Cliquez avec le bouton droit sur la requête et sélectionnez **Add to Livestream** (Ajouter au flux temps réel). Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![créer une session de flux temps réel à partir d’une requête de chasse Azure Sentinel](./media/livestream/livestream-from-query.png)

3. Pour créer une session de flux temps réel à partir de zéro : 
    
    1. Sélectionnez l’onglet **Flux temps réel**.
    2. Sélectionnez **Accéder au stream en direct**.
    
4. Dans le volet **Flux temps réel** :
    
    - Si vous avez démarré une session de flux temps réel à partir d’une requête existante, examinez la requête et apportez-y les modifications souhaitées éventuelles.
    - Si vous avez démarré une session de flux temps réel à partir de zéro, créez votre requête. 

5. Dans la barre de commandes, sélectionnez **Lecture**.
    
    La barre d’état, sous la barre de commandes, indique si votre session de flux temps réel est active ou suspendue. Dans l’exemple suivant, la session est active :
    
    > [!div class="mx-imgBorder"]
    > ![créer une session de flux temps réel à partir d’une requête de chasse Azure Sentinel](./media/livestream/livestream-session.png)

6. Dans la barre de commandes, sélectionnez **Enregistrer**.
    
    Sauf si vous sélectionnez **Suspendre**, la session reste active jusqu’à ce que vous vous déconnectiez du portail Azure.

## <a name="view-your-livestream-sessions"></a>Afficher vos sessions de flux temps réel

1. Dans le portail Azure, accédez à l’onglet **Sentinel** > **Gestion des menaces** > **Chasse** > **Flux temps réel**.

2. Sélectionnez la session de flux temps réel que vous souhaitez afficher ou modifier. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![créer une session de flux temps réel à partir d’une requête de chasse Azure Sentinel](./media/livestream/livestream-tab.png)
    
    La session de flux temps réel sélectionnée s’ouvre. Vous pouvez ensuite la lire, la suspendre, la modifier, etc.

## <a name="receive-notifications-when-new-events-occur"></a>Recevoir des notifications quand de nouveaux événements se produisent

Étant donné que les notifications de flux temps réel pour les nouveaux événements utilisent les notifications du portail Azure, vous voyez toujours ces notifications dans le portail Azure. Par exemple :

![Notification du portail Azure pour la session de flux temps réel](./media/livestream/notification.png)

Sélectionnez la notification pour ouvrir le volet **Flux temps réel**.
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Élever une session de flux temps réel au niveau d’alerte

Vous pouvez promouvoir une session de flux temps réel en une nouvelle alerte en sélectionnant **Élever au niveau d’alerte** dans la barre de commandes de la session de flux temps réel correspondante :

> [!div class="mx-imgBorder"]
> ![Élever une session de flux temps réel au niveau d’alerte](./media/livestream/elevate-to-alert.png)

Cette action ouvre l’Assistant Création de règle, qui est prérempli avec la requête associée à la session de flux temps réel.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser le flux temps réel de chasse dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :


- [Proactively hunt for threats](hunting.md) (Rechercher des menaces de façon proactive)
- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des blocs-notes pour exécuter des campagnes de repérage automatisées)
