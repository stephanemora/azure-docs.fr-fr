---
title: Définitions utilisées dans la classification de texte personnalisée
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les définitions utilisées dans la classification de texte personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 0699f4c32a62be3b07fd795357302f71ffcce8c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096826"
---
# <a name="terms-and-definitions-used-in-custom-text-classification"></a>Termes et définitions utilisés dans la classification de texte personnalisée 

En savoir plus sur les définitions utilisées dans la classification de texte personnalisée. 

## <a name="project"></a>Project

Un projet est une zone de travail dans laquelle vous créez vos modèles IA personnalisés à partir de vos données. Votre projet est accessible uniquement par vous et d’autres personnes qui ont un accès de contributeur à la ressource Azure utilisée.
Pour pouvoir créer un projet de classification de texte personnalisée, vous devez [connecter votre ressource à un compte de stockage](how-to/create-project.md).
Dans le cadre du flux de création du projet, vous devez le connecter à un conteneur de blobs où vous avez chargé votre jeu de données. Votre projet comprend automatiquement tous les fichiers `.txt` disponibles dans votre conteneur. Vous pouvez avoir plusieurs modèles dans votre projet, tous construits à partir du même jeu de données. Pour plus d’informations, consultez l’article sur les [limites du service](service-limits.md).

Dans votre projet, vous pouvez effectuer les opérations suivantes :

* [Étiqueter vos données](./how-to/tag-data.md) : processus d’étiquetage de chaque fichier de votre jeu de données avec la ou les classes respectives pour que votre modèle apprenne à classer vos fichiers dans le cadre de l’entraînement.
* [Générer et entraîner votre modèle](./how-to/train-model.md) : étape principale de votre projet. Dans cette étape, votre modèle commence à apprendre à partir de vos données étiquetées. 
* [Voir les détails de l’évaluation du modèle](./how-to/view-model-evaluation.md) : consultez les performances de votre modèle pour déterminer si des améliorations sont possibles ou si vous êtes satisfait des résultats.
* [Améliorer le modèle (facultatif)](./how-to/improve-model.md) : déterminez ce qui ne va pas avec votre modèle et améliorez les performances.
* [Déployer le modèle](quickstart.md?pivots=language-studio#deploy-your-model) : rendez votre modèle disponible pour qu’il puisse être utilisé. 
* [Tester le modèle](quickstart.md?pivots=language-studio#test-your-model) : testez votre modèle et évaluez ses performances.

### <a name="project-types"></a>Types de projet

La classification de texte personnalisée prend en charge deux types de projets

* **Classification avec une seule étiquette** : vous pouvez attribuer une seule classe à chaque fichier de votre jeu de données. Par exemple, dans le cas d’un script vidéo, votre fichier peut uniquement être `Action`, `Thriller` ou `Romance`.

* **Classification avec plusieurs étiquettes** : vous pouvez attribuer **plusieurs** classes à chaque fichier de votre jeu de données. Par exemple, dans le cas d’un script vidéo, votre fichier peut être `Action`, ou `Action` et `Thriller` ou `Romance`.

## <a name="model"></a>Modèle

Un modèle est un objet entraîné pour effectuer une tâche en particulier, dans notre exemple, une classification de texte personnalisée. Pour entraîner le modèle, vous lui fournissez des données étiquetées qu’il apprend et qu’il utilise ensuite pour classer le texte. Dès que vous êtes satisfait des performances du modèle, vous pouvez le déployer afin qu’il soit [disponible pour la consommation](https://aka.ms/ct-runtime-swagger).

## <a name="class"></a>Classe

Une classe est une catégorie définie par l’utilisateur, qui indique la classification générale du texte. Vous étiquetez vos données avec les classes attribuées avant de les transmettre au modèle pour l’entraînement. 
