---
title: Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java
description: Découvrez comment configurer des processeurs de télémétrie dans Azure Monitor Application Insights pour Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 991e52c13a5730b83552abb6b922d4d7a57c5429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024113"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java

> [!NOTE]
> La fonctionnalité Processeurs de télémétrie est en préversion.

L’agent Java 3.0 pour Application Insights peut traiter des données de télémétrie avant leur exportation.

Voici quelques cas d’utilisation des processeurs de télémétrie :
 * Masquer des données sensibles.
 * Ajouter de manière conditionnelle des dimensions personnalisées.
 * Mettre à jour le nom de l’étendue, qui est utilisé pour agréger des données de télémétrie similaires dans le portail Azure.
 * Annuler des attributs d’étendue spécifiques pour contrôler les coûts d’ingestion.

> [!NOTE]
> Si vous envisagez d’annuler des étendues spécifiques (entières) pour contrôler le coût d’ingestion, consultez [Remplacements d’échantillonnage](./java-standalone-sampling-overrides.md).

## <a name="terminology"></a>Terminologie

Avant d’en savoir plus sur les processeurs de télémétrie, vous devez comprendre le terme *étendue*. Une étendue est un terme général désignant l’un de ces trois éléments :

* Une requête entrante.
* Une dépendance sortante (par exemple, un appel distant à un autre service).
* Une dépendance « in-process » (par exemple, une tâche effectuée par les sous-composants du service).

Pour les processeurs de télémétrie, ces composants d’étendue sont importants :

* Nom
* Attributs

Le nom de l’étendue est l’affichage principal utilisé pour les requêtes et les dépendances dans le portail Azure. Les attributs d’étendue représentent à la fois les propriétés standard et personnalisées d’une requête ou d’une dépendance donnée.

## <a name="telemetry-processor-types"></a>Types de processeurs de télémétrie

Actuellement, les deux types de processeurs de télémétrie sont les processeurs d’attributs et les processeurs d’étendue.

Un processeur d’attributs peut insérer, mettre à jour, supprimer ou hacher des attributs.
Il peut également utiliser une expression régulière pour extraire un ou plusieurs nouveaux attributs d’un attribut existant.

Un processeur d’étendue peut mettre à jour le nom de la télémétrie.
Il peut également utiliser une expression régulière pour extraire un ou plusieurs nouveaux attributs du nom de l’étendue.

> [!NOTE]
> Actuellement, les processeurs de télémétrie traitent uniquement les attributs de type chaîne. Ils ne traitent pas les attributs de type booléen ou nombre.

## <a name="getting-started"></a>Prise en main

Pour commencer, créez un fichier de configuration nommé *applicationinsights.json*. Enregistrez-le dans le même répertoire que le fichier *applicationinsights-agent-\*.jar*. Utilisez le modèle suivant.

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

## <a name="include-criteria-and-exclude-criteria"></a>Inclure des critères et exclure des critères

Les processeurs d’attributs et les processeurs d’étendue prennent en charge les critères facultatifs `include` et `exclude` .
Un processeur est appliqué uniquement aux étendues qui correspondent à ses critères `include` (si fournis) _et_ qui ne correspondent pas à ses critères `exclude` (si fournis).

Pour configurer cette option, sous `include` ou `exclude` (ou les deux), spécifiez au moins un `matchType` et `spanNames` ou `attributes`.
Plusieurs conditions peuvent être spécifiées pour la configuration d’inclusion/exclusion.
Pour qu’une correspondance soit établie, toutes les conditions spécifiées doivent prendre la valeur true. 

* **Champ obligatoire** : `matchType` contrôle la façon dont les éléments des tableaux `spanNames` et `attributes` sont interprétés. Les valeurs possibles sont `regexp` et `strict`. 

* **Champs facultatifs** : 
    * `spanNames` doit correspondre au moins à l’un des éléments. 
    * `attributes` spécifie la liste des attributs par rapport auxquels établir une correspondance. Tous ces attributs doivent correspondre exactement pour qu’une correspondance soit établie.
    
> [!NOTE]
> Si les attributs `include` et `exclude` sont tous deux spécifiés, les propriétés `include` sont vérifiées avant les propriétés `exclude`.

