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
ms.openlocfilehash: 87814571e6f1c20b219020651eb798fa49a28deb
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127926"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Ajouter un filtrage des grossièretés dans l’API de traduction de texte Translator Text

En règle générale, le service Translator conserve dans la traduction les termes vulgaires présents dans la source. Le degré de vulgarité et le contexte rendant le contenu vulgaire diffèrent en fonction des cultures. Par conséquent, le degré de vulgarité dans la langue cible peut être amplifié ou réduit.

Si vous souhaitez éviter toute grossièreté dans la traduction (même si le texte source en contient), utilisez une option de filtrage des termes vulgaires dans la méthode Translate(). Cette option vous permet, si vous le souhaitez, de supprimer les termes vulgaires ou de les marquer à l’aide de balises appropriées, sans qu’aucune action ne soit appliquée.

La méthode Translate() prend un paramètre « options » qui contient le nouvel élément « ProfanityAction ». Les valeurs acceptées pour ProfanityAction sont « NoAction », « Marked » et « Deleted ».

## <a name="accepted-values-of-profanityaction-and-examples"></a>Exemples et valeurs acceptées pour ProfanityAction
|Valeur pour ProfanityAction | Action | Exemple : Source - Japonais | Exemple : Cible - Français|
| :---|:---|:---|:---|
| NoAction | Par défaut. Équivaut à ne pas définir d’option. Les termes vulgaires de la source sont reflétés dans la cible. | 彼は変態です。 | C’est un con. |
| Marked | Les mots vulgaires sont entourés de balises XML \<profanity> … \</profanity>. | 彼は変態です。 | C’est un \<profanity>con\</profanity>. |
| Deleted | Les mots vulgaires sont supprimés de la cible et ne sont pas remplacés. | 彼は。 | C’est un. |

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Appliquer le filtrage des termes vulgaires avec votre appel d’API Translator](reference/v3-0-translate.md)

