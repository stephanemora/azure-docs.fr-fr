---
title: Gérer la haute disponibilité avec redondance interzone - Portail Azure - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article explique comment activer ou désactiver la haute disponibilité avec redondance interzone dans Azure Database pour PostgreSQL - Serveur flexible à l’aide du portail Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/07/2021
ms.openlocfilehash: 5a7b8c2f76abc9dd41894280e7cf610d0cae85ea
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554866"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Gérer la haute disponibilité avec redondance interzone dans Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en préversion

Cet article décrit comment activer ou désactiver la configuration de haute disponibilité avec redondance interzone dans votre serveur flexible.

La fonctionnalité de haute disponibilité provisionne physiquement des réplicas principaux et secondaires séparés dans différentes zones. Pour plus d’informations, consultez la [documentation sur les concepts de haute disponibilité](./concepts-high-availability.md). Vous pouvez choisir d’activer la haute disponibilité pendant ou après la création d’un serveur flexible. Cette page fournit des instructions sur la façon d’activer ou de désactiver la haute disponibilité. Cette opération ne change pas vos autres paramètres, notamment la configuration du réseau virtuel, les paramètres du pare-feu et la conservation de sauvegarde. De même, l’activation et la désactivation de la haute disponibilité sont des opérations en ligne qui n’ont pas d’impact sur la connectivité et les opérations de votre application.

## <a name="pre-requisites"></a>Conditions préalables

La haute disponibilité avec redondance interzone est disponible uniquement dans les régions où plusieurs zones sont prises en charge. 

## <a name="enable-high-availability-during-server-creation"></a>Activer la haute disponibilité lors de la création du serveur

Cette section fournit des détails spécifiques pour les champs à haute disponibilité. Vous pouvez suivre ces étapes pour déployer la haute disponibilité lors de la création de votre serveur flexible.

