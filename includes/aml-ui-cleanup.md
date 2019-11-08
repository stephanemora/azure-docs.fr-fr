---
title: Fichier Include
description: Fichier Include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489928"
---
>[!IMPORTANT]
>Vous pouvez utiliser les ressources que vous avez créées comme prérequis pour d’autres didacticiels et articles de guides pratiques Azure Machine Learning.

### <a name="delete-everything"></a>Tout supprimer

Si vous n’avez pas l’intention d’utiliser les éléments que vous avez créés, supprimez l’intégralité du groupe de ressources pour éviter des frais :

1. Dans le portail Azure, sélectionnez **Groupes de ressources** sur le côté gauche de la fenêtre.
 
   ![Supprimer un groupe de ressources dans le portail Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Dans la liste, sélectionnez le groupe de ressources créé.

1. Sur le côté droit de la fenêtre, sélectionnez le bouton de sélection ( **...** ).

1. Sélectionnez **Supprimer le groupe de ressources**.

La suppression du groupe de ressources supprime également toutes les ressources créées dans le concepteur.  

### <a name="delete-only-the-compute-target"></a>Supprimer uniquement la cible de calcul

La cible de calcul que vous avez créée ici *est automatiquement mise à l’échelle* sur zéro nœud quand elle n’est pas utilisée. Cela permet de réduire les frais. Si vous souhaitez supprimer la cible de calcul, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.

    ![Supprimer la cible de calcul](./media/aml-ui-cleanup/delete-compute-target.png)

1. Dans la section **Calcul** de votre espace de travail, sélectionnez la ressource.

1. Sélectionnez **Supprimer**.

### <a name="delete-individual-assets"></a>Supprimer des ressources individuelles

Dans le concepteur où vous avez créé votre expérience, supprimez des ressources individuelles en les sélectionnant, puis en sélectionnant le bouton **Supprimer**.

![Supprimer des ressources](./media/aml-ui-cleanup/delete-asset.png)

Les jeux de données peuvent être désinscrits de votre espace de travail en sélectionnant chaque jeu de données, puis **Annuler l’enregistrement**.

![Désinscrire le jeu de données](./media/aml-ui-cleanup/unregister-dataset.png)


