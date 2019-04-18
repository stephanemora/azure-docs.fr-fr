---
title: Comment migrer votre projet à 3.0 API
titlesuffix: Azure Cognitive Services
description: Découvrez comment migrer des projets de Vision personnalisée à partir de la version précédente de l’API 3.0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044004"
---
# <a name="migrate-to-the-30-api"></a>Migrer vers le 3.0 API

Vision personnalisée a atteint maintenant la disponibilité générale et a subi une mise à jour de l’API.
Cette mise à jour inclut plusieurs nouvelles fonctionnalités et, plus important encore, quelques modifications avec rupture :

* L’API de prédiction est désormais divisée en deux en fonction du type de projet.
* L’option d’exportation de Kit de développement IA Vision (VAIDK) nécessite la création d’un projet de manière spécifique.
* Par défaut itérations ont été supprimées en faveur d’une publication / annuler la publication d’une itération nommée.

Ce guide vous explique comment mettre à jour vos projets pour travailler avec la nouvelle version d’API. Consultez le [notes de publication](release-notes.md) pour obtenir la liste complète des modifications.

## <a name="use-the-updated-prediction-api"></a>Utiliser l’API de prédiction mis à jour

L’API 2.x utilisé le même appel de prédiction pour les classifieurs de l’image et les projets de détecteur d’objet. Les deux types de projets ont été acceptables pour le **PredictImage** et **PredictImageUrl** appels. À compter de 3.0, nous avons divisé cette API afin que vous devez faire correspondre le type de projet à l’appel :

* Utilisez **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** et **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** pour obtenir des prédictions pour les projets de classification d’image.
* Utilisez **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** et **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** pour obtenir des prédictions pour les projets de détection d’objet.

## <a name="use-the-new-iteration-publishing-workflow"></a>Utiliser le nouveau workflow de publication d’itération

L’API 2.x permet l’itération par défaut ou un ID d’itération spécifié choisissez l’itération à utiliser pour la prédiction. À compter de 3.0, nous avons adopté un flux de publication dans laquelle vous publiez tout d’abord une itération sous un nom spécifié à partir de l’API de formation. Ensuite, vous passez le nom pour les méthodes de prédiction pour spécifier quelle itération à utiliser.

> [!IMPORTANT]
> Le 3.0 API n’utilisent pas la fonctionnalité d’itération par défaut. Jusqu'à ce que nous déprécier les anciennes API, vous pouvez continuer à utiliser l’API 2.x pour activer/désactiver une itération par défaut. Ces API est conservés pendant une période de temps, et vous pouvez appeler la **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** méthode pour marquer une itération comme valeur par défaut.

### <a name="publish-an-iteration"></a>Publier une itération

Une fois une itération est formée, vous pouvez la rendre disponible pour la prédiction à l’aide de la **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** (méthode). Pour publier une itération, vous aurez besoin de l’ID de ressource de prédiction, qui est disponible sur la page des paramètres du site Web CustomVision.

![La page de paramètres de site Web de Vision personnalisée avec l’ID de ressource de prédiction décrite.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Vous pouvez également obtenir ces informations à partir de la [Azure Portal](https://portal.azure.com) en accédant à la ressource de prédiction de Vision personnalisée et en sélectionnant **propriétés**.

Une fois votre itération est publiée, applications peuvent l’utiliser pour la prédiction en spécifiant le nom dans son appel d’API de prédiction. Pour effectuer une itération non disponible pour les appels de prédiction, utilisez le **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>Options d’exportation supplémentaires

Avec la version 3.0 API que nous allons exposer deux autres exporter cibles : Architecture d’ARM et Kit de développement d’intelligence artificielle Vision.

* Pour utiliser ARM, vous devez simplement choisir un domaine Compact, puis choisissez le fichier DockerFile et puis ARM en tant que les options d’exportation.
* Pour le Kit de développement IA Vision, le projet doit être créé avec le __général (Compact)__ domaine, ainsi que la spécification VAIDK dans la cible exporter l’argument de plateformes.

## <a name="next-steps"></a>Étapes suivantes

* [Formation de documentation de référence des API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Documentation de référence des API de prédiction (REST)](https://go.microsoft.com/fwlink/?linkid=865445)