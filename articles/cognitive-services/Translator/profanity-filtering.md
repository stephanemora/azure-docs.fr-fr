---
title: Filtrage des grossièretés - API de traduction de texte Translator Text
titlesuffix: Azure Cognitive Services
description: Utilisez le filtrage des grossièretés dans l’API de traduction de texte Translator Text.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: ab54f34dbdbda5af94c6ebc2bc8c1818ebf3d601
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729880"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Ajouter un filtrage des grossièretés dans l’API de traduction de texte Translator Text

En règle générale, le service Translator conserve dans la traduction les termes vulgaires présents dans la source. Le degré de vulgarité et le contexte rendant le contenu vulgaire diffèrent en fonction des cultures. Par conséquent, le degré de vulgarité dans la langue cible peut être amplifié ou réduit.

Si vous souhaitez éviter toute grossièreté dans la traduction, même si le texte source en contient, utilisez l’option de filtrage des termes vulgaires disponible dans la méthode Translate(). Cette option vous permet de supprimer les termes vulgaires, de les marquer à l’aide de balises appropriées ou de n’appliquer aucune action.

La méthode Translate() utilise le paramètre « options » qui contient le nouvel élément « ProfanityAction ». Les valeurs acceptées pour ProfanityAction sont « NoAction », « Marked » et « Deleted ».

## <a name="accepted-values-of-profanityaction-and-examples"></a>Exemples et valeurs acceptées pour ProfanityAction
|Valeur pour ProfanityAction | Action | Exemple : Source - Japonais | Exemple : Cible - Anglais|
| :---|:---|:---|:---|
| NoAction | Par défaut. Équivaut à ne pas définir d’option. Les termes vulgaires de la source sont reflétés dans la cible. | 彼は変態です。 | C’est un con. |
| Marked | Les mots vulgaires sont entourés de balises XML \<profanity> … \</profanity>. | 彼は変態です。 | C’est un \<profanity>con\</profanity>. |
| Deleted | Les mots vulgaires sont supprimés de la cible et ne sont pas remplacés. | 彼は。 | C’est un. |

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Appliquer le filtrage des termes vulgaires avec votre appel d’API Translator](reference/v3-0-translate.md)
