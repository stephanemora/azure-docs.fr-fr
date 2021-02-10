---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 5508199212b788e13a27b3c97b8fb7174dc92f32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531261"
---
Nous vous recommandons d’utiliser au moins 30 images par étiquette dans le jeu d’entraînement initial. Vous allez également collecter quelques images supplémentaires pour tester votre modèle une fois qu’il est entraîné.

Pour entraîner votre modèle efficacement, utilisez des images avec une variété de visuels. Sélectionnez des images dont les éléments suivants varient :
* angle de l’appareil photo
* éclairage
* background
* style de visuel
* objets individuels/groupés
* taille
* type

En outre, vérifiez que toutes vos images d’entraînement respectent les critères suivants :
* format .jpg, .png, .bmp ou .gif
* taille ne dépassant pas 6 Mo (4 Mo pour les images de prédiction)
* le côté le plus court ne doit pas comporter moins de 256 pixels ; les images d’une dimension inférieure sont automatiquement mises à l’échelle par le service Vision personnalisée

> [!NOTE]
> Vous avez besoin d’un jeu d’images plus étoffé pour mener à bien votre formation ? Trove, un projet Microsoft Garage, vous permet de collecter et d’acheter des ensembles d’images à des fins d’entraînement. Une fois que vous avez collecté vos images, vous pouvez les télécharger, puis les importer dans votre projet Custom Vision de la manière habituelle. Pour en savoir plus, consultez la [page dédiée à Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3).