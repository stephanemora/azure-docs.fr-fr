---
title: Transformation de recherche dans le flux de données de mappage
description: Référencez des données à partir d’une autre source à l’aide de la transformation de recherche dans le flux de données de mappage.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 08ad231b462e21fefebacd4e280bfb2b13bc0eef
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413642"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformation de recherche dans le flux de données de mappage

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Utilisez la transformation de recherche pour référencer des données provenant d’une autre source dans un flux de données. La transformation de recherche ajoute des colonnes de données mises en correspondance à vos données sources.

Une transformation de recherche est similaire à une jointure externe gauche. Toutes les lignes du flux principal existent dans le flux de sortie avec des colonnes supplémentaires venant du flux de recherche. 

## <a name="configuration"></a>Configuration

![Transformation de recherche](media/data-flow/lookup1.png "Recherche")

**Flux principal :** Le flux de données entrant. Ce flux est équivalent au côté gauche d’une jointure.

**Flux de recherche :** Les données ajoutées au flux de données principal. Les données ajoutées sont déterminées par les conditions de recherche. Ce flux est équivalent au côté droit d’une jointure.

**Correspond à plusieurs lignes :** Si cette option est activée, une ligne avec plusieurs correspondances dans le flux principal retourne plusieurs lignes. Dans le cas contraire, une seule ligne est retournée en fonction de la condition ’Match on'.

**Correspond à :** Visible uniquement si « faire correspondre plusieurs lignes » est activé. Choisissez s’il faut faire correspondre sur n’importe quelle ligne, sur la première correspondance ou sur la dernière correspondance. Faire correspondre sur n’importe quelle ligne est recommandé, car c’est l’option qui s’exécute le plus rapidement. Si la première ligne ou la dernière ligne est sélectionnée, vous devez spécifier des conditions de tri.

**Conditions de recherche :** Choisissez les colonnes à faire correspondre. Si la condition d’égalité est remplie, les lignes sont considérées comme une correspondance. Pointez et sélectionnez « Colonne calculée » pour extraire une valeur à l’aide du [Langage d’expression des flux de données](data-flow-expression-functions.md).

La transformation de recherche prend uniquement en charge les correspondances d’égalité. Pour personnaliser l’expression de recherche afin d’inclure d’autres opérateurs tels que supérieur à, il est recommandé d’utiliser une [jointure croisée dans la transformation de jointure](data-flow-join.md#custom-cross-join). Avec une jointure croisée, vous éviterez ainsi les erreurs de produit cartésien lors de l’exécution.

Toutes les colonnes des deux flux sont incluses dans les données de sortie. Pour supprimer les colonnes en double ou indésirables, ajoutez une [transformation de sélection](data-flow-select.md) après votre transformation de recherche. Les colonnes peuvent également être supprimées ou renommées dans une transformation de récepteur.

## <a name="analyzing-matched-rows"></a>Analyse des lignes correspondantes

Après la transformation de recherche, la fonction `isMatch()` peut être utilisée pour déterminer si la recherche correspond à des lignes individuelles.

![Modèle de recherche](media/data-flow/lookup111.png "Modèle de recherche")

Un exemple de ce modèle consiste à utiliser la transformation de fractionnement conditionnel pour fractionner sur la fonction `isMatch()`. Dans l’exemple ci-dessus, les lignes correspondantes passent par le flux principal, et les lignes sans correspondance transitent par le flux ```NoMatch```.

## <a name="testing-lookup-conditions"></a>Test des conditions de recherche

Lorsque vous testez la transformation de recherche avec l’aperçu des données en mode débogage, utilisez un jeu de données connues peu volumineux. Lors de l’échantillonnage de lignes à partir d’un jeu de données volumineux, vous ne pouvez pas prédire quelles lignes et quelles clés seront lues dans le cadre du test. Le résultat n’est pas déterministe, ce qui signifie que vos conditions de jointure peuvent ne renvoyer aucun résultat.

## <a name="broadcast-optimization"></a>Optimisation de la diffusion

Dans Azure Data Factory, les mappages de flux de données s’exécutent dans des environnements à grande échelle Spark. Si votre jeu de données peut tenir dans l’espace mémoire de nœud Worker, vous pouvez optimiser les performances de vos recherches en activant la diffusion.

![Jonction de diffusion](media/data-flow/broadcast.png "Jonction de diffusion")

L’activation de la diffusion pousse l’ensemble du jeu de données en mémoire. Pour les jeux de données plus petits contenant uniquement quelques milliers de lignes, la diffusion peut améliorer les performances de recherche. Pour les jeux de données volumineux, cette option peut entraîner une exception de mémoire insuffisante.

## <a name="data-flow-script"></a>Script de flux de données

### <a name="syntax"></a>Syntaxe

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Exemple

![Transformation de recherche](media/data-flow/lookup-dsl-example.png "Recherche")

Le script de transmission de données pour la configuration de recherche ci-dessus se trouve dans l’extrait de code suivant.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Étapes suivantes

* Les transformations [join](data-flow-join.md) et [exists](data-flow-exists.md) prennent toutes les deux plusieurs entrées de flux
* Utiliser une [transformation de fractionnement conditionnel](data-flow-conditional-split.md) avec ```isMatch()``` pour fractionner des lignes sur des valeurs correspondantes et non correspondantes
