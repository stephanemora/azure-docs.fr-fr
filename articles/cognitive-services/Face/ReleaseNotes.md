---
title: Nouveautés du service Visage
titleSuffix: Azure Cognitive Services
description: Les notes de publication du service Visage comportent un historique des modifications apportées aux différentes versions.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: a66f65d48a14853ec1da08f5d83b777f4fdac846
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183315"
---
# <a name="whats-new-in-face-service"></a>Nouveautés du service Visage

Le service Visage Azure est mis à jour régulièrement. Lisez cet article pour vous tenir informé des améliorations de fonctionnalités, des correctifs et des mises à jour de la documentation.

## <a name="february-2021"></a>Février 2021

* Nouveau modèle de détection d’API Visage : le nouveau modèle detection 03 est le modèle de détection le plus précis actuellement disponible. Si vous êtes un nouveau client, nous vous recommandons d’utiliser ce modèle. Le modèle detection 03 améliore à la fois le rappel et la précision des visages plus petits présents sur des images (64 x 64 pixels). Les améliorations supplémentaires incluent une réduction globale des faux positifs et une meilleure détection des orientations de visages pivotés. La combinaison du modèle detection 03 avec le nouveau modèle recognition 04 permettra également d’améliorer la précision de la reconnaissance. Pour plus d’informations, consultez [Spécifier un modèle de détection des visages](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model) .
* Attribut de masque de visage : l’attribut de masque de visage est disponible dans le dernier modèle detection 03, avec l’attribut supplémentaire `"noseAndMouthCovered"` qui détecte si le masque de visage est porté comme prévu, couvrant à la fois le nez et la bouche. Pour utiliser la fonctionnalité de détection de masque la plus récente, les utilisateurs doivent spécifier le modèle de détection dans la demande d’API : affectez la version du modèle avec le paramètre _detectionModel_ à `detection_03`. Pour plus d’informations, consultez [Spécifier un modèle de détection des visages](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-detection-model) .
* Nouveau modèle de reconnaissance de l’API Visage : Le nouveau modèle recognition 04 est le modèle de reconnaissance le plus précis actuellement disponible. Si vous êtes un nouveau client, nous vous recommandons d’utiliser ce modèle à des fins de vérification et d’identification. Il améliore la précision du modèle recognition 03, y compris une meilleure reconnaissance pour les utilisateurs inscrits et qui portent un masque (masques chirurgicaux, masques N95, masques en tissu). Les clients peuvent désormais créer des expériences utilisateur sécurisées et transparentes pour détecter si un utilisateur inscrit porte un masque à l’aide du dernier modèle detection 03, puis reconnaître les personnes avec le dernier modèle recognition 04. Pour plus d’informations, consultez [Spécifier un modèle de reconnaissance faciale](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/specify-recognition-model).


