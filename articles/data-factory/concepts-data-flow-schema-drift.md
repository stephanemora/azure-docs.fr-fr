---
title: Dérive de schéma de la fonctionnalité de mappage de flux de données dans Azure Data Factory
description: Construire des flux de données résilients dans Azure Data Factory avec dérive de schéma
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 562daa024985a546ffb49c4da11eace3bc81a659
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314820"
---
# <a name="mapping-data-flow-schema-drift"></a>Dérive de schéma de mappage de flux de données

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Il y a dérive de schéma lorsque les métadonnées de vos sources sont fréquemment mises à jour. Les champs, colonnes, types, etc. peuvent être ajoutés, supprimés ou modifiés à la volée. Sans la gestion de la dérive des schémas, votre flux de données devient vulnérable aux modifications apportées aux sources de données en amont. En cas de modification des colonnes et des champs entrants, les modèles ETL typiques échouent car ils sont généralement liés à ces noms de source.

Afin de vous protéger contre la dérive de schéma, il est important de disposer d’un outil Data Flow vous permettant, en tant qu’ingénieur de données, de :

* Définir les sources dont les noms de champs, les types de données, les valeurs et les tailles sont modifiables
* Définir des paramètres de transformation qui peuvent fonctionner avec des modèles de données et non avec des champs et des valeurs codés en dur
* Définir des expressions qui comprennent les modèles pour correspondre aux champs entrants, au lieu d’utiliser des champs nommés

## <a name="how-to-implement-schema-drift-in-adf-mapping-data-flows"></a>Comment implémenter la dérive de schéma dans fonctionnalité de mappage de flux de données d’Azure Data Factory
Azure Data Factory prend en charge en mode natif des schémas flexibles qui passent d’une exécution à une autre afin que vous puissiez générer une logique de transformation de données générique sanscdevoir recompiler vos flux de données.

* Choisissez « Allow Schema Drift » (« Autoriser la dérive de schéma ») dans votre transformation de la source

<img src="media/data-flow/schemadrift001.png" width="400">

* Lorsque vous avez sélectionné cette option, tous les champs entrants sont lus dans votre source à chaque exécution du flux de données et transmis au récepteur dans tout le flux.

* Par défaut, toutes les colonnes nouvellement détectées (colonnes dérivées) arrivent en tant que type de données chaîne. Dans votre transformation source, choisissez « Infer drifted column types » (Inférer les types de colonnes dérivés) si vous souhaitez qu’Azure Data Factory infère automatiquement les types de données à partir de la source.

* Veillez à utiliser « Auto-Map » (Mappage automatique) pour mapper tous les nouveaux champs dans la transformation du récepteur afin que ceux-ci soient sélectionnés et dirigés vers votre destination, et définissez également « Allow Schema Drift » (Autoriser la dérive de schéma) sur le Récepteur.

<img src="media/data-flow/automap.png" width="400">

* Tout fonctionnera lorsque de nouveaux champs seront introduits dans ce scénario avec un simple mappage de source à récepteur (également appelé Copie).

* Pour ajouter des transformations dans le flux de travail qui gère la dérive de schéma, vous pouvez utiliser la correspondance de modèles pour faire correspondre les colonnes par nom, type et valeur.

* Cliquez sur « Add Column Pattern » (« Ajouter un modèle de colonne ») dans la transformation « Colonne dérivée » ou « Agrégat » si vous souhaitez créer une transformation qui comprend la « dérive de schéma ».

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> Vous devez prendre une décision relative à l’architecture de votre flux de données pour accepter la dérive de schéma dans celui-ci. Lorsque vous effectuez cette opération, vous pouvez vous protéger contre les modifications de schéma à partir des sources. Toutefois, vous allez perdre la liaison anticipée de vos colonnes et les types tout au long de votre flux de données. Azure Data Factory traite les flux d’une dérive de schéma en tant que flux de la liaison tardive ; par conséquent, lorsque vous générez vos transformations, les noms de colonnes ne seront pas disponibles dans les affichages de schémas dans le flux.

<img src="media/data-flow/taxidrift1.png" width="400">

L’exemple Data Flow Taxi Demo comporte un exemple de dérive de schéma dans le flux de données inférieur avec la source TripFare. Dans la transformation d’agrégat, notez que nous utilisons la conception « modèle de colonnes » pour les champs d’agrégation. Au lieu de nommer des colonnes spécifiques ou de rechercher des colonnes par position, nous supposons que les données peuvent être modifiées et ne pas apparaître dans le même ordre d’une exécution à l’autre.

Dans cet exemple de gestion de dérive de schéma de flux de données Azure Data Factory, nous avons construit une agrégation qui analyse les colonnes de type « double », sachant que le domaine de données contient les prix pour chaque course. Nous pouvons alors effectuer un calcul mathématique agrégé pour tous les champs doubles de la source, indépendamment de l’emplacement et du nom de la colonne.

La syntaxe de flux de données Azure Data Factory utilise $$ pour représenter chaque colonne correspondante dans votre modèle. Vous pouvez également rechercher des noms de colonnes à l’aide de fonctions complexes de recherche de chaînes de caractères et d’expressions régulières. Dans ce cas, nous allons créer un nouveau nom de champ agrégé basé sur chaque correspondance d’une colonne de type « double » et ajouter le texte ```_total``` à chacun de ces noms correspondants : 

```concat($$, '_total')```

Ensuite, nous arrondirons et additionnerons les valeurs pour chacune de ces colonnes correspondantes :

```round(sum ($$))```

Vous pouvez tester cela avec l’exemple Data Flow « Taxi Demo » d’Azure Data Factory. Activez la session de débogage à l’aide du bouton Déboguer en haut de la surface de conception du flux de données pour afficher vos résultats de manière interactive :

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>Accéder aux nouvelles colonnes en aval
Quand vous générez de nouvelles colonnes avec des modèles de colonnes, vous pouvez y accéder par la suite dans vos transformations de flux de données à l’aide des méthodes suivantes :

* Utilisez « byPosition » pour identifier les nouvelles colonnes par leur numéro de position.
* Utilisez « byName » pour identifier les nouvelles colonnes par leur nom.
* Dans les modèles de colonne, utilisez les paramètres « Name » (Nom), « Stream » (Flux), « Position » ou « Type », ou n’importe quelle combinaison de ceux-ci pour identifier de nouvelles colonnes.

## <a name="next-steps"></a>Étapes suivantes
Dans le [langage d’expression de flux de données](data-flow-expression-functions.md), vous trouverez des fonctionnalités supplémentaires pour les modèles de colonnes et la dérive de schéma, notamment « byName » et « byPosition ».
