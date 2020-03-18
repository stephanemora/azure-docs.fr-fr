---
title: Transformation de sélection de flux de données de mappage
description: Transformation de sélection (Select) de mappage de Data Flow pour Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/08/2020
ms.openlocfilehash: 2d04de420f743e4fef4cff4bd2912559dae0886a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78934175"
---
# <a name="mapping-data-flow-select-transformation"></a>Transformation de sélection de mappage de flux de données


Utilisez cette transformation pour la sélectivité de colonne (en réduisant le nombre de colonnes), pour attribuer des alias aux noms de colonnes et de flux et pour réorganiser les colonnes.

## <a name="how-to-use-select-transformation"></a>Guide pratique pour l’utilisation des transformations
La transformation de sélection vous permet d’attribuer un alias à l’intégralité d’un flux, ou à des colonnes dans ce flux, d’attribuer des noms différents (alias), puis de référencer ces nouveaux noms ultérieurement dans votre flux de données. Cette transformation est utile pour les scénarios de jointure réflexive. La façon d’implémenter une jointure réflexive dans ADF Data Flow consiste à prendre un flux, d’en créer une nouvelle branche avec « Nouvelle branche », et immédiatement après, d’ajouter une transformation « Select ». Ce flux de données a maintenant un nouveau nom que vous pouvez utiliser pour la jointure au flux d’origine, créant ainsi une jointure réflexive :

![Jointure réflexive](media/data-flow/selfjoin.png "Jointure réflexive")

Dans le diagramme ci-dessus, la transformation de sélection (Select) se trouve en haut. Cela applique l’alias « OrigSourceBatting » au flux d’origine. Dans la transformation de jointure (Join) mise en évidence en dessous, vous pouvez voir que nous utilisons ce flux d’alias de sélection (Select) en tant que jointure de droite, ce qui nous permet de faire référence à la même clé du côté gauche et du côté droit de la jointure interne.

Le flux Select peut également être utilisé comme un moyen de désélectionner les colonnes à partir de votre flux de données. Par exemple, si vous avez 6 colonnes définies dans votre récepteur, mais que vous souhaitez choisir uniquement 3 colonnes spécifiques à transformer et à transmettre au récepteur, vous pouvez sélectionner uniquement ces 3 colonnes à l’aide de la transformation de sélection.

![Transformation de sélection](media/data-flow/newselect1.png "Sélectionner un alias")

## <a name="options"></a>Options
* Le paramètre par défaut pour « Sélectionner » consiste à inclure toutes les colonnes d’entrée et de conserver ces noms d’origine. Vous pouvez appliquer un alias au flux de données en définissant le nom de la transformation de sélection (Select).
* Pour appliquer des alias à des colonnes individuelles, désactivez l’option « Sélectionner tout » et utilisez le mappage de colonnes en bas.
* Choisissez Ignorer les doublons pour éliminer les colonnes dupliquées des métadonnées d’entrée ou de sortie.

![Ignorer les doublons](media/data-flow/select-skip-dup.png "Ignorer les doublons")

* Lorsque vous choisissez d’ignorer les doublons, les résultats sont visibles sous l’onglet Inspection. ADF conservera la première occurrence de la colonne et vous verrez que chaque occurrence suivante de cette même colonne a été supprimée de votre flux.

> [!NOTE]
> Pour effacer les règles de mappage, cliquez sur le bouton **Réinitialiser**.

## <a name="mapping"></a>Mappage
Par défaut, la transformation de sélection mappe automatiquement toutes les colonnes, ce qui permet de transmettre toutes les colonnes entrantes au même nom sur les données de sortie. Le nom du flux de sortie configuré dans Paramètres Select définit un nouveau nom d’alias pour le flux. Si vous conservez l’ensemble Select pour le mappage automatique, vous pouvez créer un alias de l’ensemble du flux avec toutes les colonnes identiques.

![Règles de transformation de sélection](media/data-flow/rule2.png "Mappage basé sur des règles")

Si vous souhaitez créer un alias, supprimer, renommer ou réorganiser des colonnes, vous devez d’abord désactiver l’option « auto-map » (mappage automatique). Par défaut, vous verrez un filtre par défaut entré pour vous, appelé « All input columns » (toutes les colonnes d’entrée). Vous pouvez laisser ce filtre si vous souhaitez toujours autoriser toutes les colonnes entrantes à mapper pour avoir le même nom à la sortie des données.

Toutefois, si vous souhaitez ajouter des filtres personnalisés, vous devez cliquer sur « Add mapping » (ajouter un mappage). Le mappage de champs vous fournit une liste de noms de colonnes entrantes et sortantes à mapper ainsi que des alias. Choisissez « Mappage basé sur des règles » pour créer des règles de critères spéciaux.

## <a name="rule-based-mapping"></a>Mappage basé sur des règles
Lorsque vous choisissez le mappage basé sur des règles, vous demandez à ADF d’évaluer votre expression correspondante pour qu’elle corresponde aux filtres de modèle entrants et de définir les noms de champs sortants. Vous pouvez ajouter n’importe quelle combinaison de mappage de champs et de mappage basé sur des règles. Les noms de champs sont ensuite générés au moment de l’exécution par ADF en fonction des métadonnées entrantes de la source. Vous pouvez afficher les noms des champs générés pendant le débogage et à l’aide du volet d'aperçu des données.

Vous trouverez plus d’informations sur les critères spéciaux [dans la documentation du modèle de colonne](concepts-data-flow-column-pattern.md).

### <a name="use-rule-based-mapping-to-parameterize-the-select-transformation"></a>Utiliser le mappage basé sur des règles pour paramétrer la transformation de sélection
Vous pouvez paramétrer le mappage de champs dans la transformation de sélection en utilisant un mappage basé sur des règles. Utilisez le mot clé ```name``` pour vérifier les noms de colonnes entrantes par rapport à un paramètre. Par exemple, si vous avez un paramètre de flux de données nommé ```mycolumn```, vous pouvez créer une règle de transformation de sélection unique qui mappe toujours le nom de colonne que vous avez défini ```mycolumn``` à un nom de champ de cette façon :

```name == $mycolumn```

## <a name="next-steps"></a>Étapes suivantes
* Après l’utilisation de Select pour renommer, réorganiser et attribuer des alias aux colonnes, utilisez la [transformation de récepteur Sink](data-flow-sink.md) pour placer vos données dans un magasin de données.
