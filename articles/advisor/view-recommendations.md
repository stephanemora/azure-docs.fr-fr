---
title: Afficher les recommandations Azure Advisor vous concerner
description: Afficher et filtrer les recommandations Azure Advisor pour réduire le bruit.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59052836"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Afficher les recommandations Azure Advisor vous concerner

Azure fournit des recommandations pour vous aider à optimiser vos déploiements Azure. Dans l’Assistant, vous avez accès à certaines fonctionnalités qui vous aident à affiner vos recommandations uniquement à ceux qui vous intéressent pour vous.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurer les abonnements et les groupes de ressources

L’Assistant vous donne la possibilité de sélectionner les abonnements et les groupes de ressources qui vous intéressent pour vous et votre organisation. Vous voyez uniquement des recommandations pour les abonnements et les groupes de ressources que vous sélectionnez. Par défaut, toutes sont sélectionnées. Paramètres de configuration s’appliquent à l’abonnement ou groupe de ressources, les mêmes paramètres s’appliquent à tous les utilisateurs ayant accès à ce groupe de ressources ou abonnement. Paramètres de configuration peuvent être modifiés dans le portail Azure ou par programmation.

Pour apporter des modifications dans le portail Azure :

1. Ouvrez [Azure Advisor](https://aka.ms/azureadvisordashboard) dans le portail Azure.

1. Sélectionnez **Configuration** dans le menu.

   ![Menu de configuration de l’Assistant](./media/view-recommendations/configuration.png)

1. Cochez la case la **Include** colonne pour les abonnements ou les groupes de ressources pour recevoir des recommandations d’Advisor. Si la zone est désactivée, vous peut-être pas autorisé à modifier la configuration sur ce groupe de ressources ou abonnement. En savoir plus sur [autorisations dans Azure Advisor](permissions.md).

1. Cliquez sur **appliquer** en bas une fois que vous apportez une modification.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrage de l’affichage dans le portail Azure

Paramètres de configuration restent actifs jusqu'à ce que modifié. Si vous souhaitez restreindre l’affichage des recommandations pour un affichage unique, vous pouvez utiliser les listes déroulantes en haut du Panneau de l’Assistant. À partir de la vue d’ensemble, la haute disponibilité, sécurité, performances, coût et recommandation tous les panneaux, vous pouvez sélectionner les abonnements, Types de ressources et état de recommandation que vous souhaitez voir.

   ![Menu de filtrage de l’Assistant](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Faire disparaître et différer des recommandations

Azure Advisor vous permet d’ignorer ou de reporter des recommandations sur une ressource unique. Si vous ignorez une recommandation, vous ne voyez pas il à nouveau, sauf si vous l’activer manuellement. Toutefois, différer une recommandation vous permet de spécifier une durée après laquelle la recommandation est automatiquement réactivée. Report de peut être effectué dans le portail Azure ou par programmation.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Reporter une recommandation unique dans le portail Azure 

1. Ouvrez [Azure Advisor](https://aka.ms/azureadvisordashboard) dans le portail Azure.
1. Sélectionnez une catégorie de recommandation pour afficher vos recommandations
1. Sélectionnez une recommandation dans la liste des recommandations
1. Sélectionnez reporter ou ignorer pour la recommandation que vous souhaitez reporter ou ignorer

     ![Menu de filtrage de l’Assistant](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Reporter ou ignorer une plusieurs recommandations dans le portail Azure

1. Ouvrez [Azure Advisor](https://aka.ms/azureadvisordashboard) dans le portail Azure.
1. Sélectionnez une catégorie de recommandation pour afficher vos recommandations.
1. Sélectionnez une recommandation dans la liste des recommandations.
1. Sélectionnez la case à cocher à gauche de la ligne pour toutes les ressources que vous souhaitez reporter ou ignorer la recommandation.
1. Sélectionnez **reporter** ou **Dismiss** en haut à gauche de la table.

     ![Menu de filtrage de l’Assistant](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Vous avez besoin d’autorisations de collaborateur ou propriétaire d’ignorer ou de reporter une recommandation. En savoir plus sur les autorisations dans Azure Advisor.

> [!NOTE]
> Si les boîtes de sélection sont désactivés, recommandations peuvent être chargés. Veuillez attendre que toutes les recommandations charger avant d’essayer de reporter ou ignorer.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Réactiver une recommandation différée ou ignorée

Vous pouvez activer une recommandation qui a été reporté à plus tard ou fermée. Cette action peut être effectuée dans le portail Azure ou par programmation. Dans le portail Azure :

1. Ouvrez [Azure Advisor](https://aka.ms/azureadvisordashboard) dans le portail Azure.

1. Modifiez le filtre dans le panneau de vue d’ensemble de **Postponed**. Advisor affiche ensuite des recommandations différées ou ignorées.

    ![Menu de filtrage de l’Assistant](./media/view-recommendations/activate-postponed.png)

1. Sélectionnez une catégorie pour voir **Postponed** et **ignoré** recommandations.

1. Sélectionnez une recommandation dans la liste des recommandations. Cette opération ouvre des recommandations avec le **reporté à plus tard & fermée** onglet déjà choisi d’afficher les ressources pour lequel cette recommandation a été reporté à plus tard ou fermée.

1. Cliquez sur **activer** à la fin de la ligne. Une fois que vous cliquez dessus, la recommandation est active pour cette ressource et donc supprimé de cette table. La recommandation est désormais visible dans le **Active** onglet.
 
     ![Menu de filtrage de l’Assistant](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Étapes suivantes

Cet article explique comment vous pouvez afficher les recommandations qui vous intéressent pour vous dans Azure Advisor. Pour en savoir plus sur Advisor, consultez les ressources suivantes : 

- [Présentation d’Azure Advisor](advisor-overview.md)
- [Mise en route avec l’Assistant](advisor-get-started.md)
- [Autorisations dans Azure Advisor](permissions.md)



