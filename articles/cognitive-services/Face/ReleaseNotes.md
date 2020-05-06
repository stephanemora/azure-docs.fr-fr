---
title: Notes de publication – Service Visage
titleSuffix: Azure Cognitive Services
description: Les notes de publication du service Visage comportent un historique des modifications apportées aux différentes versions.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "76165868"
---
# <a name="face-release-notes"></a>Notes de publication de Visage

Cet article fait référence à la version 1.0 du service Visage.

### <a name="release-changes-in-june-2019"></a>Modifications apportées à la version de juin 2019

* Ajout d’un nouveau modèle de détection des visages avec une meilleure précision sur les visages de petite taille, de profil, masqués et flous. Utilisez-le par le biais de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) et [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) en spécifiant le nom du nouveau modèle de détection des visages `detection_02` dans le paramètre `detectionModel`. Vous trouverez des détails supplémentaires dans [Guide pratique pour spécifier un modèle de détection](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Modifications apportées à la version d’avril 2019

* Amélioration de la précision globale des attributs `age` et `headPose`. L’attribut `headPose` a également été mis à jour ; la valeur `pitch` est désormais activée. Utilisez ces attributs en les spécifiant dans le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) paramètre `returnFaceAttributes`. 

* Amélioration de la rapidité de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) et [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Modifications apportées à la version de mars 2019

* Ajout d’un nouveau modèle de reconnaissance faciale avec une meilleure précision. Utilisez-le par le biais de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) et [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) en spécifiant le nom du nouveau modèle de reconnaissance faciale `recognition_02` dans le paramètre `recognitionModel`. Vous trouverez des détails supplémentaires dans [Guide pratique pour spécifier un modèle de reconnaissance](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Modifications apportées à la version de janvier 2019

* Ajout de la fonctionnalité de capture instantanée pour prendre en charge la migration des données entre des abonnements : [Capture instantanée](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Vous trouverez des détails supplémentaires dans [Migrer vos données de visage vers un autre abonnement API Visage](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Modifications apportées à la version d’octobre 2018

* Description détaillée de `status`, `createdDateTime`, `lastActionDateTime` et `lastSuccessfulTrainingDateTime` dans [PersonGroup - Obtenir l’état de l’entrainement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Obtenir l’état de l’entrainement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) et [ LargeFaceList - Obtenir l’état de l’entrainement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Modifications apportées à la version de mai 2018

* Nous avons considérablement amélioré l’attribut `gender` ainsi que nous avons amélioré les attributs `age`, `glasses`, `facialHair`, `hair` et `makeup`. Utilisez-les via le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

* Nous avons augmenté la taille maximale des fichiers d’image d’entrée la faisant passer de 4 à 6 Mo dans [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) et [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Modifications apportées à la version de mars 2018

* Ajout d’un conteneur à l’échelle du million : [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) et [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Vous trouverez davantage d’informations dans la section [Comment utiliser la fonctionnalité à grande échelle](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Nous avons fait passer la plage de valeurs du paramètre `maxNumOfCandidatesReturned` de [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) de [1, 5] à [1, 100], sa valeur par défaut étant 10.

### <a name="release-changes-in-may-2017"></a>Modifications apportées à la version de mai 2017

* Nous avons ajouté les attributs `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` et `noise` dans le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

* Les paramètres PersonGroup et [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) acceptent désormais 10 000 personnes.

* Nous prenons en charge la pagination dans [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) avec comme paramètres facultatifs `start` et `top`.

* Nous prenons en charge l’accès concurrentiel pour ajouter et supprimer des visages concernant les listes FaceLists et les personnes dans PersonGroup.

### <a name="release-changes-in-march-2017"></a>Modifications apportées à la version de mars 2017
* Nous avons ajouté l’attribut `emotion` dans le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

* Nous avons résolu le problème qui entraînait une absence de détection et le renvoi d’un rectangle depuis [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en tant que `targetFace` dans [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) et [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Nous avons résolu le problème affectant la taille du visage détectable pour garantir qu’elle se trouvait bien entre 36 x 36 et 4 096 x 4096 pixels.

### <a name="release-changes-in-november-2016"></a>Modifications apportées à la version de novembre 2016
* Nous avons ajouté un abonnement Stockage Visage standard qui permet de stocker des visages persistants supplémentaires lorsque vous utilisez [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) pour l’identification ou la mise en correspondance des similitudes. Les images stockées sont facturées au tarif de 0,5 $ tous les 1 000 visages (tarif journalier au prorata). Les abonnements de niveau gratuit sont toujours limités à un total de 1 000 personnes.

### <a name="release-changes-in-october-2016"></a>Modifications apportées à la version d’octobre 2016
* Nous avons modifié le message d’erreur lorsque plusieurs visages apparaissent dans targetFace. Auparavant, il indiquait « There are more than one face in the image ». Maintenant, il indique « There is more than one face in the image » dans [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) et [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Modifications apportées à la version de juillet 2016
* Nous prenons en charge l’authentification d’objet Face to Person dans [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Nous avons ajouté un paramètre `mode` facultatif qui permet de sélectionner deux modes de fonctionnement : `matchPerson` et `matchFace` dans [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), le paramètre par défaut étant `matchPerson`.

* Nous avons ajouté un paramètre `confidenceThreshold` facultatif qui permet à l’utilisateur de définir le seuil indiquant si un visage appartient à l’objet Person dans [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Nous avons ajouté les paramètres `start` et `top` facultatifs dans [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) pour permettre à l’utilisateur d’indiquer le point de départ et le nombre total de PersonGroups dans la liste.

### <a name="v10-changes-from-v0"></a>Changements apportés entre la version 0 et la version 1.0
* Nous avons mis à jour le point de terminaison de la racine du service le faisant passer de ```https://westus.api.cognitive.microsoft.com/face/v0/``` à ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Nous avons apporté des modifications à : [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) et [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Nous avons mis à jour la taille minimale pour la détection des visages la faisant passer à 36 x 36 pixels. Les visages dont la taille est inférieure à 36 x 36 pixels ne seront pas détectés.

* Nous avons abandonné les données PersonGroup et Person qui étaient disponibles dans la version 0 de Visage. Ces données ne sont pas accessibles dans la version 1.0 de Visage.

* Nous avons abandonné le point de terminaison de la version 0 de l’API Visage au 30 juin 2016.
