---
title: Révision de texte dans Azure Content Moderator | Microsoft Docs
description: Découvrez comment réviser du texte dans Content Moderator pour consulter le score et les balises détectées. Utilisez ces informations pour déterminer si le contenu est approprié.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367932"
---
# <a name="review-text"></a>Réviser le texte

Vous pouvez utiliser Azure Content Moderator pour passer en revue du texte à l’aide des scores et des balises détectées. Utilisez ces informations pour déterminer si le contenu est approprié. 

## <a name="select-or-enter-the-text-to-review"></a>Sélectionner ou saisir le texte à réviser

Dans Content Moderator, sélectionnez l’onglet **Essayer**. Ensuite, sélectionnez l’option **Texte** pour accéder à l’écran de modération de texte. Saisissez quelques lignes ou soumettez l’exemple par défaut pour effectuer la modération de texte automatique. Le texte ne doit pas dépasser 1 024 caractères.

## <a name="get-ready-to-review-results"></a>Préparer la révision des résultats

Tout d’abord, l’outil de révision appelle l’API Modération de texte. Ensuite, il génère des révisions de texte à l’aide des balises détectées. L’outil de révision révèle les résultats sous forme de scores à l’attention de votre équipe.

## <a name="review-text-results"></a>Examiner les résultats du texte

Les résultats détaillés s’affichent dans les fenêtres. Les résultats comportent les balises détectées et les termes qui ont été renvoyés par l’API Modération de texte. Pour modifier l’état de la sélection d’une balise, sélectionnez la balise. Vous pouvez également travailler avec des balises personnalisées si vous en avez créé.

![Examiner les résultats du texte](images/3-review-text-2.png)
