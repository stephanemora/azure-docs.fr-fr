---
title: Concepts de reconnaissance faciale
titleSuffix: Azure Cognitive Services
description: Découvrez les concepts relatifs à la reconnaissance faciale.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: fa38c492530cb8938e49bc15e13fdd39ed5b6f1c
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890887"
---
# <a name="face-recognition-concepts"></a>Concepts de reconnaissance faciale
Cet article explique les concepts des opérations Verify, Find Similar, Group et Identify ainsi que les structures de données sous-jacentes. Globalement, la reconnaissance décrit l’opération consistant à comparer deux différents visages pour déterminer s’ils sont similaires ou appartiennent à la même personne.

## <a name="recognition-related-data-structures"></a>Structures de données liées à la reconnaissance

Les opérations de reconnaissance utilisent principalement les structures de données suivantes. Ces objets sont stockés dans le cloud et peuvent être référencés par leurs chaînes d’ID. Chaînes d’ID sont toujours uniques dans un abonnement. Champs de noms peuvent être dupliqués.

|Nom|Description |
|:--|:--|
|DetectedFace| Cette représentation face unique est récupérée par le [détection de visage](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) opération. Son ID expire 24 heures après sa création.|
|PersistedFace| Lorsque les objets de DetectedFace sont ajoutés à un groupe, tel que FaceList ou personne, ils deviennent des objets de PersistedFace. Ils peuvent être [récupérées](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) à n’importe quelle heure et n’expirent pas.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) ou [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Cette structure de données est une liste assortie d’objets de PersistedFace. Un FaceList a un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Cette structure de données est une liste d’objets PersistedFace qui appartiennent à la même personne. Il a un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur.|
|[Groupe de personnes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ou [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Cette structure de données est une liste assortie d’objets Person. Il a un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur. Un groupe de personnes doit être [formé](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) avant de pouvoir être utilisé dans les opérations de reconnaissance.|

## <a name="recognition-operations"></a>Opérations de reconnaissance

Cette section décrit en détail comment les quatre opérations de reconnaissance utilisent les structures de données décrits précédemment. Pour obtenir une description générale de chaque opération de reconnaissance, consultez [vue d’ensemble](../Overview.md).

### <a name="verify"></a>Vérifier

L’opération [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) prend un ID de visage de DetectedFace ou PersistedFace et un autre ID de visage ou un objet Person, et détermine s’ils appartiennent à la même personne. Si vous passez un objet Person, vous pouvez éventuellement passer un objet PersonGroup auquel cet objet Person appartient afin d’améliorer les performances.

### <a name="find-similar"></a>Rechercher semblables

L’opération [Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) prend un ID de visage de DetectedFace ou PersistedFace et un objet FaceList ou un tableau d’autres ID de visage. Avec un objet FaceList, elle renvoie un objet FaceList plus petit de visages similaires au visage donné. Avec un tableau d’ID de visage, elle renvoie également un tableau plus petit.

### <a name="group"></a>Groupe

L’opération [Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) accepte un tableau d’ID de visage assortis de DetectedFace ou PersistedFace et retourne les mêmes ID regroupés en plusieurs tableaux plus petits. Chaque tableau de « groupes » contient des ID de visage similaires. Un tableau unique « messyGroup » contient des ID de visage pour lesquels aucune ressemblance n’a été trouvée.

### <a name="identify"></a>Identifier

L’opération [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) prend un ou plusieurs ID de visage à partir de DetectedFace ou de PersistedFace ainsi qu’un groupe de personnes et retourne une liste d’objets Person auxquels chaque face peut appartenir. Les objets Person retournés sont empaquetés en tant qu’objets de candidats et comportent une valeur de niveau de confiance de prédiction.

## <a name="input-data"></a>Données d’entrée

Utilisez les conseils suivants pour vous assurer que vos images d’entrée donnent les résultats de reconnaissance plus précises :

* Les formats d’image d’entrée pris en charge sont JPEG, PNG, GIF (la première image), BMP.
* Taille du fichier image doit être non supérieure à 4 Mo.
* Lorsque vous créez des objets Person, utilisez des photos possédant différents types d’éclairage et des angles.
* Certains visages peuvent ne pas être reconnus en raison de défis techniques, telles que :
  * Les images d’éclairage extrêmes, par exemple, graves rétroéclairage.
  * Obstacles qui bloquent les yeux une ou les deux.
  * Différences dans les cheveux type ou faciale cheveux.
  * Modifications de l’apparence du visage en raison de l’âge.
  * Expressions du visage extrêmes.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les concepts de reconnaissance faciale, découvrez comment écrire un script qui identifie les visages par rapport à un groupe de personnes formé.

* [Identifiez les visages dans des images](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)
