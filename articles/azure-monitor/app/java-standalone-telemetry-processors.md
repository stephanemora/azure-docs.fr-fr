---
title: Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java
description: Comment configurer des processeurs de télémétrie dans Azure Monitor Application Insights pour Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632578"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java

> [!NOTE]
> Cette fonctionnalité est encore en préversion.

L’agent Java 3.0 pour Application Insights offre désormais les fonctionnalités nécessaires pour le traitement des données de télémétrie avant leur exportation.

Voici quelques cas d’utilisation des processeurs de télémétrie :
 * Masquer des données sensibles
 * Ajouter de manière conditionnelle des dimensions personnalisées
 * Mettre à jour le nom utilisé pour l’agrégation et l’affichage dans le portail Azure
 * Supprimer les attributs d’étendue pour contrôler le coût d’ingestion

## <a name="terminology"></a>Terminologie

Avant de passer aux processeurs de télémétrie, il est important de comprendre ce que le terme étendue signifie.

Une étendue est un terme général désignant l’un de ces trois éléments :

* Une requête entrante
* Une dépendance sortante (par exemple, un appel distant à un autre service)
* Une dépendance « in-process » (par exemple, une tâche effectuée par les sous-composants du service)

Dans le cadre des processeurs de télémétrie, les composants importants d’une étendue sont :

* Nom
* Attributs

Le nom de l’étendue est l’affichage principal utilisé pour les demandes et les dépendances dans le portail Azure.

Les attributs d’étendue représentent à la fois les propriétés standard et personnalisées d’une requête ou d’une dépendance donnée.

## <a name="telemetry-processor-types"></a>Types de processeurs de télémétrie

Il existe actuellement deux types de processeurs de télémétrie.

#### <a name="attribute-processor"></a>Processeur d’attributs

Un processeur d’attributs permet d’insérer, de mettre à jour, de supprimer ou de hacher des attributs.
Il peut également extraire (via une expression régulière) un ou plusieurs nouveaux attributs d’un attribut existant.

#### <a name="span-processor"></a>Processeur d’étendue

Un processeur d’étendue permet de mettre à jour le nom de la télémétrie.
Il peut également extraire (via une expression régulière) un ou plusieurs nouveaux attributs du nom de l’étendue.

> [!NOTE]
> Notez que les processeurs de télémétrie traitent actuellement uniquement les attributs de type chaîne et non les attributs de type booléen ou numérique.

## <a name="getting-started"></a>Prise en main

Créez un fichier de configuration nommé `applicationinsights.json`, puis placez-le dans le même répertoire que `applicationinsights-agent-*.jar`, avec le modèle suivant.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-criteria"></a>Inclure/exclure des critères

Les processeurs d’attributs et les processeurs d’étendue prennent en charge les critères facultatifs `include` et `exclude` .
Un processeur est appliqué uniquement aux étendues qui correspondent à ses critères `include` (si fournis) _et_ qui ne correspondent pas à ses critères `exclude` (si fournis).

Pour configurer cette option, sous `include` et/ou `exclude`, au moins un `matchType` et l’un des `spanNames` ou `attributes` sont requis.
Plusieurs conditions peuvent être spécifiées pour la configuration d’inclusion/exclusion.
Pour qu’une correspondance soit établie, toutes les conditions spécifiées doivent prendre la valeur true. 

**Champ obligatoire** : 
* `matchType` contrôle la façon dont les éléments des tableaux `spanNames` et `attributes` sont interprétés. Les valeurs possibles sont `regexp` ou `strict`. 

**Champs facultatifs** : 
* `spanNames` doit correspondre au moins à l’un des éléments. 
* `attributes` spécifie la liste des attributs par rapport auxquels établir une correspondance. Tous ces attributs doivent correspondre exactement pour qu’une correspondance soit établie.

> [!NOTE]
> Si les attributs `include` et `exclude` sont tous deux spécifiés, les propriétés de l’attribut `include` sont vérifiées avant celles ce l’attribut `exclude`.

#### <a name="sample-usage"></a>Exemple d’utilisation

