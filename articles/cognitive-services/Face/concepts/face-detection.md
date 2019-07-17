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
ms.openlocfilehash: e61048eeab9d7061c18f3237db22fc87ca52f526
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891137"
---
# <a name="face-detection-and-attributes"></a>Détection des visages et les attributs

Cet article explique les concepts de détection de visages et les données d’attribut de visage. Détection de visage est l’action de localisation des visages humains dans une image, si vous le souhaitez retourner différents types de données liées à la face.

Vous utilisez le [doivent faire Face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) opération pour détecter les visages dans une image. Au minimum, chaque face détectée correspond à un champ faceRectangle dans la réponse. Ce jeu de coordonnées de pixel de gauche, haut, largeur et de hauteur marquer la trouve face. À l’aide de ces coordonnées, vous pouvez obtenir l’emplacement de la police et sa taille. Dans la réponse de l’API, les faces sont répertoriés par ordre de taille du plus grand au plus petit.

## <a name="face-id"></a>ID du visage

L’ID du visage est une chaîne d’identificateur unique pour chaque face détectée dans une image. Vous pouvez demander un ID de visage dans votre appel d’API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-landmarks"></a>Points de repère du visage

Points de repère face sont un ensemble de points facile à trouver sur une face, telles que les élèves ou de l’extrémité du nez. Il existe 27 points de repères prédéfinis par défaut. La figure suivante montre tous les points de 27 :

![Un diagramme de face avec tous les points de 27 repère étiqueté](../Images/landmarks.1.jpg)

Les coordonnées des points sont retournées en unités de pixels.

## <a name="attributes"></a>Attributs

Les attributs sont un ensemble de fonctionnalités qui peuvent éventuellement être détectés par le [doivent faire Face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Les attributs suivants peuvent être détectés :

* **Âge**. L’âge estimé dans les années d’un type particulier.
* **Flou**. Le flou de l’image dans l’image. Cet attribut retourne une valeur comprise entre zéro et un et d’évaluation informelle de faible, moyen ou élevé.
* **Émotion**. Liste des émotions avec leur confiance de détection pour le visage donné. Scores de confiance sont normalisées, et les scores sur tous les émotions ajouter au maximum un. Les émotions retournées sont bonheur, tristesse, neutre, la colère, mépris, le dégoût, surprise et peur.
* **Exposition**. L’exposition de la face de l’image. Cet attribut retourne une valeur comprise entre zéro et un et d’évaluation informelle de sous-exposition, goodExposure ou surexposition.
* **PILOSITÉ faciale**. La présence de PILOSITÉ faciale estimée et la longueur de l’image donnée.
* **Sexe**. Sexe estimé de la face donné. Les valeurs possibles sont male et female genderless.
* **LUNETTES**. Indique si le type de police donné possède en forme de lunettes. Les valeurs possibles sont NoGlasses ReadingGlasses, lunettes de soleil et lunettes de natation.
* **Cheveux**. Type de la face cheveux. Cet attribut indique si les cheveux est visible, si CALVITIE est détectée, et les couleurs cheveux sont détectés.
* **Positionnement de la tête**. Orientation du visage dans l’espace 3D. Cet attribut est défini par des angles de roulis, de tangage et de lacet, mesurés en degrés. Les plages de valeurs vont de -90 degrés à 90 degrés, - 180 degrés à 180 degrés, et -90 degrés à 90 degrés, respectivement. Consultez le diagramme suivant pour les mappages des angles :

    ![Une tête avec la tonalité, restaurer et lacet axes étiquetés](../Images/headpose.1.jpg)
* **Composition**. Si la face dispose de composition. Cet attribut retourne une valeur booléenne pour eyeMakeup et lipMakeup.
* **Bruit**. Le bruit visual détecté dans l’image du visage. Cet attribut retourne une valeur comprise entre zéro et un et d’évaluation informelle de faible, moyen ou élevé.
* **Occlusion**. S’il existe des parties de blocage de la face des objets. Cet attribut retourne une valeur booléenne pour eyeOccluded, foreheadOccluded et mouthOccluded.
* **Sourire**. L’expression de sourire du visage donné. Cette valeur est comprise entre 0 pour aucun sourire et l’autre pour un sourire clair.

> [!IMPORTANT]
> Attributs de visage prévues via l’utilisation d’algorithmes statistiques. Ils peuvent pas toujours précises. Soyez prudent lorsque vous prenez des décisions en fonction des données d’attribut.

## <a name="input-data"></a>Données d’entrée

Utilisez les conseils suivants pour vous assurer que vos images d’entrée donnent les résultats plus précis de la détection :

* Les formats d’image d’entrée pris en charge sont JPEG, PNG, GIF pour la première image et BMP.
* La taille du fichier image doit être non supérieure à 4 Mo.
* La plage des tailles de visages détectables s’étend de 36 x 36 à 4096 x 4096 pixels. Visages en dehors de cette plage ne sera pas détectés.
* Certains visages peuvent ne pas être détectés en raison de défis techniques. Angles de face extrêmes (pose principal) ou face occlusion (objets tels que lunettes de soleil ou mains qui bloquent la partie de la face) peut affecter la détection. Faces frontales et quasi-frontales fournissent de meilleurs résultats.

Si vous êtes détectant les visages à partir d’un flux vidéo, vous pourrez peut-être améliorer les performances en ajustant certains paramètres de votre caméra vidéo :

* **Lissage**: Les caméras vidéo nombreux appliquer un effet de lissage. Vous devez désactiver cette option si vous le pouvez, car elle crée un effet de flou entre les images et réduit de plus de clarté.
* **Vitesse d’obturation**: Une vitesse plus rapide d’obturation réduit la quantité de mouvement entre les images et rend chaque image plus claire. Nous vous recommandons les vitesses d’obturation de 1/60 secondes ou plus rapide.
* **Angle d’obturation**: Certains appareils photo spécifier l’angle d’obturateur au lieu de la vitesse d’obturation. Vous devez utiliser un angle d’obturateur inférieure si possible. Ainsi, les images vidéo plus claires.

    >[!NOTE]
    > Une caméra avec un angle d’obturateur inférieure recevra le moins de lumière dans chaque image, l’image est donc plus sombre. Vous devrez déterminer le niveau approprié à utiliser.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les concepts de détection de visage, découvrez comment écrire un script qui détecte les visages dans une image donnée.

* [Détectez les visages dans une image](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
