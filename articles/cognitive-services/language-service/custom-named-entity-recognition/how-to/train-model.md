---
title: Comment entraîner votre modèle de reconnaissance d’entités nommées (NER) personnalisées
titleSuffix: Azure Cognitive Services
description: En savoir plus sur l’entraînement de votre modèle pour la reconnaissance d’entités nommées (NER) personnalisées.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 6fdf5ad5ed2eb8291b9a43e6004d8b18c5879b93
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097924"
---
# <a name="train-your-custom-named-entity-recognition-ner-model"></a>Entraîner votre modèle de reconnaissance d’entités nommées (NER) personnalisées

L’entraînement est le processus dans lequel le modèle apprend à partir de vos [données étiquetées](tag-data.md). Une fois l’entraînement effectué, vous pouvez [utiliser les métriques d’évaluation du modèle](../how-to/view-model-evaluation.md) pour déterminer si vous devez [améliorer votre modèle](../how-to/improve-model.md).

La durée d’entraînement d’un modèle varie selon le jeu de données et peut prendre plusieurs heures. Vous pouvez uniquement entraîner un modèle à la fois, et vous ne pouvez pas créer ou entraîner d’autres modèles si un entraînement est déjà en cours dans le même projet. 

## <a name="prerequisites"></a>Prérequis

* Un [projet créé](create-project.md) correctement avec un compte de stockage Blob Azure configuré
    * Des données textuelles qui [ont été chargées](create-project.md#prepare-training-data) dans votre compte de stockage.
* Des [données étiquetées](tag-data.md)

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#application-development-lifecycle).

## <a name="data-split"></a>Division des données

Avant de commencer le processus d’entraînement, les fichiers de votre jeu de données sont divisés en deux groupes aléatoires :

* Le **jeu d’entraînement** contient 80 % des fichiers de votre jeu de données. Il s’agit du jeu principal utilisé pour entraîner le modèle.

* Le **jeu de test** contient 20 % des fichiers disponibles dans votre jeu de données. Ce jeu est utilisé pour fournir une [évaluation](../how-to/view-model-evaluation.md) non biaisée du modèle. Il n’est pas introduit dans le modèle pendant l’entraînement.

## <a name="train-model-in-language-studio"></a>Entraîner le modèle dans Language Studio

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/LanguageStudio).

2. Dans le menu de gauche, sélectionnez **Entraîner**.

3. Pour entraîner un nouveau modèle, sélectionnez **Entraîner un nouveau modèle** et tapez le nom du modèle dans la zone de texte ci-dessous. Vous pouvez **remplacer un modèle existant** en sélectionnant cette option et le modèle de votre choix dans la liste déroulante ci-dessous.

    :::image type="content" source="../media/train-model.png" alt-text="Créer un modèle" lightbox="../media/train-model.png":::

4. Sélectionnez le bouton **Entraîner** en bas de la page.

## <a name="next-steps"></a>Étapes suivantes

Une fois l’entraînement effectué, vous pouvez utiliser les [métriques d’évaluation du modèle](view-model-evaluation.md) pour éventuellement [améliorer votre modèle](improve-model.md). Dès que vous êtes satisfait de votre modèle, vous pouvez le déployer et le rendre disponible pour l’[extraction d’entités](call-api.md) à partir du texte.
