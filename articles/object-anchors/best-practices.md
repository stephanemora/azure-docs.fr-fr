---
title: Bonnes pratiques
description: Meilleures pratiques recommandées pour obtenir de meilleurs résultats
author: ariye
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: 119837282409719a194341467a5d8164a46431b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777580"
---
# <a name="best-practices"></a>Meilleures pratiques

Nous vous recommandons d’essayer certaines de ces étapes pour obtenir les meilleurs résultats.

## <a name="conversion"></a>Conversion

- Vérifiez les dimensions de vos objets physiques. Les ancres d’objets Azure fonctionnent de manière optimale pour les objets dont la plus petite dimension est comprise dans la plage de 1 à 10 m recommandée.
- Examinez votre modèle 3D dans un logiciel comme [**MeshLab**](https://www.meshlab.net/) pour obtenir les informations suivantes.
  - Assurez-vous que le modèle 3D comprend un maillage de triangle et que les triangles sur la surface extérieure sont orientés vers l’extérieur. Autrement dit, les sommets doivent être orientés de sorte que les normales suivent la règle de droite dans leur orientation vers l’extérieur.
  - Assurez-vous que le modèle 3D est spécifié avec les unités d’échelle correctes par rapport aux objets physiques. Les unités doivent être parmi les suivantes : ***centimètres, décimètres, pieds, pouces, kilomètres, mètres, millimètres, yards***.
  - Confirmez la direction de gravité nominale qui correspond à l’orientation verticale réelle de l’objet. Si la verticale/gravité vers le bas de l’objet est -Y, utilisez * **(0, -1, 0)** _ ou _*_ (0, 0, -1)_** pour -Z, et de la même façon pour toute autre direction.
  - Assurez-vous que le modèle 3D est codé dans l’un des formats pris en charge : `.glb`, `.gltf`, `.ply`, `.fbx`, `.obj`.
- Notre service de conversion de modèle peut prendre beaucoup de temps pour traiter un modèle avec un niveau de détail important. Pour plus d’efficacité, vous pouvez prétraiter votre modèle 3D pour supprimer les surfaces intérieures.

## <a name="detection"></a>Détection

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- Le Kit de développement logiciel (SDK) d’exécution inclus requiert une zone de recherche fournie par l’utilisateur pour rechercher et détecter le ou les objets physiques. La zone de recherche peut être un cadre englobant, une sphère, un tronc de vue ou une combinaison de ceux-ci. Pour éviter une détection erronée, définissez une zone de recherche suffisamment grande pour couvrir l’objet. Lorsque vous utilisez les exemples d’applications fournis, démarrez l’application en la plaçant à environ 2 mètres de la surface la plus proche.
- Avant de démarrer l’application Azure Object Anchors sur un appareil HoloLens 2, supprimez toutes les Hologrammes à l’aide de l’application **Paramètres**.
  Accédez à **Système** -> **Hologrammes**, puis sélectionnez **Supprimer tous les hologrammes** pour commencer avec un nouveau mappage.

  L’effacement des hologrammes permet aux objets d’être correctement détectés dans leurs positions actuelles au cas où ils ont été déplacés récemment.
- Après avoir supprimé les hologrammes et avant de démarrer l’application, analysez l’objet en marchant autour de lui à une distance de 1 ou 2 mètres plusieurs fois, en portant l’HoloLens.

  L’analyse préalable d’un objet et d’un environnement permet de nettoyer les surfaces résiduelles créées à partir d’objets et d’analyses antérieurs.
  Sinon, l’application peut voir des surfaces fantômes entraînant un alignement incorrect de votre modèle 3D et des hologrammes associés. L’analyse préalable de l’objet peut également réduire considérablement la latence de détection Azure Object Anchors, par exemple de 30 à 5 secondes.
- Pour les objets sombres et hautement réfléchissants, vous devrez peut-être analyser l'objet à une distance plus rapprochée sous plusieurs angles et à plusieurs distances.
- Le Kit de développement logiciel (SDK) d’exécution inclus fournit quelques paramètres permettant aux utilisateurs d’ajuster la détection, comme illustré dans nos exemples d’applications. Les paramètres par défaut fonctionnent correctement pour la plupart des objets. Si vous devez les ajuster pour des objets spécifiques, voici quelques recommandations :
  - Utilisez un seuil de couverture de surface inférieur si l’objet physique est grand, sombre ou brillant.
  - Autorisez un petit changement d’échelle (par exemple, 0,1) pour un grand objet comme une voiture.
  - Autorisez un écart, en degrés, entre la direction verticale locale de l’objet et la gravité lorsque l’objet se trouve sur une pente.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris quelques bonnes pratiques pour obtenir les meilleurs résultats lors de l’utilisation d’Azure Object Anchors pour détecter un objet. Voici quelques articles connexes :

> [!div class="nextstepaction"]
> [Résolution des problèmes de détection d’objets](./troubleshoot/object-detection.md)
