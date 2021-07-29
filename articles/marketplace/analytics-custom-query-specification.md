---
title: Spécification de requête personnalisée
description: Découvrez comment utiliser des requêtes personnalisées pour extraire par programme des données à partir de tables analytiques pour vos offres sur la place de marché commerciale Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: b82fb574b134065f3c0f1a7dc5a4742258914ff6
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112017412"
---
# <a name="custom-query-specification"></a>Spécification de requête personnalisée

Les partenaires peuvent utiliser cette spécification de requête pour facilement formuler des requêtes personnalisées à des fins d’extraction de données à partir de tables analytiques. Les requêtes peuvent être utilisées pour sélectionner uniquement les colonnes et les métriques souhaitées correspondant à un critère spécifique. La spécification du langage repose sur la définition du jeu de données sur laquelle une requête personnalisée peut être écrite.

## <a name="datasets"></a>Groupes de données

De la même façon que certaines requêtes sont exécutées sur une base de données dotée de tables et de colonnes, une requête personnalisée fonctionne sur des jeux de données dotés de colonnes et de métriques. La liste complète des jeux de données disponibles pour formuler une requête peut être obtenue à l’aide de l’API des jeux de données.

Voici un exemple de jeu de données affiché sous forme de JSON.

```json
{
     "datasetName": "ISVUsage",
     "selectableColumns": [
       "MarketplaceSubscriptionId",
       "OfferName",
            "CustomerId",
     "MonthStartDate",
     "SKU"
    ],
    "availableMetrics": [
     "NormalizedUsage", 
     "RawUsage"
        "EstimatedExtendedChargeCC"
    ],
    "availableDateRanges": [
     "LAST_MONTH",
     "LAST_3_MONTHS",
     "LAST_6_MONTHS",
     "LIFETIME"
    ]
}
```

### <a name="parts-of-a-dataset"></a>Parties d’un jeu de données

- Un nom de jeu de données est similaire à un nom de table de base de données. Par exemple, ISVUsage. Un jeu de données contient une liste de colonnes qui peuvent être sélectionnées, par exemple MarketplaceSubscriptionId.
- Un jeu de données possède également des métriques, qui s’apparentent aux fonctions d’agrégation d’une base de données. Par exemple, NormalizedUsage.
- Il existe des intervalles de temps fixes au cours desquels les données peuvent être exportées.

### <a name="formulating-a-query-on-a-dataset"></a>Formulation d’une requête sur un jeu de données

Voici quelques exemples de requêtes illustrant comment extraire différents types de données.

| Requête | Description |
| ------------ | ------------- |
| **SELECT** MarketplaceSubscriptionId,CustomerId **FROM** ISVUsage **TIMESPAN LAST_MONTH** | Cette requête permet d’obtenir chaque `MarketplaceSubscriptionId` et son `CustomerId` correspondant au cours du mois passé. |
| **SELECT** MarketplaceSubscriptionId, EstimatedExtendedChargeCC **FROM** ISVUsage **ORDER BY** EstimatedExtendedChargeCC **LIMIT** 10 | Cette requête permet d’obtenir les 10 premiers abonnements dans l’ordre décroissant du nombre de licences vendues dans chaque abonnement. |
| **SELECT** CustomerId, NormalizedUsage, RawUsage **FROM** ISVUsage **WHERE** NormalizedUsage > 100000 **ORDER BY** NormalizedUsage **TIMESPAN** LAST_6_MONTHS | Cette requête permet d’obtenir les valeurs NormalizedUsage et RawUsage de tous les clients dont la valeur NormalizedUsage est supérieure à 100 000. |
| **SELECT** MarketplaceSubscriptionId, MonthStartDate, NormalizedUsage **FROM** ISVUsage **WHERE** CustomerId IN (‘2a31c234-1f4e-4c60-909e-76d234f93161’, ‘80780748-3f9a-11eb-b378-0242ac130002’) | Cette requête permet d’obtenir le `MarketplaceSubscriptionId` et l’utilisation normalisée générée mensuellement à l’aide des deux valeurs `CustomerId` suivantes : `2a31c234-1f4e-4c60-909e-76d234f93161` et `80780748-3f9a-11eb-b378-0242ac130002`. |
|||

## <a name="query-specification"></a>Spécification de requête

Cette section décrit la définition et la structure de la requête.

### <a name="grammar-reference"></a>Référence de grammaire

Ce tableau décrit les symboles utilisés dans les requêtes.

| Symbole | Signification |
| ------------ | ------------- |
| ? | Facultatif |
| * | Zéro ou plus |
| + | Un ou plus |
| &#124; | Ou/Un des éléments de la liste |
| | |

### <a name="query-definition"></a>Définition de la requête

L’instruction de requête contient les clauses suivantes : SelectClause, FromClause, WhereClause?, OrderClause?, LimitClause?, and TimeSpan?.

- **SelectClause**: **SELECT** ColumOrMetricName (, ColumOrMetricName)*
    - **ColumOrMetricName** : colonnes et métriques définies dans le jeu de données
- **FromClause**: **FROM** DatasetName
    - **DatasetName**: nom du jeu de données défini dans le jeu de données
