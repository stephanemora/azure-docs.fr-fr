---
title: 'Description de contenu pour adultes et choquant : Vision par ordinateur'
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection de contenu pour adultes et choquant dans les images à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7f343f22eb881cf58a024f8464ffd0d615fe06e8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158891"
---
# <a name="detecting-adult-and-racy-content"></a>Détection du contenu pour adultes et osé

Les différentes catégories visuelles comptent également le groupe pour le contenu suggestif et/ou réservé aux adultes qui permet de détecter des sujets pour adultes et restreint l’affichage des images à caractère sexuel. Le filtre pour la détection de contenu suggestif et réservé aux adultes peut être défini sur une échelle variable pour s’adapter aux préférences de l’utilisateur.

## <a name="defining-adult-and-racy-content"></a>Définition du contenu pour adultes et choquant

Parmi les différentes fonctionnalités visuelles couvertes par la [méthode Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), la fonctionnalité visuelle Adulte active la détection des images pour adultes et choquantes. Par définition, les images « pour adultes » sont de nature pornographique, et représentent souvent actes sexuels et nudité. Par définition, des images « choquantes » sont de nature sexuellement suggestive et contiennent souvent moins de contenu sexuellement explicite que les images marquées comme étant « pour adultes ». Le type de fonctionnalité visuelle Adulte est couramment utilisé pour restreindre l’affichage des images à contenu sexuellement suggestif et explicitement sexuel.

## <a name="identifying-adult-and-racy-content"></a>Identification du contenu pour adultes et choquant

La méthode Analyze Image retourne deux propriétés, `isAdultContent` et `isRacyContent`, dans la réponse JSON de la méthode pour indiquer respectivement du contenu pour adultes et choquant. Les deux propriétés retournent une valeur booléenne, true ou false. Cette méthode retourne également deux propriétés, `adultScore` et `racyScore`, qui représentent respectivement les scores de confiance permettant d’identifier du contenu pour adultes et choquant. Un filtre de confiance pour la détection de contenu pour adultes et choquant peut être défini sur une échelle variable afin de répondre à votre préférence en fonction de votre scénario spécifique.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les concepts concernant la [détection du contenu spécifique à un domaine](concept-detecting-domain-content.md) et la [détection des visages](concept-detecting-faces.md).