```json

"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
Pour plus d’informations, consultez la documentation des [exemples de processeurs de télémétrie](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Processeur d’attributs

Le processeur d’attributs modifie les attributs d’une étendue. Il prend éventuellement en charge la possibilité d’inclure ou d’exclure des étendues. Il prend une liste d’actions qui sont exécutées dans l’ordre spécifié dans le fichier de configuration. Les actions prises en charge sont les suivantes :

### `insert`

Insère un nouvel attribut dans les étendues où la clé n’existe pas encore.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
Pour l’action `insert`, les éléments suivants sont obligatoires :
  * `key`
  * `value` ou `fromAttribute`
  * `action`:`insert`

### `update`

met à jour un attribut dans les étendues où la clé existe.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
Pour l’action `update`, les éléments suivants sont obligatoires :
  * `key`
  * `value` ou `fromAttribute`
  * `action`:`update`


### `delete` 

supprime un attribut d’une étendue.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
Pour l’action `delete`, les éléments suivants sont obligatoires :
  * `key`
  * `action`: `delete`

### `hash`

hache (SHA1) une valeur d’attribut existante.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
Pour l’action `hash`, les éléments suivants sont obligatoires :
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.0.2 et les versions ultérieures.

Extrait des valeurs suivant une règle d’expression régulière de la clé d’entrée vers les clés cibles spécifiées dans la règle. S’il existe déjà une clé cible, elle sera remplacée. Ce comportement est similaire à celui du paramètre `toAttributes` du [Processeur d’étendue](#extract-attributes-from-span-name), avec comme source l’attribut existant.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
Pour l’action `extract`, les éléments suivants sont obligatoires :
* `key`
* `pattern`
* `action` : `extract`

Pour plus d’informations, consultez la documentation des [exemples de processeurs de télémétrie](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Processeur d’étendue

Le processeur d’étendue modifie le nom ou les attributs d’une étendue en fonction de son nom. Il prend éventuellement en charge la possibilité d’inclure ou d’exclure des étendues.

### <a name="name-a-span"></a>Nommer une étendue

Le paramètre suivant est requis dans la section du nom :

* `fromAttributes` : la valeur d’attribut pour les clés est utilisée pour créer un nom dans l’ordre spécifié dans la configuration. Toutes les clés d’attribut doivent être spécifiées dans l’étendue pour que le processeur la renomme.

Vous pouvez éventuellement configurer le paramètre suivant :

* `separator` : une chaîne, qui est spécifiée, sera utilisée pour fractionner les valeurs
> [!NOTE]
> Si le changement de nom dépend d’attributs en modifiés par le processeur d’attributs, assurez-vous que le processeur d’étendue est spécifié après le processeur d’attributs dans la spécification du pipeline.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-span-name"></a>Extraire des attributs d’une nom d’étendue

Prend une liste d’expressions régulières à mettre en correspondance avec le nom d’étendue, et en extrait des attributs en fonction de sous-expressions. Doit être spécifié dans la section `toAttributes`.

Les paramètres suivants sont requis :

`rules` : liste de règles pour l’extraction de valeurs d’attribut du nom d’étendue. Les valeurs dans le nom d’étendue sont remplacées par les noms d’attributs extraits. Chaque règle figurant dans la liste est une chaîne de modèle regex. Le nom d’étendue est vérifié par rapport à l’expression régulière. Si l’expression régulière correspond, toutes les sous-expressions nommées de l’expression régulière sont extraites en tant qu’attributs et ajoutées à l’étendue. Chaque nom de sous-expression devient un nom d’attribut, et la portion correspondante de la sous-expression devient la valeur d’attribut. La portion correspondante dans le nom d’étendue est remplacée par le nom d’attribut extrait. Si les attributs existent déjà dans l’étendue, ils sont remplacés. Le processus est répété pour toutes les règles dans l’ordre dans de leur spécification. Chaque règle subséquente fonctionne sur le nom d’étendue qui est la sortie résultant du traitement de la règle précédente.

```json

"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="list-of-attributes"></a>Liste d’attributs

Voici une liste d’attributs d’étendue courants qui peuvent être utilisés dans les processeurs de télémétrie.

### <a name="http-spans"></a>Étendues HTTP

| Attribut  | Type | Description | 
|---|---|---|
| `http.method` | string | Méthode de requête HTTP.|
| `http.url` | string | URL complète de la requête HTTP sous la forme `scheme://host[:port]/path?query[#fragment]`. En règle générale, le fragment n’est pas transmis via HTTP. S’il est connu, il est néanmoins préférable de l’inclure.|
| `http.status_code` | nombre | [Code de statut de la réponse HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Type de protocole HTTP utilisé. |
| `http.user_agent` | string | Valeur de l’en-tête [HTTP User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) envoyé par le client. |

### <a name="jdbc-spans"></a>Étendues JDBC

| Attribut  | Type | Description  |
|---|---|---|
| `db.system` | string | Identificateur du produit SGBD (système de gestion de base de données) utilisé. |
| `db.connection_string` | string | Chaîne de connexion utilisée pour se connecter à la base de données. Il est recommandé de supprimer les informations d’identification incorporées.|
| `db.user` | string | Nom d’utilisateur utilisé pour accéder à la base de données. |
| `db.name` | string | Cet attribut est utilisé pour signaler le nom de la base de données qui fait l’objet de l’accès. Dans les commandes qui changent de base de données, cette valeur doit être définie sur la base de données cible (même en cas d’échec de la commande).|
| `db.statement` | string | Instruction de base de données en cours d’exécution.|
