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
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64415998"
---
# <a name="face-recognition-concepts"></a>Concepts de reconnaissance faciale

Cet article explique les concepts de diverses opérations de reconnaissance faciale (vérification, recherchez similaire, regroupement, d’identification) et les structures de données sous-jacente. Globalement, reconnaissance décrit le travail de la comparaison des deux visages différents pour déterminer si elles sont similaires ou appartenir à la même personne.

## <a name="recognition-related-data-structures"></a>Structures de données liées à la reconnaissance

Les opérations de reconnaissance utilisent principalement les structures de données suivantes. Ces objets sont stockés dans le cloud et peuvent être référencés par leurs chaînes d’ID. Chaînes d’ID sont donc toujours uniques dans un abonnement, tandis que les champs de noms peuvent être dupliqués.

|Nom|Description|
|:--|:--|
|**DetectedFace**| Il s’agit d’une représentation unique face récupérée par le [détection de visage](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) opération. Son ID expire 24 heures après sa création.|
|**PersistedFace**| Lorsque **DetectedFace** objets sont ajoutés à un groupe (tel que **FaceList** ou **personne**), qu’ils deviennent **PersistedFace** objets qui peuvent être [récupérées](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) à n’importe quelle heure et n’expirent pas.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Il s’agit d’une liste assortie de **PersistedFace** objets. Un **FaceList** possède un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur.|
|**[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Il s’agit d’une liste de **PersistedFace** objets qui appartiennent à la même personne. Il a un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Il s’agit d’une liste assortie de **personne** objets. Il a un ID unique, une chaîne de nom et éventuellement une chaîne de données utilisateur. Un **groupe de personnes** doit être [formé](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) avant de pouvoir être utilisé dans les opérations de reconnaissance.|

## <a name="recognition-operations"></a>Opérations de reconnaissance

Cette section décrit en détail comment les quatre opérations de reconnaissance utilisent les structures de données ci-dessus. Consultez le [vue d’ensemble](../Overview.md) pour obtenir une description générale de chaque opération de reconnaissance.

### <a name="verification"></a>Vérification

Le [Vérifiez](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) opération accepte un ID de visage (**DetectedFace** ou **PersistedFace**) et soit un autre face ID ou un **personne** objet et Détermine s’ils appartiennent à la même personne. Si vous passez un **personne**, vous pouvez éventuellement passer une **groupe de personnes** sur lesquels elle **personne** appartient afin d’améliorer les performances.

### <a name="find-similar"></a>Rechercher des éléments semblables

Le [Rechercher similaire](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) opération accepte un ID de visage (**DetectedFace** ou **PersistedFace**) et soit un **FaceList** ou un tableau d’autres face ID. Avec un **FaceList**, elle retourne un plus petit **FaceList** des faces qui sont similaires à la face donnée. Avec un tableau des ID de visage, il retourne de même un tableau plus petit.

### <a name="grouping"></a>Regroupement

Le [groupe](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) opération accepte un tableau des ID de visage assortie (**DetectedFace** ou **PersistedFace**) et retourne les mêmes ID regroupement en plusieurs tableaux plus petits. Chaque tableau de « groupes » contient face ID ressembler, et un tableau unique « messyGroup » contient face ID pour lequel aucune ressemblances ont été trouvés.

### <a name="identification"></a>Identification

Le [identifier](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) opération prend un ou plusieurs ID de visage (**DetectedFace** ou **PersistedFace**) et un **groupe de personnes** et retourne une liste de **personne** objets chaque face peut appartenir à. Retourné **personne** objets sont encapsulées comme **Candidate** objets, qui ont une valeur de niveau de confiance de prédiction.

## <a name="input-data"></a>Données d’entrée

Utilisez les conseils suivants pour vous assurer que vos images d’entrée donnent les résultats de reconnaissance plus précises :

* Les formats d’image d’entrée pris en charge sont JPEG, PNG, GIF (la première image), BMP.
* Taille du fichier image doit être non supérieure à 4 Mo.
* Lors de la création **personne** objets, vous devez utiliser des photos qui comportent une grande variété des angles d’éclairage.
* Certains visages ne soient pas reconnus en raison de défis techniques :
  * Images avec éclairage extrême (par exemple, graves rétroéclairage).
  * Obstructions en matière de blocage des yeux une ou les deux.
  * Différences dans les cheveux style ou faciale cheveux.
  * Nouveautés d’apparence face en raison de l’âge.
  * Expressions du visage extrêmes.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les concepts de reconnaissance faciale, découvrez comment écrire un script simple qui identifie les visages contre un formé **groupe de personnes**.

* [Comment identifier des visages dans des images](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)