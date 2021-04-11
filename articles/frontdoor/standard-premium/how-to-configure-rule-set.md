---
title: 'Azure Front Door : configurer l’ensemble de règles dans Front Door'
description: Cet article fournit des conseils sur la configuration d’un ensemble de règles.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715596"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Configurer un ensemble de règles dans Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article explique comment créer un ensemble de règles et votre premier ensemble de règles dans le portail Azure. Vous apprendrez ensuite à associer l’ensemble de règles à un itinéraire à partir de la page de l’ensemble de règles ou d’Endpoint Manager.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Avant de pouvoir configurer un ensemble de règles, vous devez créer un profil Azure Front Door Standard/Premium. Pour plus d’informations, consultez la section [Démarrage rapide : créer un profil Azure Front Door Standard/Premium](create-front-door-portal.md).

## <a name="configure-rule-set-in-azure-portal"></a>Configurer l’ensemble de règles dans le portail Azure

1. Dans votre profil Front Door, sélectionnez **Ensemble de règles** sous **Paramètres**. Sélectionnez **Ajouter** et attribuez un nom à l’ensemble de règles.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="Capture d’écran de la page de l’ensemble de règles.":::
    
1. Sélectionnez **Ajouter une règle** pour créer votre première règle. Attribuez-lui un nom de règle. Ensuite, sélectionnez **Ajouter une condition** ou **Ajouter une action** pour définir votre règle. Vous pouvez ajouter jusqu’à 10 conditions et 5 actions pour une règle. Dans cet exemple, nous utilisons une variable de serveur pour ajouter un en-tête de réponse 8Geo-country* pour les requête incluant *contoso* dans l’URL.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="Capture d’écran de la page de configuration de l’ensemble de règles.":::
    
    > [!NOTE]
    > * Pour supprimer une condition ou une action de la règle, utilisez la corbeille à droite de la condition ou de l’action spécifique.
    > * Pour créer une règle qui s’applique à tout le trafic entrant, ne spécifiez aucune condition.
    > * Pour arrêter l’évaluation des règles restantes si une règle spécifique est remplie, cochez la case **Arrêter l’évaluation des règles restantes**. Si cette option est cochée, toutes les règles restantes de l’ensemble de règles ne sont pas exécutées, que les conditions de correspondance soient remplies ou non.  

1. Vous pouvez déterminer la priorité des règles dans votre ensemble de règles à l’aide des flèches pour déplacer les règles vers le haut ou le bas dans l’ordre de priorité. La liste est en ordre croissant, c’est-à-dire que la règle la plus importante est répertoriée en premier.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="Capture d’écran de la priorité de l’ensemble de règles." lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. Une fois que vous avez créé une ou plusieurs règles, sélectionnez **Enregistrer** pour terminer la création de votre ensemble de règles.

1. Associez ensuite l’ensemble de règles à un itinéraire afin qu’il puisse prendre effet. Vous pouvez associer l’ensemble de règles via la page de l’ensemble de règles ou vous pouvez accéder à Endpoint Manager pour créer l’association.
 
    **Page de l’ensemble de règles** : 
    
    1. Sélectionnez l’ensemble de règles à associer.
    
    1. Sélectionnez le lien *Non associé*.
     

    1. Ensuite, dans la page **Associer un itinéraire**, sélectionnez le point de terminaison et l’itinéraire que vous souhaitez associer à l’ensemble de règles. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="Capture d’écran de la page Créer un itinéraire.":::    
        
    1. Sélectionnez *Suivant* pour modifier l’ordre des ensembles de règles s’il existe plusieurs ensembles de règles pour l’itinéraire sélectionné. L’ensemble de règles sera exécuté de haut en bas. Vous pouvez modifier l’ordre en sélectionnant l’ensemble de règles et en le déplaçant vers le haut ou le bas. Sélectionnez ensuite *Associer*.
    
        > [!Note]
        > Vous ne pouvez associer un ensemble de règles qu’à un seul itinéraire sur cette page. Pour associer un ensemble de règles à des itinéraires multiples, utilisez Endpoint Manager.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="Capture d’écran de l’ordre des ensembles de règles.":::
    
    1. L’ensemble de règles est maintenant associé à un itinéraire. Vous pouvez consulter l’en-tête de réponse et voir que « Geo-country » est ajouté.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="Capture d’écran de la règle associée à un itinéraire.":::

   **Endpoint Manager** : 
    
    1. Accédez à Endpoint manager, sélectionnez le point de terminaison que vous souhaitez associer à l’ensemble de règles.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="Capture d’écran de la sélection du point de terminaison dans Endpoint Manager." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. Sélectionnez *Modifier le point de terminaison*.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="Capture d’écran montrant la sélection de l’option Modifier le point de terminaison dans Endpoint Manager." lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. Sélectionnez l’itinéraire. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="Capture d’écran de la sélection d’un itinéraire.":::
    
    1. Dans la page *Mettre à jour l’itinéraire*, dans *Règles*, sélectionnez les ensembles de règles que vous souhaitez associer à l’itinéraire dans la liste déroulante. Vous pouvez ensuite modifier l’ordre en déplaçant l’ensemble de règles vers le haut ou le bas. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="Capture d’écran de la page Mettre à jour un itinéraire.":::
    
    1. Sélectionnez ensuite *Mettre à jour* ou *Ajouter* pour terminer l’association.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Supprimer un ensemble de règles à partir de votre profil Azure Front Door

Dans les étapes précédentes, vous avez configuré et associé un ensemble de règles à votre itinéraire. Si vous ne voulez plus que l’ensemble de règles soit associé à votre Front Door, vous pouvez supprimer l’ensemble de règles en effectuant les étapes suivantes :

1. Accédez à la **page de l’ensemble de règles** sous **Paramètres** pour dissocier l’ensemble de règles de tous les itinéraires associés.

1. Développez l’itinéraire, puis sélectionnez les points de suspension. Sélectionnez ensuite *Modifier l’itinéraire*.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="Capture d’écran de l’itinéraire développé dans l’ensemble de règles.":::

1. Accédez à la section Règles sur la page Itinéraire, sélectionnez l’ensemble de règles, puis sélectionnez le bouton *Supprimer*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="Capture d’écran de la page Mettre à jour l’itinéraire pour supprimer un ensemble de règles." lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. Sélectionnez *Mettre à jour* pour dissocier l’ensemble de règles de l’itinéraire.

1. Répétez les étapes 2 à 5 pour dissocier les autres itinéraires associés à cet ensemble de règles jusqu’à ce que l’état des itinéraires affiche *Non associé*.

1. Vous pouvez supprimer un ensemble de règles *Non associé* en cliquant sur les points de suspension situés à droite, puis en sélectionnant *Supprimer*. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="Capture d’écran montrant comment supprimer un ensemble de règles.":::

1. L’ensemble de règles est maintenant supprimé.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des [En-têtes de sécurité avec un ensemble de règles](how-to-add-security-headers.md).
