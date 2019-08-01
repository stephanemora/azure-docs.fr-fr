---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423599"
---
Nous vous recommandons d’utiliser au moins 30 images par étiquette dans le jeu d’entraînement initial. Vous allez également collecter quelques images supplémentaires pour tester votre modèle une fois qu’il est entraîné.

Pour entraîner votre modèle efficacement, utilisez des images avec une variété de visuels. Sélectionnez des images dont les éléments suivants varient :
* angle de l’appareil photo
* éclairage
* background
* style de visuel
* objets individuels/groupés
* size
* Type

En outre, vérifiez que toutes vos images d’entraînement respectent les critères suivants :
* format .jpg, .png ou .bmp
* taille ne dépassant pas 6 Mo (4 Mo pour les images de prédiction)
* le côté le plus court ne doit pas comporter moins de 256 pixels ; les images d’une dimension inférieure sont automatiquement mises à l’échelle par le service Vision personnalisée