---
title: Résolution des problèmes de détection d’objets
description: Résolution des problèmes de détection d’un objet à l’aide d’Azure Object Anchors.
author: craigktreasure
manager: rgarcia
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: b70babbf3f105903cc7b14d175908b972bde9158
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601992"
---
# <a name="troubleshooting-object-detection"></a>Résolution des problèmes de détection d’objets

Cet article suppose que vous avez déjà converti un modèle 3D en un modèle de détection Azure Object Anchors et que vous avez correctement chargé ce dernier dans une application.

## <a name="troubleshooting-steps"></a>Étapes de dépannage

* Pour une expérience optimale, assurez-vous que la taille du modèle que vous détectez est prise en charge (1 à 10 mètres).
* Assurez-vous que la pièce dispose de suffisamment de textures en ajoutant quelques affiches.
* Supprimez les hologrammes actuels pour réinitialiser la carte comme décrit [ci-dessous](#remove-holograms-to-reset-the-map).
* Scannez davantage l’objet.
* Définissez un cadre étroit comme zone de recherche incluant tout ou partie de l’objet.
* Effacez le cache de cartographie spatiale et scannez à nouveau l’objet.
* Assurez-vous que la direction de la gravité et la dimension des ressources ont été correctement utilisées lors de la conversion du modèle, comme décrit [ci-dessous](#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct).
* Inspectez visuellement le modèle de détection comme décrit [ci-dessous](#visually-inspect-the-detection-models-mesh).
* Ajustez les valeurs de requêtes du modèle comme décrit [ci-dessous](#adjust-object-query-values).
* Capturez les diagnostics comme décrit [ci-dessous](#capture-diagnostics).

### <a name="remove-holograms-to-reset-the-map"></a>Supprimer les hologrammes pour réinitialiser la carte

Si les objets détectés présentent l’un des problèmes suivants, la suppression et la réinitialisation de la carte peuvent résoudre le problème :
* Orientation inversée
* Pose incorrecte
* Modèle incliné

Pour supprimer les hologrammes et réinitialiser la carte, ouvrez l’application **Paramètres** et accédez à **Système** -> **Hologrammes**. Ensuite, sélectionnez **Supprimer tous les hologrammes** pour commencer avec une nouvelle carte.

L’effacement des hologrammes permet aux objets d’être correctement détectés dans leurs positions actuelles au cas où ils ont été déplacés récemment.

Scannez de nouveau votre environnement en vous y déplaçant en portant l’appareil HoloLens. Marchez plusieurs fois autour des objets que vous souhaitez détecter, à une distance d’un ou deux mètres.

### <a name="ensure-the-gravity-direction-and-asset-dimension-unit-are-correct"></a>Vérifier que la direction de la gravité et l’unité de dimension des ressources sont correctes

Lorsque vous soumettez un modèle 3D à convertir à l’aide du Kit de développement logiciel (SDK) de conversion Object Anchors (voir [ici](../quickstarts/get-started-model-conversion.md)), vous devez entrer la direction de la gravité (`Gravity`) et l’unité de mesure (`AssetDimensionUnit`) correctes pour votre modèle 3D. Si ces valeurs ne sont pas correctes, il est peu probable qu’Object Anchors détecte correctement votre objet.

La direction de la gravité est le vecteur descendant qui pointe vers la terre. Pour les modèles CAO, la direction de la gravité est généralement l’opposé d’une direction vers le haut. Par exemple, dans de nombreux cas, +Z signifie « vers le haut », auquel cas -Z ou `Vector3(0.0, 0.0, -1.0)` représente la direction de la gravité. Lorsque vous déterminez la gravité, vous devez également tenir compte de l’orientation dans laquelle le modèle sera vu pendant l’exécution. Si vous essayez de détecter une chaise dans le monde réel sur une surface plane, la gravité peut être `Vector3(0.0, 0.0, -1.0)`. Mais si la chaise se trouve sur une pente à 45 degrés, la gravité peut être `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)`.

La direction de la gravité peut être déterminée à l’aide d’un outil de rendu 3D, par exemple [MeshLab](http://www.meshlab.net/).

L’unité de mesure représente l’échelle du modèle. Les unités prises en charge peuvent être obtenues l’aide de l’énumération **Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit**.

Vous pouvez également suivre les instructions [ici](../visualize-converted-model.md) pour visualiser un modèle de détection dans Unity afin d’inspecter visuellement que la direction de la gravité et l’échelle semblent correctes.

### <a name="visually-inspect-the-detection-models-mesh"></a>Inspecter visuellement le maillage du modèle de détection

Parfois, il peut être utile d’inspecter visuellement le maillage du modèle de détection afin de repérer tout problème d’orientation, d’échelle ou de fonctionnalité. Suivez les instructions données [ici](../visualize-converted-model.md) pour visualiser un modèle converti dans Unity.

### <a name="adjust-object-query-values"></a>Ajuster les valeurs des requêtes d’objet

* Définissez des zones de recherche étroites pour mieux couvrir l’objet complet afin d’améliorer la vitesse et la précision de la détection.
* La valeur par défaut `ObjectQuery.MinSurfaceCoverage` est souvent suffisante, mais vous pouvez utiliser une valeur plus petite pour obtenir une détection plus rapide.
* Utilisez une petite valeur pour `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` si l’objet est censé être à la verticale.
* Une application doit toujours utiliser un modèle d’objet `1:1` pour la détection. Idéalement, l’échelle estimée doit être proche de 1, avec une erreur inférieure à 1 %. Une application peut avoir la valeur `ObjectQuery.MaxScaleChange` à `0` ou `0.1` pour désactiver ou activer l’estimation de l’échelle, et évaluer qualitativement la pose de l’instance.
* Pour plus d’informations, consultez [Guide pratique pour détecter un objet difficile](../detect-difficult-object.md).

### <a name="capture-diagnostics"></a>Capturer les diagnostics

L’application peut capturer et enregistrer des archives de diagnostics à l’aide de l’objet [ObjectDiagnosticsSession](../concepts/sdk-overview.md#objectdiagnosticssession).

L’**exemple d’application Unity avec MRTK** écrit les diagnostics dans le dossier [TempState](../quickstarts/get-started-unity-hololens-mrtk.md). Vous pouvez démarrer une session de diagnostic en ouvrant le <a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">menu manuel</a>, en sélectionnant **Démarrer le suivi**, en reproduisant une tentative de détection, puis en sélectionnant **Arrêter le suivi** pour enregistrer l’archive de diagnostic. Vous pouvez ensuite utiliser le [portail d’appareil Windows](/windows/mixed-reality/develop/platform-capabilities-and-apis/using-the-windows-device-portal) pour récupérer l’archive de diagnostic dans le dossier **TempState** de l’application.

L’archive de diagnostic peut ensuite être partagée avec nous pour nous permettre de déboguer le problème.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de résolution des problèmes, vous avez appris à résoudre les problèmes de détection d’un objet physique à l’aide d’Azure Object Anchors.
Voici quelques articles connexes :

> [!div class="nextstepaction"]
> [Procédure de détection d’un objet difficile](../detect-difficult-object.md)

> [!div class="nextstepaction"]
> [Guide pratique pour visualiser un modèle Object Anchors](../visualize-converted-model.md)