1.  Dans le [portail Azure](https://portal.azure.com/), choisissez Serveur flexible, puis cliquez sur Créer.  Pour plus d’informations sur la façon de renseigner des champs tels que **Abonnement**, **Groupe de ressources**, **Nom du serveur**, **Région** et ainsi de suite, consultez la documentation relative à la création de serveur.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Afficher l’abonnement et la région":::

2.  Choisissez votre **zone de disponibilité**. C’est utile si vous souhaitez colocaliser votre application dans la même zone de disponibilité que la base de données afin de réduire la latence. Choisissez **Aucune préférence** si vous souhaitez que le serveur flexible soit déployé sur n’importe quelle zone de disponibilité.
    ![Sélection de la zone de disponibilité]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="Sélection de la zone de disponibilité":::  

3.  Cochez la case **Haute disponibilité avec redondance interzone** dans l’option Disponibilité.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Case à cocher Haute disponibilité":::

4.  Si vous souhaitez changer le calcul et le stockage par défaut, cliquez sur **Configurer le serveur**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="configurer le serveur - calcul et stockage":::  

5.  Si l’option haute disponibilité est cochée, le niveau modulable ne sera pas un choix possible. Vous pouvez choisir le niveau de calcul **Usage général** ou **Mémoire optimisée**. Ensuite, vous pouvez sélectionner la **taille de calcul** de votre choix dans la liste déroulante.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Sélection du niveau de calcul":::  


6.  Sélectionnez la **taille de stockage** en Gio à l’aide de la barre glissante, puis une **période de conservation des sauvegardes** comprise entre sept et 35 jours.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Sauvegarde du stockage"::: 

7. Cliquez sur **Enregistrer**. 

## <a name="enable-high-availability-post-server-creation"></a>Activer la haute disponibilité après la création du serveur

Effectuez les étapes suivantes pour activer la haute disponibilité pour votre serveur flexible existant.

1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur flexible PostgreSQL.

2.  Dans la page du serveur flexible, cliquez sur **Haute disponibilité** dans le panneau de gauche pour ouvrir la page de haute disponibilité.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Sélection du panneau de gauche"::: 

3.  Cochez la case **Haute disponibilité redondante interzone** pour **activer** l’option, puis cliquez sur **Enregistrer** pour enregistrer la modification.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Activer la haute disponibilité"::: 

4.  Une boîte de dialogue de confirmation indique qu’en cas d’activation de la haute disponibilité, vos frais augmenteront en raison d’un déploiement de serveur et de stockage supplémentaire.

5.  Cliquez sur le bouton **Activer la haute disponibilité** pour activer la haute disponibilité.

6.  Une notification indique que le déploiement de la haute disponibilité est en cours.

## <a name="disable-high-availability"></a>Désactiver la haute disponibilité

Suivez ces étapes pour désactiver la haute disponibilité de votre serveur flexible qui est déjà configuré avec la redondance de zone.

1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur flexible Azure Database pour PostgreSQL.

2.  Dans la page du serveur flexible, cliquez sur **Haute disponibilité** dans le panneau avant pour ouvrir la page de haute disponibilité.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Sélection du panneau de gauche"::: 

3.  Décochez la case **Haute disponibilité avec redondance interzone** pour **désactiver** l’option. Cliquez ensuite sur **Enregistrer** pour enregistrer la modification.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Désactiver la haute disponibilité"::: 

4.  Une boîte de dialogue de confirmation s’affiche, dans laquelle vous pouvez confirmer la désactivation de la haute disponibilité.

5.  Cliquez sur le bouton **Désactiver la haute disponibilité** pour désactiver la haute disponibilité.

6.  Une notification indique que la désactivation du déploiement à haute disponibilité est en cours.

## <a name="forced-failover"></a>basculement forcé

Suivez ces étapes pour forcer le basculement de votre serveur principal vers le serveur flexible de secours. Le serveur principal est alors immédiatement arrêté et un basculement vers le serveur de secours est déclenché. Cette procédure est utile dans certains cas, notamment pour tester la durée de basculement de votre charge de travail à la suite d’une panne imprévue.

1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur flexible existant pour lequel la fonctionnalité de haute disponibilité est déjà activée.
2.  Dans la page du serveur flexible, cliquez surHaute disponibilité dans le panneau avant pour ouvrir la page de haute disponibilité.
3.  Vérifiez la zone de disponibilité principale et la zone de disponibilité de secours
4.  Cliquez sur Basculement forcé pour lancer la procédure de basculement manuel. Une fenêtre contextuelle vous informe du temps d’arrêt potentiel jusqu’à la fin du basculement. Lisez le message, puis cliquez sur OK.
5.  Une notification indique que le basculement est en cours.
6.  Une fois le basculement vers le serveur de secours réussi, une notification s’affiche.
7.  Vérifiez la nouvelle zone de disponibilité principale et la zone de disponibilité de secours.
    
    :::image type="content" source="./media/how-to-manage-high-availability-portal/ha-forced-failover.png" alt-text="Basculement forcé à la demande"::: 

>[!IMPORTANT] 
> * N’effectuez pas de basculements consécutifs sans pause. Attendez au moins 15-20 minutes entre chaque basculement, ce qui permettra également au nouveau serveur de secours d’être entièrement établi.
>
> * La durée d’ensemble de l’opération indiquée dans le portail peut être plus longue que le temps d’arrêt réel subi par l’application. Mesurez le temps d’arrêt du point de vue de l’application. 

## <a name="planned-failover"></a>Basculement planifié

Suivez ces étapes pour effectuer un basculement planifié de votre serveur principal vers le serveur flexible de secours. Le serveur de secours est alors préparé, puis le basculement est effectué. Le temps d’arrêt est minimal, car un basculement approprié est effectué vers le serveur de secours. Cette approche est utile dans certaines situations, par exemple pour ramener le serveur principal dans la zone de disponibilité préférée après un événement de basculement.
1.  Dans le [portail Azure](https://portal.azure.com/), sélectionnez votre serveur flexible existant pour lequel la fonctionnalité de haute disponibilité est déjà activée.
2.  Dans la page du serveur flexible, cliquez surHaute disponibilité dans le panneau avant pour ouvrir la page de haute disponibilité.
3.  Vérifiez la zone de disponibilité principale et la zone de disponibilité de secours
4.  Cliquez sur Basculement planifié pour lancer la procédure de basculement manuel. Une fenêtre contextuelle vous informe du processus à suivre. Lisez le message, puis cliquez sur OK.
5.  Une notification indique que le basculement est en cours.
6.  Une fois le basculement vers le serveur de secours réussi, une notification s’affiche.
7.  Vérifiez la nouvelle zone de disponibilité principale et la zone de disponibilité de secours.
        :::image type="content" source="./media/how-to-manage-high-availability-portal/ha-planned-failover.png" alt-text="Basculement planifié à la demande"::: 

>[!IMPORTANT] 
>
> * N’effectuez pas de basculements consécutifs sans pause. Attendez au moins 15-20 minutes entre chaque basculement, ce qui permettra également au nouveau serveur de secours d’être entièrement établi.
>
> * Il est recommandé d’effectuer un basculement planifié lorsque l’activité est faible.
>
> * La durée d’ensemble de l’opération peut être plus longue que le temps d’arrêt réel subi par l’application. Mesurez le temps d’arrêt du point de vue de l’application.


## <a name="next-steps"></a>Étapes suivantes

-   Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
-   Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