- **WhereClause**: **WHERE** FilterCondition (**AND** FilterCondition)*
    - **FilterCondition**: valeur de l’opérateur ColumOrMetricName
        - **Opérateur** :  = | > | < | >= | <= | != | LIKE | NOT LIKE | IN | NOT IN
        - **Valeur** : nombre | StringLiteral | MultiNumberList | MultiStringList 
            - **Nombre** : -? [0-9]+ (. [0-9] [0-9]*)?
            - **StringLiteral**:  ' [a-zA-Z0-9_]*'
            - **MultiNumberList**: (Number  (,Number)*)
            - **MultiStringList**: (StringLiteral (,StringLiteral)*)
- **OrderClause**: **ORDER BY** OrderCondition (,OrderCondition)*
    - **OrderCondition**: ColumOrMetricName (**ASC** | **DESC**)*
    - **LimitClause**: **LIMIT** [0-9]+
    - **TimeSpan**: **TIMESPAN** ( TODAY | YESTERDAY | LAST_7_DAYS | LAST_14_DAYS | LAST_30_DAYS | LAST_90_DAYS | LAST_180_DAYS | LAST_365_DAYS | LAST_MONTH | LAST_3_MONTHS | LAST_6_MONTHS | LAST_1_YEAR | LIFETIME)

### <a name="query-structure"></a>Structure de la requête

Une requête de rapport est composée de plusieurs parties :

- SELECT
- FROM
- WHERE
- ORDER BY
- LIMIT
- TIMESPAN

Chaque partie est décrite ci-dessous.

#### <a name="select"></a>SELECT

Cette partie de la requête spécifie les colonnes qui seront exportées. Les colonnes pouvant être sélectionnées correspondent aux champs répertoriés dans les sections `selectableColumns` et `availableMetrics` d’un jeu de données. Si une colonne de métrique est incluse dans la liste des champs sélectionnés, les métriques sont calculées pour chaque combinaison distincte des colonnes non métriques. 

**Exemple** :
- **SELECT** `OfferName`, `NormalizedUsage`

#### <a name="distinct"></a>DISTINCT

L’ajout du mot clé DISTINCT après SELECT garantit que les données exportées finales n’ont pas de lignes en double. Le mot clé DISTINCT fonctionne indépendamment du fait qu’une colonne métrique soit sélectionnée ou non.

**Exemple** :
- **SELECT DISTINCT** `MarketplaceSubscriptionId, OfferType`

#### <a name="from"></a>FROM

Cette partie de la requête indique le jeu de données à partir duquel les données doivent être exportées. Le nom du jeu de données spécifié ici doit correspondre à un nom de jeu de données valide renvoyé par l’API des jeux de données.

**Exemple** :
- FROM `ISVUsage`
- FROM `ISVOrder`

#### <a name="where"></a>WHERE

Cette partie de la requête est utilisée pour spécifier les conditions de filtre du jeu de données. Seules les lignes correspondant à toutes les conditions répertoriées dans cette clause sont présentes dans le fichier exporté final. La condition de filtre peut se trouver dans l’une des colonnes répertoriées dans `selectableColumns` et `availableMetrics`. Les valeurs spécifiées dans la condition de filtre peuvent correspondre à une liste de nombres ou une liste de chaînes uniquement lorsque l’opérateur est `IN` ou `NOT IN`. Les valeurs peuvent toujours être spécifiées sous forme de chaîne littérale et sont converties en types natifs de colonnes. Plusieurs conditions de filtre doivent être séparées par une opération `AND`.

**Exemple** :

- MarketplaceSubscriptionId = ‘868368da-957d-4959-8992-3c12dc7e6260’
- CustomerName **LIKE** ‘%Contosso%’
- CustomerId **NOT IN** (1000, 1001, 1002)
- OrderQuantity=100
- OrderQuantity=‘100’
    - MarketplaceSubscriptionId=’7b487ac0-ce12-b732-dcd6-91a1e4e74a50’ AND CustomerId=’ 0f8b7fa0-eb83-a183-1225-ca153ef807aa’

#### <a name="order-by"></a>ORDER BY

Cette partie de la requête spécifie les critères de tri des lignes exportées. Les colonnes sur lesquelles le tri peut être défini doivent provenir de `selectableColumns` et `availableMetrics` du jeu de données. Si aucune direction de tri n’est spécifiée, elle est définie par défaut sur `DESC` sur la colonne. Le tri peut être défini sur plusieurs colonnes en séparant les critères par une virgule.

**Exemple** :

- **ORDER BY** NormalizedUsage **ASC**, EstimatedExtendedCharge(CC) **DESC**
- **ORDER BY** CustomerName **ASC**, NormalizedUsage

#### <a name="limit"></a>LIMIT

Cette partie de la requête spécifie le nombre de lignes qui seront exportées. Le nombre que vous spécifiez doit être un entier positif non nul.

#### <a name="timespan"></a>TIMESPAN

Cette partie de la requête spécifie la durée pendant laquelle les données doivent être exportées. Les valeurs possibles doivent provenir du champ `availableDateRanges` dans la définition du jeu de données.

### <a name="case-sensitivity-in-query-specification"></a>Respect de la casse dans la spécification de requête

La spécification ne respecte pas la casse. Les mots clés prédéfinis, les noms de colonnes et les valeurs peuvent être spécifiés en majuscules ou en minuscules.

## <a name="see-also"></a>Voir aussi

- [Liste des requêtes système](analytics-system-queries.md)
