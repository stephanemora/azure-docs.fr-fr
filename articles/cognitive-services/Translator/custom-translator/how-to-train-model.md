---
title: Effectuer l'apprentissage d'un modèle - Custom Translator
titleSuffix: Azure Cognitive Services
description: L'apprentissage est une étape importante lors de la création d'un modèle de traduction. L'apprentissage repose sur les documents que vous sélectionnez.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: c29a0b8b429705bb0315c37fc6fe63eb8d77511f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996701"
---
# <a name="train-a-model"></a>Effectuer l’apprentissage d’un modèle

L'apprentissage est une étape importante lors de la création d'un modèle de traduction, car sans lui le modèle ne peut pas être créé. L'apprentissage repose sur les documents que vous sélectionnez.

Pour effectuer l'apprentissage d'un modèle :

1.  Sélectionnez le projet dans lequel vous souhaitez générer un modèle.

2.  L'onglet Données du projet affiche tous les documents pertinents pour la paire de langues du projet. Sélectionnez manuellement les documents que vous souhaitez utiliser pour effectuer l'apprentissage de votre modèle. Vous pouvez sélectionner les documents d'apprentissage, d'optimisation et de test à partir de cet écran. Et il vous suffit de sélectionner l'ensemble d'apprentissage et de laisser Custom Translator créer les ensembles d'optimisation et de test pour vous.

    -  Nom du document : nom du document.

    -  Association : déterminez si le document est un document bilingue ou unilingue. Les documents unilingues ne sont actuellement pas pris en charge pour l'apprentissage.

    -  Type de document : entraînement, optimisation, test ou dictionnaire.

    -  Paire de langues : affiche la langue source et la langue cible du projet.

    -  Phrases sources : affiche le nombre de phrases extraites du fichier source.

    -  Phrases cibles : affiche le nombre de phrases extraites du fichier cible.

    ![Effectuer l'apprentissage du modèle](media/how-to/how-to-train-model.png)

3.  Cliquez sur le bouton Effectuer l'apprentissage.

4.  Dans la boîte de dialogue, spécifiez un nom pour votre modèle.

5.  Cliquez sur Effectuer l'apprentissage du modèle.

    ![Boîte de dialogue Effectuer l'apprentissage du modèle](media/how-to/how-to-train-model-2.png)

6.  Custom Translator soumet l'apprentissage et affiche son état dans l'onglet Modèles.

    ![Page Effectuer l'apprentissage d'un modèle](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator prend en charge 10 formations simultanées au sein d’un espace de travail à tout moment.


## <a name="edit-a-model"></a>Modifier un modèle

Vous pouvez modifier un modèle à l'aide du lien Modifier de la page Détails du modèle.

1.  Cliquez sur l'icône en forme de crayon.

    ![Modifier le modèle](media/how-to/how-to-edit-model.png)

2.  Dans la boîte de dialogue modifier,

    1.  Nom du modèle (obligatoire) : donnez un nom significatif à votre modèle.

        ![Boîte de dialogue Modifier plus](media/how-to/how-to-edit-model-dialog.png)

3.  Cliquez sur Enregistrer.


## <a name="next-steps"></a>Étapes suivantes

- Apprenez à [afficher les détails du modèle](how-to-view-model-details.md).
