---
title: Fichier Include
description: Fichier Include
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028589"
---
>[!IMPORTANT]
>Vous pouvez utiliser les ressources que vous avez créé comme composants requis pour les autres didacticiels de service Azure Machine Learning et les articles de savoir-faire.


### <a name="delete-everything"></a>Supprimer tout

Si vous ne souhaitez pas utiliser autre chose que vous avez créé, supprimez le groupe de ressources ensemble afin d’éviter les frais liés à :

1. Dans le portail Azure, sélectionnez **groupes de ressources** sur le côté gauche de la fenêtre.
 
   ![Supprimer un groupe de ressources dans le portail Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Dans la liste, sélectionnez le groupe de ressources que vous avez créé.

1. Sur le côté droit de la fenêtre, sélectionnez le bouton de sélection (**...** ).

1. Sélectionnez **Supprimer le groupe de ressources**.

Suppression du groupe de ressources supprime également toutes les ressources que vous avez créé dans l’interface visuelle.  

### <a name="delete-only-the-compute-target"></a>Supprimez uniquement la cible de calcul

La cible de calcul que vous avez créé ici *automatiquement opère* aux nœuds de zéro quand il n’est pas utilisé. Cela consiste à réduire les frais. Si vous souhaitez supprimer la cible de calcul, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre espace de travail.

    ![Supprimer la cible de calcul](./media/aml-ui-cleanup/delete-compute-target.png)

1. Dans le **calcul** section de votre espace de travail, sélectionnez la ressource.

1. Sélectionnez **Supprimer**.

### <a name="delete-individual-assets"></a>Supprimer des ressources individuelles

Dans l’interface visuelle où vous avez créé votre expérience, supprimer des ressources individuelles en les sélectionnant puis en sélectionnant le **supprimer** bouton.

![Supprimer les expériences](./media/aml-ui-cleanup/delete-experiment.png)
