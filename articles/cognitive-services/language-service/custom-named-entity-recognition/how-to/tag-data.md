---
title: Guide pratique pour étiqueter vos données pour la Reconnaissance d’entité nommée (NER) personnalisée
titleSuffix: Azure Cognitive Services
description: Découvrez comment étiqueter vos données pour une utilisation avec la Reconnaissance d’entité nommée (NER) personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 2328971a659bd4d4b147f9a708c1b5910e8ce12c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096818"
---
# <a name="tag-your-data-for-custom-named-entity-recognition-ner-in-language-studio"></a>Étiqueter vos données pour la Reconnaissance d’entité nommée (NER) personnalisée dans Language Studio

Avant de générer vos modèles d’extraction d’entité personnalisés, vous devez disposer de données étiquetées. Si vos données ne sont pas encore étiquetées, vous pouvez les étiqueter dans Language Studio. Pour étiqueter vos données, vous devez avoir [créé un projet](../quickstart.md).

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir étiqueter des données, vous avez besoin des éléments suivants :

* Un projet [correctement créé](create-project.md) avec un compte Stockage Blob Azure configuré
    * Des données texte qui [ont été chargées](create-project.md#prepare-training-data) sur votre compte de stockage

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#application-development-lifecycle).

## <a name="tag-your-data"></a>Étiqueter vos données

Une fois les données d’entraînement chargées sur votre compte Stockage Azure, vous devez les étiqueter pour que votre modèle sache quels mots seront associés aux classes dont vous avez besoin. Quand vous étiquetez des données dans Language Studio (ou quand vous étiquetez manuellement vos données), ces étiquettes sont stockées au [format JSON](../concepts/data-formats.md) que votre modèle utilisera pendant l’entraînement.  

Lorsque vous étiquetez vos données, gardez à l’esprit les points suivants :

* **Étiquetez précisément** : veillez à toujours étiqueter chaque entité avec son type correct. N’incluez que ce que vous souhaitez extraire, et évitez les données inutiles dans votre étiquette.
* **Étiquetez de manière cohérente** : la même entité doit avoir la même étiquette dans tous les fichiers.
* **Étiquetez de manière complète** : étiquetez toutes les instances de l’entité dans tous vos fichiers.

La précision, la cohérence et l’exhaustivité de vos données étiquetées sont des facteurs clés pour les performances du modèle. Pour étiqueter vos données :

1. Accédez à la page de projets dans [Language Studio](https://aka.ms/custom-extraction) et sélectionnez votre projet.

2. Dans le menu de gauche, sélectionnez **Tag data**

3. Vous trouverez à gauche une liste de tous les fichiers `.txt` disponibles dans vos projets. Vous pouvez sélectionner le fichier que vous souhaitez commencer à étiqueter, ou vous pouvez utiliser les boutons **Précédent** et **Suivant** en bas de la page pour naviguer.

4. Pour démarrer l’étiquetage, cliquez sur **Add entities** dans l’angle supérieur droit. Vous pouvez afficher tous les fichiers ou seulement les fichiers étiquetés en modifiant la vue dans le menu déroulant **Viewing**.

>[!TIP]
> * Il n’existe pas de nombre standard d’étiquettes dont vous aurez besoin ; commencez par utiliser 50 étiquettes par entité. Le nombre d’étiquettes dont vous aurez besoin dépend de la différence entre vos entités et de la façon dont elles peuvent être différenciées les unes des autres. Il dépend également de votre étiquetage, qui doit être cohérent et complet.

:::image type="content" source="../media/tagging-screen.png" alt-text="Capture d’écran montrant l’écran Language Studio pour l’étiquetage des données." lightbox="../media/tagging-screen.png":::

Si vous avez activé plusieurs langues pour votre projet, vous trouverez une liste déroulante **Language**, qui vous permet de sélectionner la langue de chaque document.

Lors de l’étiquetage, vos modifications sont synchronisées périodiquement ; si elles n’ont pas encore été enregistrées, un avertissement figure en haut de votre page. Si vous souhaitez enregistrer manuellement, cliquez sur le bouton **Save tags** en haut de la page.

## <a name="tagging-options"></a>Options d’étiquetage

Vous avez deux options pour étiqueter votre document :


|Option |Description  |
|---------|---------|
|Étiqueter à l’aide d’un pinceau     | Sélectionnez l’icône de pinceau en regard d’une entité dans l’angle supérieur droit de l’écran, puis mettez en surbrillance les mots dans le document que vous souhaitez associer à l’entité.           |
|Étiqueter à l’aide d’un menu    | Mettez en surbrillance le mot que vous souhaitez étiqueter en tant qu’entité, et un menu s’affiche. Sélectionnez l’étiquette que vous souhaitez affecter à cette entité.        |

La capture d’écran ci-dessous illustre l’étiquetage à l’aide d’un pinceau.

:::image type="content" source="../media/tag-options.png" alt-text="Capture d’écran montrant les options d’étiquetage proposées dans la NER personnalisée." lightbox="../media/tag-options.png":::

## <a name="remove-tags"></a>Supprimer des étiquettes

Pour supprimer une étiquette

1. Sélectionnez l’entité à partir de laquelle vous souhaitez supprimer une étiquette.
2. Faites défiler le menu qui s’affiche, puis sélectionnez **Remove Tag**.

## <a name="delete-or-rename-entities"></a>Supprimer ou renommer des entités

Pour supprimer ou renommer une entité

1. Sélectionnez l’entité que vous souhaitez modifier dans le coin supérieur droit du menu.
2. Cliquez sur les trois points en regard de l’entité, puis sélectionnez l’option souhaitée dans le menu déroulant.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez étiqueté vos données, vous pouvez commencer l’[entraînement d’un modèle](train-model.md) qui va apprendre à partir de vos données.
