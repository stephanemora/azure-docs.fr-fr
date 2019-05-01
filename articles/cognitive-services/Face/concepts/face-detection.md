---
title: Détection des visages et les concepts d’attributs
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts relatifs à la détection des visages et les attributs de visage.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572553"
---
# <a name="face-detection-and-attributes"></a>Détection des visages et les attributs

Cet article explique les concepts de détection de visages et les données d’attribut de visage. Détection de visage est l’action de localisation des visages humains dans une image, éventuellement retourner une variété de données relatives à la face.

Vous utilisez le [doivent faire Face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) opération pour détecter les visages dans une image. Au minimum, chaque détectée face aux correspond à un **faceRectangle** champ dans la réponse. Il s’agit d’un ensemble de coordonnées en pixels (gauche, haut, largeur, hauteur) marquant la trouve face. À l’aide de ces coordonnées, vous pouvez obtenir l’emplacement de la face, ainsi que sa taille. Dans la réponse de l’API, les faces sont répertoriés par ordre de taille du plus grand au plus petit.

## <a name="face-id"></a>ID de visage

L’ID de visage est simplement une chaîne d’identificateur unique pour chaque face détectée dans une image. Vous pouvez demander un ID de visage dans votre [doivent faire Face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) appel d’API.

## <a name="face-landmarks"></a>Points de repère du visage

Points de repère face sont un ensemble de points facile à trouver sur un visage telles que les élèves ou de l’extrémité du nez. Il existe 27 points de repères prédéfinis par défaut. La figure suivante montre tous les points de 27 :

![Un diagramme de face avec tous les points de 27 repère étiqueté](../Images/landmarks.1.jpg)

Les coordonnées des points sont retournées en unités de pixels.

## <a name="attributes"></a>Attributs

Les attributs sont un ensemble de fonctionnalités face supplémentaire qui peut éventuellement être détectés par le [doivent faire Face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Les attributs qui peuvent être détectés sont les suivantes :

* **Âge** l’âge estimé, en années, d’un type particulier.
* **Flou** le flou de l’image dans l’image. Elle retourne une valeur entre zéro et un, ainsi qu’une évaluation informelle (« faible », « medium », « élevé »).
* **Exposition** l’exposition de la face de l’image. Elle retourne une valeur entre zéro et un, ainsi qu’une évaluation informelle (« sous-exposition », « goodExposure », « surexposition »).
* **Émotion** une liste des émotions avec leur confiance de détection pour le visage donné. Scores de confiance sont normalisées : les scores sur tous les émotions ajoutera au maximum un. Les émotions retournées sont bonheur, tristesse, neutre, la colère, mépris, le dégoût, surprise et peur.
* **PILOSITÉ faciale** la présence de PILOSITÉ faciale estimée et la longueur de la face donnée.
* **Sexe** sexe estimé de la face donné. Les valeurs possibles sont « masculin », « féminin » et « genderless ».
* **LUNETTES** indique si le type de police donné possède en forme de lunettes. Les valeurs possibles sont « NoGlasses », « ReadingGlasses », « Lunettes de soleil » et « Swimming lunettes ».
* **Cheveux** le style PILOSITÉ de la face. Elle indique si les cheveux est visible, si CALVITIE est détectée, et les couleurs cheveux sont détectés.
* **Head poser** l’orientation de la face dans l’espace 3D. Cette opération est décrite par la tonalité, restaurer et lacet angles en degrés. Les plages de valeurs sont [-90, 90], [-180, 180] et [-90, 90] degrés respectivement. Consultez le diagramme suivant pour les mappages d’angle :

    ![Une tête avec la tonalité, restaurer et lacet axes étiquetés](../Images/headpose.1.jpg)
* **Composition** indique si la police a composition. Cela retourne une valeur booléenne pour « eyeMakeup » et « lipMakeup ».
* **Bruit** le bruit visuel détecté dans l’image du visage. Elle retourne une valeur entre zéro et un, ainsi qu’une évaluation informelle (« faible », « medium », « élevé »).
* **OCCLUSION** s’il existe des parties de la face des objets. Cela retourne une valeur booléenne pour « eyeOccluded », « foreheadOccluded » et « mouthOccluded ».
* **Sourire** l’expression de sourire du visage donné. Il s’agit d’une valeur comprise entre zéro (aucun sourire) et l’autre (sourire clair).

> [!IMPORTANT]
> Attributs de visage prévues via l’utilisation d’algorithmes statistiques et peuvent toujours être inexacte. Soyez prudent lors de l’établissement des décisions basées sur les données d’attribut.

## <a name="input-data"></a>Données d’entrée

Utilisez les conseils suivants pour vous assurer de que vos images d’entrée donnent les résultats plus précis de la détection :

* Les formats d’image d’entrée pris en charge sont JPEG, PNG, GIF (la première image), BMP.
* Taille du fichier image doit être non supérieure à 4 Mo.
* Les visages sont détectables selon une plage de tailles allant de 36 x 36 à 4 096 x 4 096 pixels. Visages en dehors de cette plage ne sera pas détectés.
* Certains visages peuvent ne pas être détectés en raison de défis techniques. Angles de face extrêmes (pose principal) ou face occlusion (objets tels que lunettes de soleil ou bloque la partie de la face des mains) peut affecter la détection. Faces frontales et quasi-frontales fournissent de meilleurs résultats.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les concepts de détection de visage, apprenez à écrire un script simple qui détecte les visages dans une image donnée.

* [Comment détecter des visages dans une image](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)