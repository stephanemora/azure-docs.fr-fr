---
title: Modèles personnalisés – Form Recognizer
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts liés aux modèles personnalisés de l’API Form Recognizer – utilisation et limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 235e868952bb742b082492e2e388170a921c1929
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467961"
---
# <a name="form-recognizer-custom-models"></a>Modèles personnalisés Form Recognizer

Form Recognizer utilise une technologie avancée d’apprentissage automatique pour analyser et extraire les données de vos formulaires et documents. Un modèle Form Recognizer est une représentation des données extraites qui sert de référence pour l’analyse de votre contenu spécifique. Il existe deux types de modèles Form Recognizer :

* **Modèles personnalisés**. Les modèles personnalisés Form Recognizer représentent les données extraites de _formulaires_ spécifiques à votre entreprise. Les modèles personnalisés doivent être formés pour analyser vos données de formulaire distinctes.

* **Modèles prédéfinis**. Form Recognizer prend actuellement en charge les modèles prédéfinis pour les _tickets de caisse, les cartes de visite, les cartes d’identité_ et les _factures_. Les modèles prédéfinis détectent et extraient des informations des images de document et renvoient les données extraites dans une sortie JSON structurée.

## <a name="what-does-a-custom-model-do"></a>Que fait un modèle personnalisé ?

Grâce à Form Recognizer, vous pouvez effectuer l’apprentissage d’un modèle qui extrait des informations de formulaires pertinents pour votre cas d’usage. Vous n’avez besoin que de cinq exemples du même type de formulaire pour commencer. Votre modèle personnalisé peut être formé avec ou sans jeux de données étiquetés.

## <a name="create-use-and-manage-your-custom-model"></a>Créer, utiliser et gérer votre modèle personnalisé

De manière générale, les étapes de création, de formation et d’utilisation de votre modèle personnalisé sont les suivantes :

> [!div class="nextstepaction"]
> [1. Assembler votre jeu de données d’apprentissage](build-training-data-set.md#custom-model-input-requirements)

La création d’un modèle personnalisé commence par l’établissement de votre jeu de données d’apprentissage. Vous aurez besoin d’un minimum de cinq formulaires remplis du même type pour votre exemple de jeu de données. Ils peuvent être de différents types de fichiers et contenir à la fois du texte et de l’écriture manuscrite. Vos formulaires doivent être du même type de document et respecter les [exigences d’entrée](build-training-data-set.md#custom-model-input-requirements) de Form Recognizer.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. Charger vos jeu de données d’apprentissage](build-training-data-set.md#upload-your-training-data)

Vous devez charger votre jeu de données d’apprentissage dans un conteneur de stockage Blob Azure. Si vous ignorez comment créer un compte de stockage Azure avec un conteneur, *consultez* [Démarrage rapide de Stockage Azure pour Portail Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Utilisez le niveau tarifaire Gratuit (F0) pour tester le service, puis effectuez par la suite une mise à niveau vers un niveau payant pour la production.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. Effectuer l’apprentissage de votre modèle personnalisé](quickstarts/client-library.md#train-a-custom-model)

Vous pouvez effectuer l’apprentissage de votre modèle [sans](quickstarts/client-library.md#train-a-model-without-labels) ou [avec](quickstarts/client-library.md#train-a-model-with-labels) des jeux de données étiquetés. Les jeux de données sans étiquette reposent uniquement sur l’API de disposition pour détecter et identifier les informations clés sans intervention humaine supplémentaire. Les jeux de données étiquetés s’appuient également sur l’API de disposition, mais une autre intervention humaine est incluse, comme vos étiquettes spécifiques et l’emplacement des champs. Pour utiliser à la fois des données avec et sans étiquettes, commencez avec au moins cinq formulaires remplis du même type pour les données d’apprentissage avec étiquettes, puis ajoutez des données sans étiquettes au jeu de données requis.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. Analyser les documents avec votre modèle personnalisé](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Testez votre modèle nouvellement formé à l’aide d’un formulaire qui ne fait pas partie du jeu de données d’apprentissage. Vous pouvez poursuivre les formations pour améliorer les performances de votre modèle personnalisé.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. Gérer vos modèles personnalisés](quickstarts/client-library.md#manage-custom-models)

À tout moment, vous pouvez afficher une liste de tous les modèles personnalisés sous votre abonnement, récupérer des informations sur un modèle personnalisé spécifique ou supprimer un modèle personnalisé de votre compte.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez la documentation de **[référence sur l’API Form Recognizer](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** .
>
