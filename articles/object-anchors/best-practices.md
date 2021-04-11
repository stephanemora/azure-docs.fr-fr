---
title: Bonnes pratiques
description: Meilleures pratiques recommandées pour obtenir de meilleurs résultats
author: ariye
ms.author: crtreasu
ms.date: 03/12/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: 6b9546843d88a5a7329120cca86d685d8abf3460
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061925"
---
# <a name="best-practices"></a>Meilleures pratiques

Nous vous recommandons d’essayer certaines de ces étapes pour obtenir les meilleurs résultats.

## <a name="ingestion"></a>Ingestion

- Vérifiez les dimensions de vos objets physiques. Les ancres d’objets Azure fonctionnent de manière optimale pour les objets dont la plus petite dimension est comprise dans la plage de 1 à 10 m recommandée.
- Examinez votre modèle 3D dans un logiciel comme [**MeshLab**](https://www.meshlab.net/) pour obtenir les informations suivantes.
  - Assurez-vous que le modèle 3D comprend un maillage de triangle et que les triangles sur la surface extérieure sont orientés vers l’extérieur. Autrement dit, les sommets doivent être orientés de sorte que les normales suivent la règle de droite dans leur orientation vers l’extérieur.
  - Assurez-vous que le modèle 3D est spécifié avec les unités d’échelle correctes par rapport aux objets physiques. Les unités doivent être parmi les suivantes : ***centimètres, décimètres, pieds, pouces, kilomètres, mètres, millimètres, yards***.
  - Confirmez la direction de gravité nominale qui correspond à l’orientation verticale réelle de l’objet. Si la verticale/gravité vers le bas de l’objet est -Y, utilisez * **(0, -1, 0)** _ ou _*_ (0, 0, -1)_** pour -Z, et de la même façon pour toute autre direction.
  - Assurez-vous que le modèle 3D est codé dans l’un des formats pris en charge : `.glb`, `.gltf`, `.ply`, `.fbx`, `.obj`.
- Notre service de conversion de modèle peut prendre beaucoup de temps pour traiter un modèle avec un niveau de détail important. Pour plus d’efficacité, vous pouvez prétraiter votre modèle 3D pour supprimer les surfaces intérieures.

## <a name="detection"></a>Détection

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- Le Kit de développement logiciel (SDK) d’exécution inclus requiert une zone de recherche fournie par l’utilisateur pour rechercher et détecter le ou les objets physiques. La zone de recherche peut être un cadre englobant, une sphère, un tronc de vue ou une combinaison de ceux-ci. Pour éviter une détection erronée, il est préférable de définir une zone de recherche suffisamment grande pour couvrir l’objet. Lorsque vous utilisez les exemples d’applications fournis, vous pouvez vous baser sur un côté de l’objet à environ 2 mètres de la surface la plus proche et démarrer l’application.
- Avant de démarrer l’application Azure Object Anchors sur un appareil HoloLens 2, supprimez les hologrammes à proximité de votre lieu de travail en passant par les paramètres principaux de vos appareils (***Paramètres -> Système -> Hologrammes***).

  Cette étape garantit que, si un nouvel objet comme une voiture est présent dans le même espace que celui occupé précédemment par un autre, ou si l’objet a été déplacé à partir de l’espace cible, les anciens hologrammes non pertinents ne sont pas conservés et créent une visualisation confuse de l’objet actuellement affiché.
- Après avoir supprimé les hologrammes et avant de démarrer l’application, analysez l’objet, une voiture par exemple, en observant l’objet tout en déplaçant l’appareil d’environ 1 à 2 mètres et en faisant lentement le tour de l’objet une ou deux fois.

  Cette étape garantit que toutes les estimations de surface résiduelles créées dans votre espace par des objets et des analyses antérieurs sont actualisées avec les surfaces de l’objet cible actuel que vous allez utiliser. Sinon, l’application peut voir des surfaces fantômes doubles entraînant un alignement incorrect de votre modèle 3D et des hologrammes associés. L’analyse préalable de l’objet réduit également considérablement la latence de détection Azure Object Anchors, par exemple de 30 à 5 secondes.
- Pour les objets sombres et hautement réfléchissants, vous devrez peut-être analyser l’objet dans une plage plus rapprochée et en déplaçant votre tête vers le haut, le bas, et vers la gauche et la droite pour que l’appareil puisse voir les surfaces sous plusieurs angles et à plusieurs distances.
- Si vous constatez une détection d’objet incorrecte, comme une orientation retournée ou une pose incorrecte telle qu’un modèle incliné, vous devez visualiser le mappage spatial. Les résultats incorrects sont souvent dus à une reconstruction de surface médiocre ou incomplète. Vous pouvez supprimer les hologrammes, analyser l’objet et réexécuter la détection d’objet sur l’application.
- Le Kit de développement logiciel (SDK) d’exécution inclus fournit quelques paramètres permettant aux utilisateurs d’ajuster la détection, comme illustré dans nos exemples d’applications. Les paramètres par défaut fonctionnent correctement pour la plupart des objets. Si vous devez les ajuster pour des objets spécifiques, voici quelques recommandations :
  - Utilisez un seuil de couverture de surface inférieur si l’objet physique est grand, sombre ou brillant.
  - Autorisez un petit changement d’échelle (par exemple, 0,1) pour un grand objet comme une voiture.
  - Autorisez un écart, en degrés, entre la direction verticale locale de l’objet et la gravité lorsque l’objet se trouve sur une pente.
