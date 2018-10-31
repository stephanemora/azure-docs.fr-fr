---
title: Filtrage des grossièretés - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Utilisez le filtrage des grossièretés dans l’API de traduction de texte Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 4154950cf8d8b6ec2e47a9f8100cb7983ac127bf
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648035"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Ajouter un filtrage des grossièretés dans l’API de traduction de texte Translator Text

En règle générale, le service Translator conserve dans la traduction les termes vulgaires présents dans la source. Le degré de vulgarité et le contexte rendant le contenu vulgaire diffèrent en fonction des cultures. Par conséquent, le degré de vulgarité dans la langue cible peut être amplifié ou réduit.

Si vous souhaitez éviter toute grossièreté dans la traduction, même si le texte source en contient, utilisez l’option de filtrage des termes vulgaires disponible dans la méthode Translate(). Cette option vous permet de supprimer les termes vulgaires, de les marquer à l’aide de balises appropriées ou de n’appliquer aucune action.

La méthode Translate() utilise le paramètre « options » qui contient le nouvel élément « ProfanityAction ». Les valeurs acceptées pour ProfanityAction sont « NoAction », « Marked » et « Deleted ».

## <a name="accepted-values-of-profanityaction-and-examples"></a>Exemples et valeurs acceptées pour ProfanityAction
|Valeur pour ProfanityAction | Action | Exemple : Source - Japonais | Exemple : Cible - Français|
| :---|:---|:---|:---|
| NoAction | Par défaut. Équivaut à ne pas définir d’option. Les termes vulgaires de la source sont reflétés dans la cible. | 彼は変態です。 | C’est un con. |
| Marked | Les mots vulgaires sont entourés de balises XML \<profanity> … \</profanity>. | 彼は変態です。 | C’est un \<profanity>con\</profanity>. |
| Deleted | Les mots vulgaires sont supprimés de la cible et ne sont pas remplacés. | 彼は。 | C’est un. |

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Appliquer le filtrage des termes vulgaires avec votre appel d’API Translator](reference/v3-0-translate.md)
