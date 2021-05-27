---
title: Comment utiliser plusieurs emplacements avec Personalizer
description: Découvrez comment utiliser plusieurs emplacements avec Personalizer pour améliorer les recommandations de contenu fournies par le service.
services: cognitive-services
ms.author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: a7c7c1227e0704e77307dce12e0f7687fd9b9f54
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382224"
---
# <a name="get-started-with-multi-slot-for-azure-personalizer"></a>Prise en main de la fonctionnalité multi-emplacement pour Azure Personalizer

La personnalisation sur plusieurs emplacements (préversion) vous permet de cibler du contenu dans des présentations web, des carrousels et des listes où plusieurs actions (par exemple, un produit ou un élément de contenu) sont présentées à vos utilisateurs. Grâce aux API multi-emplacement de Personalizer, vous pouvez demander aux modèles d’IA de Personalizer d’apprendre quels sont les contextes d'utilisation et les produits qui suscitent certains comportements, en tenant compte du placement dans votre interface utilisateur et en en tirant des enseignements. Par exemple, Personalizer peut apprendre que certains produits ou contenus attirent davantage de clics dans une barre latérale ou un pied de page que dans la partie principale d’une page. 

Dans ce guide, vous allez apprendre à utiliser les API multi-emplacements de Personalizer.

::: zone pivot="programming-language-csharp"

[!INCLUDE [Try multi-slot with C#](./includes/quickstart-multislot-csharp.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [Try multi-slot with Node.js](./includes/quickstart-multislot-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Try multi-slot with Python](./includes/quickstart-multislot-python.md)]

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les emplacements multiples](concept-multi-slot-personalization.md)