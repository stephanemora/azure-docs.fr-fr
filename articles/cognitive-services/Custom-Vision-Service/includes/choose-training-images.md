---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130080"
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
