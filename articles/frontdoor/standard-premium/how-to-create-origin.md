---
title: Configurer une origine Azure Front Door Standard/Premium (préversion)
description: Cet article explique comment configurer une origine à l’aide d’Endpoint Manager.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741886"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Configurer une origine Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article vous montre comment créer une origine Azure Front Door Standard/Premium dans le groupe d’origine existant. 

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir créer une origine Azure Front Door Standard/Premium, vous devez avoir créé au moins un groupe d’origine.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Créer une origine Azure Front Door Standard/Premium

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et accédez à votre profil Azure Front Door Standard/Premium.

1. Sélectionnez **Groupe d’origine**. Ensuite, sélectionnez **+ Ajouter** pour créer un groupe d’origine.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Capture d’écran de la page d’arrivée du groupe d’origine.":::

1. Sur la page **Ajouter un groupe d’origine**, entrez un **Nom** unique pour le nouveau groupe d’origine.

1. Ensuite, sélectionnez **+ Ajouter une origine** pour ajouter une nouvelle origine à ce groupe d’origine. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Capture d’écran de la page Ajouter une origine.":::
  
    | Paramètre | Value |
    | --- | --- |
    | Nom | Entrez un nom unique pour la nouvelle origine d’Azure Front Door. |   
    | Type d'origine | Le type de ressource que vous souhaitez ajouter. Azure Front Door Standard/Premium prend en charge la découverte automatique de votre origine d’application depuis cotre service d’application, votre service cloud ou le stockage. Si vous voulez une autre ressource dans Azure ou un backend non Azure, sélectionnez **Hôte personnalisé**. |
    | Nom d’hôte  | Si vous n’avez pas sélectionné **Hôte personnalisé** pour le type d’hôte d’origine, sélectionnez votre serveur principal en choisissant le nom d’hôte d’origine dans la liste déroulante. |
    | En-tête de l’hôte d’origine | Entrez la valeur d’en-tête de l’hôte envoyée au serveur principal pour chaque requête. Pour plus d’informations, consultez [En-tête d’hôte d’origine](concept-origin.md#hostheader). |
    | Port HTTP | Entrez la valeur du port que l’origine prend en charge pour le protocole HTTP. |
    | Port HTTPS | Entrez la valeur du port que l’origine prend en charge pour le protocole HTTPS. |
    | Priority | Attribuez des priorités à votre origine différente lorsque vous souhaitez utiliser une origine de service primaire pour tout le trafic. Fournissez également des sauvegardes si l’origine de sauvegarde ou primaire est indisponible. Pour plus d'informations, veuillez consulter [Priorité](concept-origin.md#priority). |
    | Poids | Attribuez des poids à vos différentes origines si vous voulez répartir le trafic entre un ensemble d’origines, que ce soit de façon équitable ou selon des coefficients de pondération. Pour plus d’informations, veuillez consulter [Poids](concept-origin.md#weighted). |
    | Statut | Sélectionnez cette option pour activer l’origine. |
    | Règle | Sélectionnez les ensembles de règles qui seront appliqués à cet itinéraire. Pour plus d’informations sur la configuration des règles, consultez [Configurer un ensemble de règles pour Azure Front Door](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > Pendant la configuration, les API ne valident pas si l’origine est inaccessible depuis les environnements Front Door. Assurez-vous que Front Door peut atteindre votre origine.

1. Sélectionnez **Ajouter** pour créer une origine. L’origine créée doit apparaître dans la liste d’origines avec le groupe.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Capture d’écran de l’origine dans l’affichage de la liste.":::

1. Sélectionnez **Ajouter** pour ajouter le groupe d’origines au point de terminaison actuel. Le groupe d’origine doit apparaître dans le panneau du groupe d’origines.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Pour supprimer un groupe d’origines lorsque vous n’en avez plus besoin, cliquez sur le **...** , puis sélectionnez **Supprimer** dans la liste déroulante.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Capture d’écran montrant comment supprimer un groupe d’origines.":::

Pour supprimer une origine lorsque vous n’en avez plus besoin, cliquez sur le **...** , puis sélectionnez **Supprimer** dans la liste déroulante. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Capture d’écran montrant comment supprimer une origine.":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les domaines personnalisés, consultez [Ajout d’un domaine personnalisé](how-to-add-custom-domain.md) à votre point de terminaison Azure Front Door Standard/Premium.