### <a name="sample-usage"></a>Exemple d’utilisation

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
Pour plus d’informations, consultez [Exemples de processeurs de télémétrie](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Processeur d’attributs

Le processeur d’attributs modifie les attributs d’une étendue. Il peut prendre en charge la possibilité d’inclure ou d’exclure des étendues. Il prend une liste d’actions qui sont exécutées dans l’ordre spécifié par le fichier de configuration. Le processeur prend en charge les actions suivantes :

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

L’action `insert` insère un nouvel attribut dans les étendues où la clé n’existe pas encore.   

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
L’action `insert` nécessite les paramètres suivants :
* `key`
* `value` ou `fromAttribute`
* `action`: `insert`

### `update`

L’action `update` met à jour un attribut dans les étendues où la clé existe déjà.

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
L’action `update` nécessite les paramètres suivants :
* `key`
* `value` ou `fromAttribute`
* `action`: `update`


### `delete` 

L’action `delete` supprime un attribut d’une étendue.

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
L’action `delete` nécessite les paramètres suivants :
* `key`
* `action`: `delete`

### `hash`

L’action `hash` hache (SHA1) une valeur d’attribut existante.

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
L’action `hash` nécessite les paramètres suivants :
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> La fonctionnalité `extract` est disponible uniquement dans la version 3.0.2 et les versions ultérieures.

L’action `extract` extrait des valeurs en suivant une règle d’expression régulière de la clé d’entrée vers les clés cibles spécifiées dans la règle. S’il existe déjà une clé cible, elle est remplacée. Cette action se comporte comme le paramètre `toAttributes` du [processeur d’étendue](#extract-attributes-from-the-span-name), où l’attribut existant est la source.

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
L’action `extract` nécessite les paramètres suivants :
* `key`
* `pattern`
* `action`: `extract`

Pour plus d’informations, consultez [Exemples de processeurs de télémétrie](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Processeur d’étendue

Le processeur d’étendue modifie le nom ou les attributs d’une étendue en fonction de son nom. Il peut prendre en charge la possibilité d’inclure ou d’exclure des étendues.

### <a name="name-a-span"></a>Nommer une étendue

La `name` section requiert le paramètre `fromAttributes`. Les valeurs de ces attributs sont utilisées pour créer un nouveau nom, concaténées dans l’ordre spécifié par la configuration. Le processeur ne modifie le nom de l’étendue que si tous ces attributs sont présents sur l’étendue.

Le paramètre `separator` est facultatif. Ce paramètre est une chaîne. Il est spécifié pour fractionner les valeurs.
> [!NOTE]
> Si le changement de nom dépend du processeur d’attributs pour modifier les attributs, assurez-vous que le processeur d’étendue est spécifié après le processeur d’attributs dans la spécification du pipeline.

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

### <a name="extract-attributes-from-the-span-name"></a>Extraire des attributs du nom d’étendue

La section `toAttributes` répertorie les expressions régulières à mettre en correspondance avec le nom de l’étendue. Elle extrait des attributs en fonction des sous-expressions.

Le paramètre `rules` est obligatoire. Ce paramètre répertorie les règles utilisées pour extraire des valeurs d’attribut du nom d’étendue. 

Les valeurs dans le nom d’étendue sont remplacées par les noms d’attributs extraits. Chaque règle de la liste est une chaîne de modèle d’expression régulière (regex). 

Voici comment les valeurs sont remplacées par les noms d’attributs extraits :

1. Le nom d’étendue est vérifié par rapport à l’expression régulière. 
1. Si l’expression régulière correspond, toutes les sous-expressions nommées de l’expression régulière sont extraites en tant qu’attributs. 
1. Les attributs extraits sont ajoutés à l’étendue. 
1. Chaque nom de sous-expression devient un nom d’attribut. 
1. La portion correspondante de la sous-expression devient la valeur de l’attribut. 
1. La portion correspondante dans le nom d’étendue est remplacée par le nom d’attribut extrait. Si les attributs existent déjà dans l’étendue, ils sont remplacés. 
 
Le processus est répété pour toutes les règles dans l’ordre spécifié. Chaque règle subséquente fonctionne sur le nom d’étendue qui est la sortie de la règle précédente.

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

## <a name="common-span-attributes"></a>Attributs d’étendue courants

Cette section répertorie certains attributs d’étendue courants que les processeurs de télémétrie peuvent utiliser.

### <a name="http-spans"></a>Étendues HTTP

| Attribut  | Type | Description | 
|---|---|---|
| `http.method` | string | Méthode de requête HTTP.|
| `http.url` | string | URL complète de la requête HTTP sous la forme `scheme://host[:port]/path?query[#fragment]`. Le fragment n’est généralement pas transmis via HTTP. Toutefois, si le fragment est connu, il doit être inclus.|
| `http.status_code` | nombre | [Code de statut de la réponse HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | string | Type de protocole HTTP. |
| `http.user_agent` | string | Valeur de l’en-tête [HTTP User-Agent](https://tools.ietf.org/html/rfc7231#section-5.5.3) envoyé par le client. |

### <a name="jdbc-spans"></a>Étendues JDBC

| Attribut  | Type | Description  |
|---|---|---|
| `db.system` | string | Identificateur du produit SGBD (système de gestion de base de données) utilisé. |
| `db.connection_string` | string | Chaîne de connexion utilisée pour se connecter à la base de données. Il est recommandé de supprimer les informations d’identification incorporées.|
| `db.user` | string | Nom d’utilisateur utilisé pour accéder à la base de données. |
| `db.name` | string | Chaîne utilisée pour signaler le nom de la base de données qui fait l’objet de l’accès. Dans les commandes qui changent de base de données, cette chaîne doit être définie sur la base de données cible, même en cas d’échec de la commande.|
| `db.statement` | string | Instruction de base de données en cours d’exécution.|
