---
title: Glossaire - Service API Visage
titleSuffix: Azure Cognitive Services
description: Le glossaire explique les termes que vous êtes susceptible de rencontrer lorsque vous utilisez le service API Visage.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: d627c3c4419affa0d71cdb23df945c96d9fd7585
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652431"
---
# <a name="glossary"></a>Glossaire

## <a name="a"></a>A

#### <a name="attributes"></a>Attributs

Les attributs sont des caractéristiques de visage facultatif qui peuvent être détectées, telles que [âge](#age-attribute), [sexe](#gender-attribute), [pose principal](#head-pose-attribute), [PILOSITÉ faciale](#facial-hair-attribute)et [sourire](#smile-attribute). Elles peuvent être obtenues à partir de l’API de détection en spécifiant le _returnFaceAttributes_ paramètre de requête.

Pour obtenir une liste complète des attributs de visage, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Âge (attribut)

L’âge est un [attribut](#attributes) qui décrit l’âge d’un visage particulier. L’attribut âge est facultatif dans les résultats de détection, et il peut être contrôlé par une requête de détection en spécifiant le paramètre returnFaceAttributes.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Candidat

Les candidats sont essentiellement des résultats d’[identification](#identification) (par exemple des personnes identifiés et le niveau de confiance dans les détections). Un candidat est représenté par l’[ID de personne](#person-id) et la [confiance](#confidence), indiquant que la personne est identifiée avec un niveau de confiance élevé.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Confiance

La confiance est une mesure révélant la similarité entre des [visages](#face) ou [personnes](#person) avec des valeurs numériques. On l’utilise dans l’[identification](#identification) et la [ vérification](#verification) pour indiquer les similitudes entre les résultats recherchés, identifiés et vérifiés.

Pour plus d’informations, consultez la documentation de référence : [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Détection de visage

La détection de visage est l’action qui consiste à localiser des visages dans des images. Les utilisateurs peuvent charger une image ou spécifier une URL d’image dans la requête. Les visages détectés sont retournés avec des [ID de visages](#face-id) indiquant une identité unique dans l’API Visage. Les rectangles indiquent les emplacements des visages dans l’image en pixels, ainsi que les [attributs](#attributes) facultatifs pour chaque visage, tels que l’[âge](#age-attribute), le [sexe](#gender-attribute), la [pose de la tête](#head-pose-attribute), la [pilosité faciale](#facial-hair-attribute) et le [sourire](#smile-attribute).

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Émotion (attribut)

Émotion est un de la [attributs](#attributes). Lorsque l’interrogé, il retourne une liste des émotions et leur confiance de détection pour le visage donné. Scores de confiance sont normalisées : les scores sur tous les émotions ajoutera au maximum un. Les émotions retournées sont bonheur, tristesse, neutre, la colère, mépris, le dégoût, surprise et peur.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Face

Visage est un terme unifié pour les résultats dérivés de l’API Visage associés aux visages détectés. Au final, face est représentée par une identité unifiée ([Face ID](#face-id)), une région spécifiée dans les images ([Rectangle englobant le visage](#face-rectangle)) et les attributs supplémentaires liés à la face, tel que [âge](#age-attribute), [sexe](#gender-attribute), points de repère et [pose principal](#head-pose-attribute). Les visages peuvent également être retournés à partir de la détection.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>API Visage

L’API Visage est une API basée sur le cloud qui fournit les algorithmes les plus avancés en matière de détection et de reconnaissance de visage. La fonctionnalité principale de l’API Visage peut être divisée en deux catégories : détection de visage avec des attributs, et [reconnaissance](#recognition) de visage.

Pour plus d’informations, consultez la documentation de référence : [Visage - Présentation de l’API](./Overview.md), [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Visage - Grouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Attributs de visage/attributs faciaux

Voir [Attributs](#attributes).

#### <a name="face-id"></a>ID de visage

L’ID de visage est dérivée des résultats de détection, dans lesquels une chaîne représente un [visage](#face) dans l’[API Visage](#face-api).

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Points de repère de visage/points de repère faciaux

Les points de repère sont facultatifs dans les résultats de détection. Il s’agit de points faciaux sémantiques, tels que les yeux, le nez et la bouche (voir la figure ci-dessous). Les points de repère peuvent être contrôlés avec une requête de détection par le nombre booléen returnFaceLandmarks. Si returnFaceLandmarks a la valeur True, les visages retournés ont des attributs de points de repère.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Rectangle de visage

Le rectangle de visage est dérivé des résultats de détection. Il s’agit d’un rectangle vertical (gauche, haut, largeur, hauteur) dans les images en pixels. L’angle supérieur gauche d’un [visage](#face) (gauche, haut), en plus de la largeur et de la hauteur, indique les tailles de visage selon les axes x et y respectivement.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Pilosité faciale (attribut)

La pilosité faciale est l’un des [attributs](#attributes) servant à décrire la longueur des cheveux et poils des visages disponibles. L’attribut pilosité faciale est facultatif dans les résultats de détection, et il peut être contrôlé par une requête de détection à l’aide de returnFaceAttributes. Si returnFaceAttributes contient « facialHair », les visages retournés ont des attributs de pilosité faciale.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList est une collection de [PersistedFace](#persistedface). Il s’agit de l’unité de [Rechercher semblables](#find-similar). Un FaceList est fourni avec un [ID de FaceList](#facelist-id), ainsi que d’autres attributs tels que le nom et les données utilisateur.

Pour plus d’informations, consultez la documentation de référence : [FaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>ID de FaceList

L’ID de FaceList est une chaîne fournie par l’utilisateur qui sert d’identificateur de [FaceList](#facelist). L’ID de FaceList doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez la documentation de référence : [FaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Rechercher semblables

Cette API sert à rechercher des visages semblables parmi une collection de visages. Les visages de requêtes et les collections de visages sont représentés sous forme d’[ID de visages](#face-id) ou d’[ID de FaceList](#facelist-id)/[ID de LargeFaceList](#largefacelist-id) dans la requête. Les résultats retournés sont des visages semblables recherchés, représentés par des [ID de visages](#face-id) ou ID de PersistedFace.

Pour plus d’informations, consultez la documentation de référence : [Face - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Gender (attribut)

Gender est un [attribut](#attributes) servant à décrire le sexe des visages disponibles. L’attribut gender est facultatif dans les résultats de détection, et il peut être contrôlé par une requête de détection à l’aide de returnFaceAttributes. Si returnFaceAttributes contient « gender », les visages retournés ont des attributs de sexe.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Regroupement

Le regroupement de visages est le regroupement d’une collection de visages d’après des similitudes faciales. Les collections de visages sont indiquées par les collections d’ID de visages dans la requête. Du fait du regroupement, les visages semblables sont regroupés dans des [groupes](#groups), et les visages qui ne ressemblent à aucun autre visage sont fusionnés dans un groupe désordonné. Il existe au maximum un seul [groupe désordonné](#messy-group) dans le résultat de regroupement.

Pour plus d’informations, consultez la documentation de référence : [Face - groupe](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Groupes

Les groupes sont dérivés des résultats de [regroupement](#grouping). Chaque groupe contient une collection de visages semblables, où les visages sont indiqués par des [ID de visages](#face-id).

Pour plus d’informations, consultez la documentation de référence : [Face - groupe](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>Pose de la tête (attribut)

La pose de la tête est un [attribut](#attributes) qui représente l’orientation du visage dans l’espace 3D en fonction des angles de roulement, tangage et lacet, comme illustré ci-dessous. Les plages de valeurs de roulement et lacet sont [-180, 180] et [-90, 90] en degrés. Dans la version actuelle, la valeur de tangage retournée par la détection est toujours 0. L’attribut pose de la tête est facultatif dans les résultats de détection, et il peut être contrôlé par une requête de détection à l’aide du paramètre returnFaceAttributes. Si le paramètre returnFaceAttributes contient « headPose », les visages retournés ont des attributs de pose de la tête.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identification

L’identification consiste à identifier un ou plusieurs visages parmi un LargePersonGroup/PersonGroup.
Un [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup) est une collection de [personnes](#person).
Les visages et le LargePersonGroup/PersonGroup sont représentés respectivement par des [ID de visages](#face-id) et des [ID de LargePersonGroup](#largepersongroup-id)/[ID de PersonGroup](#persongroup-id) dans la requête.
Les résultats identifiés sont des [candidats](#candidate), représentés par des [personnes](#person) avec confiance.
Les différents visages dans l’entrée sont pris en compte séparément, et chaque visage a son propre résultat identifié.

> [!NOTE]
> Le LargePersonGroup/PersonGroup doit être entraîné avant identification. Si le LargePersonGroup/PersonGroup n’est pas entraîné, ou si l’[état](#status-train) affiché de la formation n’est pas « succès » (par exemple, « en cours d’exécution », « échec » ou « expiration »), la réponse de la requête est 400.
> 

Pour plus d’informations, consultez la documentation de référence : [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical est un champ booléen de résultats de [vérification](#verification) indiquant si deux visages appartiennent à la même personne.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : Vérifiez](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Points de repère

Voir Points de repères de visage.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList est une collection de [PersistedFace](#persistedface). Il s’agit de l’unité de [Rechercher semblables](#find-similar). Un LargeFaceList est fourni avec un [ID de LargeFaceList](#largefacelist-id), ainsi que d’autres attributs tels que le nom et les données utilisateur.

Pour plus d’informations, consultez la documentation de référence : [LargeFaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - Répertorier visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>ID de LargeFaceList

L’ID de LargeFaceList est une chaîne fournie par l’utilisateur qui sert d’identificateur de [LargeFaceList](#largefacelist). L’ID de LargeFaceList doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez la documentation de référence : [LargeFaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup est une collection de [personnes](#person). Il s’agit de l’unité d’[Identification](#identification). Un LargePersonGroup est fourni avec un [ID de LargePersonGroup](#largepersongroup-id), ainsi que d’autres attributs tels que le nom et les données utilisateur.

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person - Répertorier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>ID de LargePersonGroup

L’ID de LargePersonGroup est une chaîne fournie par l’utilisateur qui sert d’identificateur de [LargePersonGroup](#largepersongroup). L’ID de LargePersonGroup doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Groupe désordonné

Le groupe désordonné est dérivé des résultats de [regroupement](#grouping). Il contient des visages qui ne ressemblent à aucun autre. Chaque visage dans un groupe désordonné est indiqué par l’[ID de visage](#face-id).

Pour plus d’informations, consultez la documentation de référence : [Face - groupe](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Nom (personne)

Nom est une chaîne descriptive conviviale pour [personne](#person). Contrairement à l’[ID de personne](#person-id), le nom des personnes peut être dupliqué dans un groupe.

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Nom (LargePersonGroup/PersonGroup)

Nom est également une chaîne descriptive conviviale pour [LargePersonGroup](#largepersongroup)/[groupe de personnes](#persongroup). Contrairement à l’[ID de LargePersonGroup](#largepersongroup-id)/[ID de PersonGroup](#persongroup-id), le nom de LargePersonGroups/PersonGroups peut être dupliqué au sein d’un abonnement.

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace est une structure de données dans l’API Visage. PersistedFace est fourni avec un ID de PersistedFace, ainsi que d’autres attributs tels que le nom et les données utilisateur.

Pour plus d’informations, consultez la documentation de référence : [LargeFaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>ID de personne

L’ID de personne est généré quand un [PersistedFace](#persistedface) est créé avec succès. Une chaîne est créée pour représenter ce visage dans l’[API Visage](#face-api).

Pour plus d’informations, consultez la documentation de référence : [LargeFaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Personne

Person est une structure de données gérée dans l’API Visage. Person est fourni avec un [ID de personne](#person-id), ainsi que d’autres attributs tels que le nom, une collection de [PersistedFace](#persistedface) et les données utilisateur.

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>ID de personne

L’ID de personne est généré quand une [Person](#person) est créée avec succès. Une chaîne est créée pour représenter cette personne dans l’[API Visage](#face-api).

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

PersonGroup est une collection de [personnes](#person). Il s’agit de l’unité d’[Identification](#identification). Un PersonGroup est fourni avec un [ID de PersonGroup](#persongroup-id), ainsi que d’autres attributs tels que le nom et les données utilisateur.

Pour plus d’informations, consultez la documentation de référence : [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person - Répertorier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>ID de PersonGroup

L’ID de PersonGroup est une chaîne fournie par l’utilisateur qui sert d’identificateur de [PersonGroup](#persongroup). L’ID de groupe doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez la documentation de référence : [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Pose (attribut)

Voir [Pose de la tête](#head-pose-attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Reconnaissance

La reconnaissance est une zone d’application répandue pour les technologies faciales, telles que [Rechercher semblables](#find-similar), [Regrouper](#grouping), [Identifier](#identification),[Vérifier si deux visages sont identiques](#verification).

Pour plus d’informations, consultez la documentation de référence : [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Visage - Regrouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Rectangle (visage)

Voir [Rectangle visage](#face-rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Recherche de visages semblables

Voir [Rechercher semblables](#find-similar).

#### <a name="smile-attribute"></a>Smile (attribut)

Smile est un [attribut](#attributes) servant à décrire l’expression de sourire des visages disponibles. L’attribut smile est facultatif dans les résultats de détection, et il peut être contrôlé par une requête de détection à l’aide de returnFaceAttributes. Si returnFaceAttributes contient « smile », les visages retournés ont des attributs de sourire.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Instantané

Un instantané est un stockage à distance temporaire pour certains types de données Visage. Il fonctionne comme une sorte de Presse-papiers pour copier des données d’un abonnement à un autre. L’utilisateur « prend » d’abord un instantané des données de l’abonnement source, puis les « applique » à un nouvel objet de données de l’abonnement cible. 

Pour plus de détails, consultez le [Guide de migration de Visage](./face-api-how-to-topics/how-to-migrate-face-data.md) ainsi que la documentation de référence REST sur [Instantané - Prendre](/rest/api/cognitiveservices/face/snapshot/take) et [Instantané - Appliquer](/rest/api/cognitiveservices/face/snapshot/apply).

#### <a name="status-train"></a>État (entraîner)

L’état est une chaîne servant à décrire la procédure d’entraînement de LargeFaceList/LargePersonGroups/PersonGroups, notamment « non démarré «, « en cours d’exécution », « succès », « échec ».

Pour plus d’informations, consultez la documentation de référence : [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [groupe de personnes - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Clé d’abonnement

La clé d’abonnement est une chaîne que vous devez spécifier comme paramètre de chaîne de requête afin d’appeler toute API Visage. Elle figure dans la page Mes abonnements une fois que vous vous êtes connecté au portail Microsoft Cognitive Services. Deux clés sont associées à chaque abonnement : une clé primaire et une clé secondaire. Toutes deux peuvent être utilisées pour appeler l’API de façon identique. Vous devez sécuriser les clés de l’abonnement, et vous pouvez aussi les regénérer à tout moment à partir de la page Mes abonnements.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Entraîner (LargePersonGroup/LargeFaceList/PersonGroup)

Cette API sert à prétraiter le [LargeFaceList](#largefacelist)/[LargePersonGroup](#largepersongroup)/[PersonGroup](#persongroup) zfin de garantir les performances de [Rechercher semblables](#find-similar)/[Identification](#identification) . Si l’entraînement n’est pas lancé, ou si l’[état de l’entraînement](#status-train) n’est pas affiché comme ayant réussi, l’identification de ce PersonGroup échoue.

Pour plus d’informations, consultez la documentation de référence : [LargeFaceList - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Données utilisateur

Les données utilisateur sont des informations supplémentaires associées à [Person](#person) et [PersonGroup](#persongroup)/[LargePersonGroup](#largepersongroup). Elles sont définies par les utilisateurs afin de faciliter l’utilisation, la compréhension et la mémorisation des données.

Pour plus d’informations, consultez la documentation de référence : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Vérification

Cette API sert à vérifier si deux visages sont identiques. Les deux visages sont représentés sous forme d’ID de visages dans la requête. Les résultats vérifiés contiennent un champ booléen (isIdentical) indiquant « same » si la valeur est True, et un champ numérique ([confidence](#confidence)) indiquant le niveau de confiance.

Pour plus d’informations, consultez la documentation de référence : [Doivent faire face : Vérifiez](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
