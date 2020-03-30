---
title: Afficher les détails d’un modèle – Custom Translator
titleSuffix: Azure Cognitive Services
description: 'L’onglet Models (Modèles), sous n’importe quel projet, donne des informations sur chaque modèle : nom, état, score BLEU, nombre de phrases d’apprentissage, d’optimisation et de test, etc.'
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595568"
---
# <a name="view-model-details"></a>Afficher les détails du modèle

L’onglet Models (Modèles) qui se trouve sous le projet présente tous les modèles entraînés pour ce projet.

Pour chacun des modèles du projet, ces informations s’affichent.

1.  Name (Nom) : nom du modèle.

2.  Status (État) : état du modèle. Un nouvel apprentissage conserve l’état Submitted (Soumis) jusqu'à ce qu’il soit accepté. Il devient ensuite Dataprocessing (Traitement de données) lorsque le service évalue le contenu des documents. Une fois cette évaluation terminée, l’état passe à Running (En cours d’exécution) : vous pouvez alors voir le nombre de phrases impliquées dans l’apprentissage, y compris dans les jeux d’optimisation et de test qui sont créés automatiquement. Voici la liste des états des modèles.

    -  Submitted (Soumis) : le backend traite les documents de ce modèle.

    -  TrainingQueued (En file d’attente) : l’apprentissage est dans la file d’attente du système de traduction automatique pour ce modèle.

    -  Running (En cours d’exécution) : l’apprentissage est en cours d’exécution dans le système de traduction automatique pour ce modèle.

    -  Succeeded (Réussi) : l’apprentissage a réussi dans le système de traduction automatique et un modèle est disponible. Dans cet état, un score BLEU s’affiche pour ce modèle.

    -  Deployed (Déployé) : le modèle entraîné est soumis au système de traduction automatique en vue de son déploiement.

    -  Undeploying (Déploiement en cours d’annulation) : le déploiement du modèle est en cours d’annulation.

    -  Undeployed (Déploiement annulé) : le processus d’annulation du déploiement du modèle a réussi.

    -  Training Failed (Échec de l’apprentissage) : l’apprentissage a échoué. En cas d’échec de l’apprentissage, relancez la tâche d’apprentissage. Si l’erreur persiste, contactez-nous. Ne supprimez pas le modèle qui a échoué.

    - DataProcessingFailed (Échec du traitement de données) : le traitement de données a échoué pour un ou plusieurs documents appartenant au modèle.

    - DeploymentFailed (Échec du déploiement) : le déploiement du modèle a échoué.

    - MigratedDraft (Brouillon migré) : le modèle est à l’état de brouillon après la migration de Hub vers Custom Translator.

4.  BLEU Score (Score BLEU) : score BLEU (Bilingual Evaluation Understudy) du modèle. Il révèle la qualité du système de traduction, en indiquant dans quelle mesure les traductions effectuées par le système de traduction à l’issue de cet apprentissage correspondent aux phrases de référence du jeu de données de test. Le score BLEU apparaît si l’apprentissage s’est terminé avec succès. Si l’apprentissage n’est pas fini ou a échoué, il ne s’affiche pas.

5.  Training (Apprentissage) : nombre total de phrases utilisées dans le jeu d’apprentissage.

6.  Tuning (Optimisation) : nombre total de phrases utilisées dans le jeu d’optimisation.

7.  Test (Test) : nombre total de phrases utilisées dans le jeu de test.

8.  Mono (Mono) : nombre total de phrases utilisées dans le jeu mono.

9.  Bouton d’action Deploy (Déployer) : un modèle dont l’apprentissage a réussi, mais qui n’a pas été déployé présente le bouton Deploy (Déployer). Si le modèle est déployé, le bouton Undeploy (Annuler le déploiement) s’affiche.

10. Delete (Supprimer) : bouton permettant de supprimer le modèle. Le fait de supprimer un modèle n’entraîne pas la suppression des documents utilisés pour le créer.

    ![Afficher les détails du modèle](media/how-to/how-to-view-model-details.png)

>[!Note]
>Pour comparer des apprentissages consécutifs sur les mêmes systèmes, il est important que le jeu d’optimisation et le jeu de test restent constants.

## <a name="view-model-training-details"></a>Afficher les informations d’apprentissage du modèle

Une fois l’apprentissage terminé, vous pouvez consulter des informations à ce sujet sur la page des détails. Sélectionnez un projet, recherchez et sélectionnez l’onglet Models (Modèles), puis choisissez un modèle.

La page du modèle comporte deux onglets : Training Details (Détails de l’apprentissage) et Test (Test).

1.  **Training Details (Détails de l’apprentissage) :** cet onglet présente la liste des documents utilisés dans l’apprentissage :

    -  Name (Nom) : nom du document.

    -  Type (Type) : type de document (en parallèle/mono).

    -  Nombre de phrases dans la langue source : nombre de phrases faisant partie de la langue source.

    -  Nombre de phrases dans la langue cible : nombre de phrases faisant partie de la langue cible.

    -  Aligned Sentences (Phrases alignées) : nombre de phrases alignées par Custom Translator pendant le processus d’alignement.

    -  Used Sentences (Phrases utilisées) : nombre de phrases utilisées par Custom Translator pendant cet apprentissage.

    ![Informations d’apprentissage du modèle](media/how-to/how-to-model-training-details.png)

2.  **Test (Test) :** cet onglet indique les détails du test d’un apprentissage réussi.

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [résultats des tests](how-to-view-system-test-results.md) et analysez les résultats de l’apprentissage.
