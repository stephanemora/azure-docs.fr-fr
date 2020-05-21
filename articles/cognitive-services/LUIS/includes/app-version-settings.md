---
title: Fichier include
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590982"
---
|Level|Paramètre de l’interface utilisateur|Paramètre d’API|Information|
|--|--|--|--|
|Application|Rendre les points de terminaison publics|`Public`|N’importe qui peut accéder à votre application publique s’il possède une clé de prédiction et connaît votre ID d’application. |
|Version|Utiliser un apprentissage non déterministe|`UseAllTrainingData`|L’entraînement utilise un petit pourcentage d’échantillonnage négatif. Si vous souhaitez utiliser toutes les données au lieu du petit échantillonnage négatif, définissez la valeur `true`. |
|Version|Normaliser les signes diacritiques|`NormalizeDiacritics`|La normalisation des signes diacritiques remplace les caractères possédant des signes diacritiques dans les énoncés par des caractères normaux.|
|Version|Normaliser la ponctuation|`NormalizePunctuation`|La normalisation de la ponctuation signifie qu’avant l’apprentissage de vos modèles et avant la prédiction de vos requêtes de point de terminaison, la ponctuation est retirée des énoncés.|
|Version|Normaliser les formulaires Word|`NormalizeWordForm`|Ignorez les formulaires Word au-delà de la racine.|

Découvrez les [concepts](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) de la normalisation et comment utiliser les API [app](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) et [version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) pour mettre à jour ces paramètres, ou utilisez la section **Gérer** du portail LUIS, dans la page **Paramètres de l’application**.