## <a name="january-2021"></a>Janvier 2021
* Limiter la latence lors de l’utilisation de l’API Visage : L’équipe Visage a publié un nouvel article détaillant les causes potentielles de la latence lors de l’utilisation du service, et les stratégies d’atténuation possibles. Voir [Limiter la latence lors de l’utilisation du service Visage](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Décembre 2020
* Configuration du client pour le stockage d’ID de visage : Bien que le service Visage ne stocke pas les images des clients, les caractéristiques de visage extraites seront stockées sur le serveur. L’ID de visage est un identificateur de la caractéristique de visage et sera utilisé dans les options [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) et [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Les caractéristiques de visage stockées expirent et seront supprimées 24 heures après l’appel de détection d’origine. Les clients peuvent maintenant déterminer la durée de mise en cache de ces ID de visage. La valeur maximale est toujours de 24 heures, mais une valeur minimale de 60 secondes peut maintenant être définie. Les nouvelles plages de temps pour les ID de visage mis en cache sont comprises entre 60 secondes et 24 heures. Pour plus d’informations, consultez les informations de référence sur l’API [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (paramètre *faceIdTimeToLive*).

## <a name="november-2020"></a>Novembre 2020
* Publication d’un exemple d’application d’inscription à Visage pour montrer les meilleures pratiques pour l’établissement d’un consentement explicite et créer des systèmes de reconnaissance faciale de haute précision via des inscriptions de haute qualité. L’exemple open source se trouve dans le guide [Créer une application d’inscription](build-enrollment-app.md) et sur [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), prêt à être déployé ou personnalisé par des développeurs. 

## <a name="august-2020"></a>Août 2020
* Chiffrement des données au repos géré par le client : Le service Visage chiffre automatiquement vos données lors de leur conservation dans le cloud. Le chiffrement du service Visage protège vos données et vous aide à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Par défaut, votre abonnement utilise des clés de chiffrement gérées par Microsoft. Il existe aussi une nouvelle possibilité de gérer votre abonnement avec vos propres clés appelées clés gérées par le client (CMK). Pour plus d’informations, consultez [Clés gérées par le client](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>Avril 2020
* Nouveau modèle de reconnaissance de l’API Visage : Le nouveau modèle recognition 03 est le modèle le plus précis actuellement disponible. Si vous êtes un nouveau client, nous vous recommandons d’utiliser ce modèle. Recognition 03 offrira une meilleure précision pour les comparaisons de similarité et les comparaisons de correspondance de personne. Pour plus d’informations, consultez [Spécifier un modèle de reconnaissance faciale](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Juin 2019

* Ajout d’un nouveau modèle de détection des visages avec une meilleure précision sur les visages de petite taille, de profil, masqués et flous. Utilisez-le par le biais de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) et [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) en spécifiant le nom du nouveau modèle de détection des visages `detection_02` dans le paramètre `detectionModel`. Vous trouverez des détails supplémentaires dans [Guide pratique pour spécifier un modèle de détection](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Avril 2019

* Amélioration de la précision globale des attributs `age` et `headPose`. L’attribut `headPose` a également été mis à jour ; la valeur `pitch` est désormais activée. Utilisez ces attributs en les spécifiant dans le paramètre `returnFaceAttributes` de [Face - Detect paramètre ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Amélioration de la rapidité de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) et [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2019"></a>Mars 2019

* Ajout d’un nouveau modèle de reconnaissance faciale avec une meilleure précision. Utilisez-le par le biais de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) et [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) en spécifiant le nom du nouveau modèle de reconnaissance faciale `recognition_02` dans le paramètre `recognitionModel`. Vous trouverez des détails supplémentaires dans [Guide pratique pour spécifier un modèle de reconnaissance](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Janvier 2019

* Ajout de la fonctionnalité de capture instantanée pour prendre en charge la migration des données entre des abonnements : [Capture instantanée](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Vous trouverez des détails supplémentaires dans [Migrer vos données de visage vers un autre abonnement API Visage](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Octobre 2018

* Description détaillée de `status`, `createdDateTime`, `lastActionDateTime` et `lastSuccessfulTrainingDateTime` dans [PersonGroup - Obtenir l’état de l’entrainement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Obtenir l’état de l’entrainement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) et [ LargeFaceList - Obtenir l’état de l’entrainement](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Mai 2018

* Nous avons considérablement amélioré l’attribut `gender` ainsi que nous avons amélioré les attributs `age`, `glasses`, `facialHair`, `hair` et `makeup`. Utilisez-les via le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

* Nous avons augmenté la taille maximale des fichiers d’image d’entrée la faisant passer de 4 à 6 Mo dans [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) et [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Mars 2018

* Nous avons ajouté un conteneur à l’échelle un million : [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) et [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Vous trouverez davantage d’informations dans la section [Comment utiliser la fonctionnalité à grande échelle](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Nous avons fait passer la plage de valeurs du paramètre `maxNumOfCandidatesReturned` de [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) de [1, 5] à [1, 100], sa valeur par défaut étant 10.

## <a name="may-2017"></a>Mai 2017

* Nous avons ajouté les attributs `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` et `noise` dans le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

* Les paramètres PersonGroup et [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) acceptent désormais 10 000 personnes.

* Nous prenons en charge la pagination dans [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) avec comme paramètres facultatifs `start` et `top`.

* Nous prenons en charge l’accès concurrentiel pour ajouter et supprimer des visages concernant les listes FaceLists et les personnes dans PersonGroup.

## <a name="march-2017"></a>Mars 2017
* Nous avons ajouté l’attribut `emotion` dans le paramètre `returnFaceAttributes` de [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

* Nous avons résolu le problème qui entraînait une absence de détection et le renvoi d’un rectangle depuis [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) en tant que `targetFace` dans [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) et [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Nous avons résolu le problème affectant la taille du visage détectable pour garantir qu’elle se trouvait bien entre 36 x 36 et 4 096 x 4096 pixels.

## <a name="november-2016"></a>Novembre 2016
* Nous avons ajouté un abonnement Stockage Visage standard qui permet de stocker des visages persistants supplémentaires lorsque vous utilisez [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) pour l’identification ou la mise en correspondance des similitudes. Les images stockées sont facturées au tarif de 0,5 $ tous les 1 000 visages (tarif journalier au prorata). Les abonnements de niveau gratuit sont toujours limités à un total de 1 000 personnes.

## <a name="october-2016"></a>Octobre 2016
* Nous avons modifié le message d’erreur lorsque plusieurs visages apparaissent dans targetFace. Auparavant, il indiquait « There are more than one face in the image ». Maintenant, il indique « There is more than one face in the image » dans [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) et [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Juillet 2016
* Nous prenons en charge l’authentification d’objet Face to Person dans [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Nous avons ajouté un paramètre `mode` facultatif qui permet de sélectionner deux modes de fonctionnement : `matchPerson` et `matchFace` dans [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), le paramètre par défaut étant `matchPerson`.

* Nous avons ajouté un paramètre `confidenceThreshold` facultatif qui permet à l’utilisateur de définir le seuil indiquant si un visage appartient à l’objet Person dans [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Nous avons ajouté les paramètres `start` et `top` facultatifs dans [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) pour permettre à l’utilisateur d’indiquer le point de départ et le nombre total de PersonGroups dans la liste.

## <a name="v10-changes-from-v0"></a>Changements apportés entre la version 0 et la version 1.0

* Nous avons mis à jour le point de terminaison de la racine du service le faisant passer de ```https://westus.api.cognitive.microsoft.com/face/v0/``` à ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Nous avons apporté des modifications à : [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) et [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Nous avons mis à jour la taille minimale pour la détection des visages la faisant passer à 36 x 36 pixels. Les visages dont la taille est inférieure à 36 x 36 pixels ne seront pas détectés.

* Nous avons abandonné les données PersonGroup et Person qui étaient disponibles dans la version 0 de Visage. Ces données ne sont pas accessibles dans la version 1.0 de Visage.

* Nous avons abandonné le point de terminaison de la version 0 de l’API Visage au 30 juin 2016.