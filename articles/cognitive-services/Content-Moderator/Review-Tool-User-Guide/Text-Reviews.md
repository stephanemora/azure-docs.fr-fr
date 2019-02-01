---
title: Révision de texte dans Azure Content Moderator | Content Moderator
description: Découvrez comment réviser du texte dans Content Moderator pour consulter le score et les balises détectées. Utilisez ces informations pour déterminer si le contenu est approprié.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219785"
---
# <a name="let-human-reviewers-review-text"></a>Confiez la révision du texte à des réviseurs humains

Vous pouvez utiliser Azure Content Moderator pour passer en revue du texte à l’aide des scores et des balises détectées. Utilisez ces informations pour déterminer si le contenu est approprié. 

## <a name="select-or-enter-the-text-to-review"></a>Sélectionner ou saisir le texte à réviser

Dans Content Moderator, sélectionnez l’onglet **Essayer**. Ensuite, sélectionnez l’option **Texte** pour accéder à l’écran de modération de texte. Saisissez quelques lignes ou soumettez l’exemple par défaut pour effectuer la modération de texte automatique. Le texte ne doit pas dépasser 1 024 caractères.

## <a name="get-ready-to-review-results"></a>Préparer la révision des résultats

Tout d’abord, l’outil de révision appelle l’API Modération de texte. Ensuite, il génère des révisions de texte à l’aide des balises détectées. L’outil de révision révèle les résultats sous forme de scores à l’attention de votre équipe.

## <a name="review-text-results"></a>Examiner les résultats du texte

Les résultats détaillés s’affichent dans les fenêtres. Les résultats comportent les balises détectées et les termes qui ont été renvoyés par l’API Modération de texte. Pour modifier l’état de la sélection d’une balise, sélectionnez la balise. Vous pouvez également travailler avec des balises personnalisées si vous en avez créé.

![Capture d’écran de l’outil de vérification montrant du texte marqué d’indicateurs dans une fenêtre du navigateur Chrome](../images/reviewresults_text.png)
