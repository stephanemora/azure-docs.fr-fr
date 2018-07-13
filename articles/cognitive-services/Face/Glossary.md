---
title: Glossaire pour le Service API Visage | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Le glossaire explique les termes que vous êtes susceptible de rencontrer lorsque vous utilisez le service API Visage.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368205"
---
# <a name="glossary"></a>Glossaire

## <a name="a"></a>A

#### <a name="Attributes"></a>Attributs

Les attributs, tels que [âge](#Age-Attribute), [sexe](#Gender-Attribute), [pose de la tête](#Head-Pose-Attribute), [ 
pilosité faciale](#Facial-Hair-Attribute) et [sourire](#Smile-Attribute), sont facultatifs dans les résultats de [détection](#Detection-Face-Detection).
Ils peuvent être obtenus à partir de l’API de [détection](#Detection-Face-Detection) en spécifiant les paramètres de requête : returnFaceAttributes. Les attributs fournissent des informations supplémentaires concernant les [visages](#Face) sélectionnés, en plus de l’[ID de visage](#Face-ID) et du [rectangle](#Face-Rectangle).

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Age-Attribute"></a>Âge (attribut)

L’âge est un [attribut](#Attributes) qui décrit l’âge d’un visage particulier. L’attribut âge est facultatif dans les résultats de [détection](#Detection-Face-Detection), et il peut être contrôlé par une requête de [détection](#Detection-Face-Detection) en spécifiant le paramètre returnFaceAttributes.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>b

## <a name="c"></a>C

#### <a name="Candidate"></a>Candidat

Les candidats sont essentiellement des résultats d’[identification](#Identification) (par exemple des personnes identifiés et le niveau de confiance dans les détections). Un candidat est représenté par l’[ID de personne](#Person-ID) et la [confiance](#Confidence), indiquant que la personne est identifiée avec un niveau de confiance élevé.

Pour plus d’informations, consultez le guide [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="Confidence"></a>Confiance

La confiance est une mesure révélant la similarité entre des [visages](#Face) ou [personnes](#Person) avec des valeurs numériques. On l’utilise dans l’[identification](#Identification) et la [ vérification](#Verification) pour indiquer les similitudes entre les résultats recherchés, identifiés et vérifiés.

Pour plus d’informations, consultez les guides suivants : [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"></a>Détection de visage

La détection de visage est l’action qui consiste à localiser des visages dans des images. Les utilisateurs peuvent charger une image ou spécifier une URL d’image dans la requête. Les visages détectés sont retournés avec des [ID de visages](#Face-ID) indiquant une identité unique dans l’API Visage. Les rectangles indiquent les emplacements des visages dans l’image en pixels, ainsi que les [attributs](#Attributes) facultatifs pour chaque visage, tels que l’[âge](#Age-Attribute), le [sexe](#Gender-Attribute), la [pose de la tête](#Head-Pose-Attribute), la [pilosité faciale](#Facial-Hair-Attribute) et le [sourire](#Smile-Attribute).

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>Visage

Visage est un terme unifié pour les résultats dérivés de l’API Visage associés aux visages détectés. Le visage est représenté par une identité unifiée ([ID de visage](#Face-ID)), une région donnée dans les images ([Rectangle de visage](#Face-Rectangle)) et des [attributs](#Face-Attributes-Facial-Attributes) supplémentaires liés au visage, tels que l’[âge](#Age-Attribute), le [sexe](#Gender-Attribute), les [points de repère](#Face-Landmarks-Facial-Landmarks) et la [pose de la tête](#Head-Pose-Attribute). Les visages peuvent également être retournés à partir de la [détection](#Detection-Face-Detection).

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-API"></a>API Visage

L’API Visage est une API basée sur le cloud qui fournit les algorithmes les plus avancés en matière de détection et de reconnaissance de visage. La fonctionnalité principale de l’API Visage peut être divisée en deux catégories : [détection](#Detection-Face-Detection) de visage avec des [attributs](#Face-Attributes-Facial-Attributes), et [reconnaissance](#Recognition) de visage.

Pour plus d’informations, consultez les guides suivants : [Visage - Présentation de l’API](./Overview.md), [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Visage - Grouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="Face-Attributes-Facial-Attributes"></a>Attributs de visage/attributs faciaux

Voir [Attributs](#Attributes).

#### <a name="Face-ID"></a>ID de visage

L’ID de visage est dérivée des résultats de [détection](#Detection-Face-Detection), dans lesquels une chaîne représente un [visage](#Face) dans l’[API Visage](#Face-API).

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-Landmarks-Facial-Landmarks"></a>Points de repères de visage/points de repères faciaux

Les points de repère sont facultatifs dans les résultats de [détection](#Detection-Face-Detection). Il s’agit de points faciaux sémantiques, tels que les yeux, le nez et la bouche (voir la figure ci-dessous). Les points de repère peuvent être contrôlés avec une requête de [détection](#Detection-Face-Detection) par le nombre booléen returnFaceLandmarks. Si returnFaceLandmarks a la valeur True, les visages retournés ont des attributs de points de repère.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>Rectangle de visage

Le rectangle de visage est dérivé des résultats de [détection](#Detection-Face-Detection). Il s’agit d’un rectangle vertical (gauche, haut, largeur, hauteur) dans les images en pixels. L’angle supérieur gauche d’un [visage](#Face) (gauche, haut), en plus de la largeur et de la hauteur, indique les tailles de visage selon les axes x et y respectivement.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Facial-Hair-Attribute"></a>Pilosité faciale (attribut)

La pilosité faciale est l’un des [attributs](#Attributes) servant à décrire la longueur des cheveux et poils des visages disponibles. L’attribut pilosité faciale est facultatif dans les résultats de [détection](#Detection-Face-Detection), et il peut être contrôlé par une requête de [détection](#Detection-Face-Detection) à l’aide de returnFaceAttributes. Si returnFaceAttributes contient « facialHair », les visages retournés ont des attributs de pilosité faciale.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="FaceList"></a>ID de FaceList

FaceList est une collection de [PersistedFace](#PersistedFace). Il s’agit de l’unité de [Rechercher semblables](#Find-Similar). Un FaceList est fourni avec un [ID de FaceList](#FaceList-ID), ainsi que d’autres attributs tels que le [nom](#Name) et les [données utilisateur](#UserData-User-Data).

Pour plus d’informations, consultez les guides suivants : [FaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="FaceList-ID"></a>ID de FaceList

L’ID de FaceList est une chaîne fournie par l’utilisateur qui sert d’identificateur de [FaceList](#FaceList). L’ID de FaceList doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez les guides suivants : [FaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="Find-Similar"></a>Rechercher semblables

Cette API sert à rechercher des visages semblables parmi une collection de visages. Les visages de requêtes et les collections de visages sont représentés sous forme d’[ID de visages](#Face-ID) ou d’[ID de FaceList](#FaceList-ID)/[ID de LargeFaceList](#LargeFaceList-ID) dans la requête. Les résultats retournés sont des visages semblables recherchés, représentés par des [ID de visages](#Face-ID) ou [ID de PersistedFace](#PersistedFace-ID).

Pour plus d’informations, consultez les guides suivants : [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="Gender-Attribute"></a>Gender (attribut)

Gender est un [attribut](#Attributes) servant à décrire le sexe des visages disponibles. L’attribut gender est facultatif dans les résultats de [détection](#Detection-Face-Detection), et il peut être contrôlé par une requête de [détection](#Detection-Face-Detection) à l’aide de returnFaceAttributes. Si returnFaceAttributes contient « gender », les visages retournés ont des attributs de sexe.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Grouping"></a>Groupement

Le regroupement de visages est le regroupement d’une collection de visages d’après des similitudes faciales. Les collections de visages sont indiquées par les collections d’ID de visages dans la requête. Du fait du regroupement, les visages semblables sont regroupés dans des [groupes](#Groups), et les visages qui ne ressemblent à aucun autre visage sont fusionnés dans un groupe désordonné. Il existe au maximum un seul [groupe désordonné](#Messy-Group) dans le résultat de regroupement.

Pour plus d’informations, consultez le guide [Visage - Grouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="Groups"></a>Groupes

Les groupes sont dérivés des résultats de [regroupement](#Grouping). Chaque groupe contient une collection de visages semblables, où les visages sont indiqués par des [ID de visages](#Face-ID).

Pour plus d’informations, consultez le guide [Visage - Grouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>Pose de la tête (attribut)

La pose de la tête est un [attribut](#Attributes) qui représente l’orientation du visage dans l’espace 3D en fonction des angles de roulement, tangage et lacet, comme illustré ci-dessous. Les plages de valeurs de roulement et lacet sont [-180, 180] et [-90, 90] en degrés. Dans la version actuelle, la valeur de tangage retournée par la détection est toujours 0. L’attribut pose de la tête est facultatif dans les résultats de [détection](#Detection-Face-Detection), et il peut être contrôlé par une requête de [détection](#Detection-Face-Detection) à l’aide du paramètre returnFaceAttributes. Si le paramètre returnFaceAttributes contient « headPose », les visages retournés ont des attributs de pose de la tête.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>Identification

L’identification consiste à identifier un ou plusieurs visages parmi un LargePersonGroup/PersonGroup.
Un [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) est une collection de [personnes](#Person).
Les visages et le LargePersonGroup/PersonGroup sont représentés respectivement par des [ID de visages](#Face-ID) et des [ID de LargePersonGroup](#LargePersonGroup-ID)/[ID de PersonGroup](#PersonGroup-ID) dans la requête.
Les résultats identifiés sont des [candidats](#Candidate), représentés par des [personnes](#Person) avec confiance.
Les différents visages dans l’entrée sont pris en compte séparément, et chaque visage a son propre résultat identifié.

> [!NOTE]
> Le LargePersonGroup/PersonGroup doit être entraîné avant identification. Si le LargePersonGroup/PersonGroup n’est pas entraîné, ou si l’[état](#Status-Train) affiché de la formation n’est pas « succès » (par exemple, « en cours d’exécution », « échec » ou « expiration »), la réponse de la requête est 400.
> 

Pour plus d’informations, consultez les guides suivants : [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical est un champ booléen de résultats de [vérification](#Verification) indiquant si deux visages appartiennent à la même personne.

Pour plus d’informations, consultez le guide [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Points de repère

Voir [Points de repères de visage](#Face-Landmarks-Facial-Landmarks).

#### <a name="LargeFaceList"></a>ID de LargeFaceList

LargeFaceList est une collection de [PersistedFace](#PersistedFace). Il s’agit de l’unité de [Rechercher semblables](#Find-Similar). Un LargeFaceList est fourni avec un [ID de LargeFaceList](#LargeFaceList-ID), ainsi que d’autres attributs tels que le [nom](#Name) et les [données utilisateur](#UserData-User-Data).

Pour plus d’informations, consultez les guides suivants : [LargeFaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - Répertorier visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="LargeFaceList-ID"></a>ID de LargeFaceList

L’ID de LargeFaceList est une chaîne fournie par l’utilisateur qui sert d’identificateur de [LargeFaceList](#LargeFaceList). L’ID de LargeFaceList doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez les guides suivants : [LargeFaceList - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="LargePersonGroup"></a>ID de LargePersonGroup

LargePersonGroup est une collection de [personnes](#Person). Il s’agit de l’unité d’[Identification](#Identification). Un LargePersonGroup est fourni avec un [ID de LargePersonGroup](#LargePersonGroup-ID), ainsi que d’autres attributs tels que le [nom](#Name) et les [données utilisateur](#UserData-User-Data).

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person - Répertorier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="LargePersonGroup-ID"></a>ID de LargePersonGroup

L’ID de LargePersonGroup est une chaîne fournie par l’utilisateur qui sert d’identificateur de [LargePersonGroup](#LargePersonGroup). L’ID de LargePersonGroup doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="Messy-Group"></a>Groupe désordonné

Le groupe désordonné est dérivé des résultats de [regroupement](#Grouping). Il contient des visages qui ne ressemblent à aucun autre. Chaque visage dans un groupe désordonné est indiqué par l’[ID de visage](#Face-ID).

Pour plus d’informations, consultez le guide [Visage - Grouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Nom (personne)

Le nom est une chaîne descriptive conviviale pour [Person](#Person). Contrairement à l’[ID de personne](#Person-ID), le nom des personnes peut être dupliqué dans un groupe.

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Name"></a>Nom (LargePersonGroup/PersonGroup)

Le nom est également une chaîne descriptive conviviale pour [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Contrairement à l’[ID de LargePersonGroup](#LargePersonGroup-ID)/[ID de PersonGroup](#PersonGroup-ID), le nom de LargePersonGroups/PersonGroups peut être dupliqué au sein d’un abonnement.

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace est une structure de données dans l’API Visage. PersistedFace est fourni avec un [ID de PersistedFace](#PersistedFace-ID), ainsi que d’autres attributs tels que le [nom](#Name) et les [données utilisateur](#UserData-User-Data).

Pour plus d’informations, consultez les guides suivants : [LargeFaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person-ID"></a>ID de personne

L’ID de personne est généré quand un [PersistedFace](#PersistedFace) est créé avec succès. Une chaîne est créée pour représenter ce visage dans l’[API Visage](#Face-API).

Pour plus d’informations, consultez les guides suivants : [LargeFaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [ PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person"></a>Person

Person est une structure de données gérée dans l’API Visage. Person est fourni avec un [ID de personne](#Person-ID), ainsi que d’autres attributs tels que le [nom](#Name), une collection de [PersistedFace](#PersistedFace) et les [données utilisateur](#UserData-User-Data).

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Person-ID"></a>ID de personne

L’ID de personne est généré quand une [Person](#Person) est créée avec succès. Une chaîne est créée pour représenter cette personne dans l’[API Visage](#Face-API).

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup est une collection de [personnes](#Person). Il s’agit de l’unité d’[Identification](#Identification). Un PersonGroup est fourni avec un [ID de PersonGroup](#PersonGroup-ID), ainsi que d’autres attributs tels que le [nom](#Name) et les [données utilisateur](#UserData-User-Data).

Pour plus d’informations, consultez les guides suivants : [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person - Répertorier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="PersonGroup-ID"></a>ID de PersonGroup

L’ID de PersonGroup est une chaîne fournie par l’utilisateur qui sert d’identificateur de [PersonGroup](#PersonGroup). L’ID de groupe doit être unique au sein de l’abonnement.

Pour plus d’informations, consultez les guides suivants : [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Obtenir](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Pose (attribut)

Voir [Pose de la tête](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="Recognition"></a>Reconnaissance

La reconnaissance est une zone d’application répandue pour les technologies faciales, telles que [Rechercher semblables](#Find-Similar), [Regrouper](#Grouping), [Identifier](#Identification),[Vérifier si deux visages sont identiques](#Verification).

Pour plus d’informations, consultez les guides suivants : [Visage - Rechercher semblables](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Visage - Regrouper](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Rectangle (visage)

Voir [Rectangle visage](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>Smile (attribut)

Smile est un [attribut](#Attributes) servant à décrire l’expression de sourire des visages disponibles. L’attribut smile est facultatif dans les résultats de [détection](#Detection-Face-Detection), et il peut être contrôlé par une requête de [détection](#Detection-Face-Detection) à l’aide de returnFaceAttributes. Si returnFaceAttributes contient « smile », les visages retournés ont des attributs de sourire.

Pour plus d’informations, consultez le guide [Visage - Détecter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Recherche de visages semblables

Voir [Rechercher semblables](#Find-Similar).

#### <a name="Status-Train"></a>État (entraîner)

L’état est une chaîne servant à décrire la procédure d’[entraînement de LargeFaceList/LargePersonGroups/PersonGroups](#Train), notamment « non démarré «, « en cours d’exécution », « succès », « échec ».

Pour plus d’informations, consultez le guide [LargeFaceList - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Clé d’abonnement

La clé d’abonnement est une chaîne que vous devez spécifier comme paramètre de chaîne de requête afin d’appeler toute API Visage. Elle figure dans la page Mes abonnements une fois que vous vous êtes connecté au portail Microsoft Cognitive Services. Deux clés sont associées à chaque abonnement : une clé primaire et une clé secondaire. Toutes deux peuvent être utilisées pour appeler l’API de façon identique. Vous devez sécuriser les clés de l’abonnement, et vous pouvez aussi les regénérer à tout moment à partir de la page Mes abonnements.

## <a name="t"></a>T

#### <a name="Train"></a>Entraîner (LargePersonGroup/LargeFaceList/PersonGroup)

Cette API sert à prétraiter le [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) zfin de garantir les performances de [Rechercher semblables](#Find-Similar)/[Identification](#Identification) . Si l’entraînement n’est pas lancé, ou si l’[état de l’entraînement](#Status-Train) n’est pas affiché comme ayant réussi, l’identification de ce PersonGroup échoue.

Pour plus d’informations, consultez les guides suivants : [LargeFaceList - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Entraîner](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [Visage - Identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>Données utilisateur

Les données utilisateur sont des informations supplémentaires associées à [Person](#Person) et [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Elles sont définies par les utilisateurs afin de faciliter l’utilisation, la compréhension et la mémorisation des données.

Pour plus d’informations, consultez les guides suivants : [LargePersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Mettre à jour](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="Verification"></a>Vérification

Cette API sert à vérifier si deux visages sont identiques. Les deux visages sont représentés sous forme d’ID de visages dans la requête. Les résultats vérifiés contiennent un champ booléen ([isIdentical](#Is-Identical)) indiquant « same » si la valeur est True, et un champ numérique ([confidence](#Confidence)) indiquant le niveau de confiance.

Pour plus d’informations, consultez le guide [Visage - Vérifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>O

## <a name="z"></a>Z
