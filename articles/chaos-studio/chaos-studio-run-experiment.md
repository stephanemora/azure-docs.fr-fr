---
title: Exécuter et gérer une expérience de chaos dans Azure Chaos Studio
description: Découvrez comment démarrer, arrêter, afficher les détails et afficher l’historique d’une expérience de chaos dans Azure Chaos Studio
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06abbe99467af52cb7867d31f31d4aa2d727d0ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096501"
---
# <a name="run-and-manage-an-experiment-in-azure-chaos-studio"></a>Exécuter et gérer une expérience dans Azure Chaos Studio

Vous pouvez utiliser une expérience de chaos pour vérifier que votre application résiste aux défaillances en les provoquant dans un environnement contrôlé. Cet article fournit une vue d’ensemble de l’utilisation d’une expérience de chaos que vous avez créée précédemment.

## <a name="start-an-experiment"></a>Commencer une expérience

1. Ouvrez le [portail Azure](https://portal.azure.com).

2. Recherchez **Chaos Studio (version préliminaire)** dans la barre de recherche.

3. Cliquez sur **Expériences**. Il s’agit de la liste des expériences qui vous permet de démarrer, d’arrêter ou de supprimer des expériences en bloc ou de créer une nouvelle expérience.

    ![Liste d’expériences dans le portail](images/run-experiment-list.png)

4. Cliquez sur votre expérience. La page vue d’ensemble de l’expérience vous permet de démarrer, d’arrêter et de modifier votre expérience, d’afficher des détails essentiels sur la ressource et d’afficher l’historique. Cliquez sur le bouton **Démarrer**, puis sur **OK** pour commencer votre expérience.

    ![Démarrer l’expérience](images/run-experiment-start.png)

5. L’état de l’expérience indique *PreProcessingQueued*, *WaitingToStart* et enfin *Running*.

## <a name="view-experiment-history-and-details"></a>Afficher l’historique et les détails de l’expérience

1. Une fois l’expérience en cours d’exécution, cliquez sur **Détails** sur l’exécution actuelle sous **Historique** pour afficher l’état détaillé et les erreurs.

    ![Historique d’exécution](images/run-experiment-history.png)

2. La vue Détails de l’expérience affiche l’état d’exécution de chaque étape, branche et erreur. Cliquez sur une erreur.

    ![Détails de l’expérience](images/run-experiment-details.png)

3. Les détails de l’erreur affichent des informations supplémentaires sur l’exécution de l’erreur, notamment les cibles qui ont échoué ou réussi et pourquoi. En cas d’erreur lors de l’exécution de votre expérience, les informations de débogage s’affichent ici.

    ![Détails de l’erreur](images/run-experiment-fault.png)

## <a name="edit-experiment"></a>Modifier l’expérience

1. Revenez à la vue d’ensemble de l’expérience et cliquez sur le bouton **Modifier**.

    ![Modifier l’expérience](images/run-edit.png)

2. Il s’agit du même concepteur expérimental que celui utilisé pour créer l’expérience. Vous pouvez ajouter ou supprimer des étapes, des branches et des erreurs, ainsi que modifier les paramètres et les cibles d’erreur. Pour modifier une erreur, cliquez sur **...** à côté de l’erreur.

    ![Modifier l’erreur](images/run-edit-ellipses.png)

3. Lorsque vous avez terminé les modifications, cliquez sur **Enregistrer**. Si vous souhaitez ignorer vos modifications sans les enregistrer, cliquez sur le bouton **Fermer (X)** dans le coin supérieur droit.
  ![Enregistrer l’expérience](images/run-edit-save.png)

> [!WARNING]
> Si vous avez ajouté des cibles à votre expérience, pensez à ajouter une attribution de rôle sur la ressource cible pour votre identité d’expérimentation.

## <a name="delete-experiment"></a>Supprimer l’expérience
1. Revenez à la liste des expérimentations et cochez la ou les expériences que vous souhaitez supprimer. Cliquez sur **Supprimer** dans la barre d’outils au-dessus de la liste des expériences. Vous devrez peut-être cliquer sur les points de suspension (...) pour voir l’option supprimer, en fonction de la résolution de l’écran.

    ![Supprimer l’expérience](images/run-delete.png)

2. Cliquez sur **Oui** pour confirmer la suppression de la ressource.

3. Vous pouvez également ouvrir une expérience et cliquer sur le bouton **Supprimer** dans la barre d’outils.
