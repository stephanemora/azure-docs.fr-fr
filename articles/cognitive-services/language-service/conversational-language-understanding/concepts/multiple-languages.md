---
title: Projets multilingues
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser des projets multilingues dans Compréhension du langage courant
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4bbad9e51e26d643ff121e9b80b5da3fbafd89b9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096739"
---
# <a name="multilingual-projects"></a>Projets multilingues

Compréhension du langage courant vous permet d’étendre facilement votre projet à plusieurs langues en même temps. Quand vous activez plusieurs langues dans des projets, vous pouvez ajouter des énoncés et des synonymes spécifiques à la langue à votre projet, et obtenir des prédictions multilingues pour vos intentions et vos entités. 

## <a name="multilingual-intent-and-learned-entity-components"></a>Composants des intentions et des entités apprises multilingues

Quand vous activez plusieurs langues dans un projet, vous pouvez entraîner le projet principalement dans une langue et obtenir immédiatement des prédictions dans d’autres langues. 

Par exemple, vous pouvez entraîner entièrement votre projet avec des énoncés en anglais, et l’interroger en français, allemand, mandarin, japonais, coréen et d’autres langues. Compréhension du langage courant vous permet de mettre facilement à l’échelle vos projets en plusieurs langues en utilisant la technologie multilingue pour entraîner vos modèles.

Chaque fois que vous identifiez qu’une langue particulière ne fonctionne pas aussi bien que les autres langues, vous pouvez ajouter des énoncés pour cette langue dans votre projet. Dans la page [Étiqueter des énoncés](../how-to/tag-utterances.md) de Language Studio, vous pouvez sélectionner la langue de l’énoncé que vous ajoutez. Quand vous introduisez des exemples pour cette langue dans le modèle, il reçoit davantage de syntaxe de cette langue et apprend à mieux la prédire.

Vous ne devez pas nécessairement ajouter la même quantité d’énoncés pour chaque langue. Vous devez créer la majeure partie de votre projet dans une seule langue et ajouter seulement quelques énoncés dans les langues dont vous observez qu’elles ne fonctionnent pas bien. Si vous créez un projet qui est principalement en anglais et que vous commencez à le tester en français, en allemand et en espagnol, vous constaterez peut-être que l’allemand ne fonctionne pas aussi bien que les deux autres langues. Dans ce cas, envisagez d’ajouter 5 % de vos exemples anglais d’origine en allemand, d’entraîner un nouveau modèle et de tester à nouveau l’allemand. Vous devez normalement obtenir de meilleurs résultats pour les requêtes en allemand. Plus vous ajoutez d’énoncés, plus les résultats sont susceptibles d’être améliorés. 

Quand vous ajoutez des données dans une autre langue, vous ne devez pas redouter qu’elles aient une incidence négative sur les autres langues. 

## <a name="list-and-prebuilt-components-in-multiple-languages"></a>Composants de liste et composants prédéfinis dans plusieurs langues

Les projets pour lesquels plusieurs langues sont activées vous permettent de spécifier des synonymes **par langue** pour chaque clé de liste. Selon la langue avec laquelle vous interrogez votre projet, vous allez obtenir les correspondances seulement pour le composant de liste avec des synonymes de cette langue. Quand vous interrogez votre projet, vous pouvez spécifier la langue dans le corps de la demande :

```json
"query": "{query}"
"language": "{language code}"
```

Si vous ne fournissez pas de langue, c’est la langue par défaut de votre projet qui est utilisée. Consultez l’article sur la [prise en charge des langues](../language-support.md) pour obtenir la liste des différents codes de langue.

Les composants prédéfinis sont similaires, où vous devez vous attendre à obtenir des prédictions pour les composants prédéfinis qui sont disponibles dans des langues spécifiques. La langue de la demande détermine ici aussi les composants qui tentent d’être prédits. Consultez l’article de référence sur les [composants prédéfinis](../prebuilt-component-reference.md) pour la prise en charge des langues de chaque composant prédéfini.

## <a name="next-steps"></a>Étapes suivantes

* [Étiqueter les énoncés](../how-to/tag-utterances.md) 
* [Entraîner un modèle](../how-to/train-model.md)
