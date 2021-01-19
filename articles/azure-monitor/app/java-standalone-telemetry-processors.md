---
title: Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java
description: Comment configurer des processeurs de télémétrie dans Azure Monitor Application Insights pour Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: ba4e6b8b5e9db494ab4c0c372c2086087a2d58cb
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133172"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Processeurs de télémétrie (préversion) – Azure Monitor Application Insights pour Java

> [!NOTE]
> Cette fonctionnalité est encore en préversion.

L’agent Java 3.0 pour Application Insights offre désormais les fonctionnalités nécessaires pour le traitement des données de télémétrie avant leur exportation.

Voici quelques cas d’utilisation des processeurs de télémétrie :
 * Masquer des données sensibles
 * Ajouter de manière conditionnelle des dimensions personnalisées
 * Mettre à jour le nom de télémétrie utilisé pour l’agrégation et l’affichage
 * Supprimer ou filtrer les attributs d’étendue pour contrôler le coût d’ingestion

## <a name="terminology"></a>Terminologie

Avant de passer aux processeurs de télémétrie, il est important de comprendre ce que sont les traces et les étendues.

### <a name="traces"></a>Traces

Les traces permettent d’effectuer le suivi de la progression d’une requête unique, appelée `trace`, au fil de sa gestion par les services qui composent une application. La demande peut être lancée par un utilisateur ou une application. Chaque unité de travail d’une `trace` est appelée `span` (étendue) ; une `trace` est une arborescence d’étendues. Une `trace` est composée de l’étendue racine unique et d’une ou plusieurs étendues enfants.

### <a name="span"></a>Étendue

Les étendues sont des objets qui représentent le travail effectué par différents services ou des composants impliqués dans une demande lorsqu’elle transite par un système. Un `span` contient un `span context`, à savoir un ensemble d’identificateurs globaux uniques qui représentent la requête unique dont chaque étendue fait partie. 

Les étendues englobent les éléments suivants :

* Nom de l’étendue
* `SpanContext` immuable qui identifie de façon unique l’étendue
* Étendue parente sous la forme d’un `Span`, d’un `SpanContext` ou d’une valeur Null
* `SpanKind`
* Horodatage de début
* Horodatage de fin
* [`Attributes`](#attributes)
* Liste d’événements horodatés
* `Status`

En règle générale, le cycle de vie d’une étendue se présente ainsi :

* Un service reçoit une demande. Le contexte d’étendue, s’il existe, est extrait des en-têtes de demande.
* Une nouvelle étendue est créée comme enfant du contexte d’étendue extrait ; s’il n’y en a pas, une nouvelle étendue racine est créée.
* Le service gère la demande. Des attributs et des événements supplémentaires sont ajoutés à l’étendue. Ils sont utiles pour comprendre le contexte de la demande, par exemple le nom d’hôte de l’ordinateur qui gère la demande et les identificateurs du client.
* De nouvelles étendues peuvent être créées pour représenter le travail effectué par les sous-composants du service.
* Lorsque le service effectue un appel distant à un autre service, le contexte d’étendue actuel est sérialisé et transmis au service suivant en injectant le contexte d’étendue dans les en-têtes ou l’enveloppe de message.
* Le travail effectué par le service se termine avec ou sans erreur. L’état de l’étendue est défini, et l’étendue est marquée comme terminée.

### <a name="attributes"></a>Attributs

Les `Attributes` (attributs) constituent une liste de zéro, une ou plusieurs paires clé-valeur encapsulées dans un `span`. Un attribut DOIT comporter les propriétés suivantes :

La clé d’attribut, qui DOIT être une chaîne non Null et non vide
La valeur de l’attribut, qui peut être :
* Un type primitif : chaîne, booléen, nombre à virgule flottante double précision (IEEE 754-1985) ou entier signé 64 bits
* Un tableau de valeurs de type primitif Le tableau DOIT être homogène, c’est-à-dire qu’il NE DOIT PAS contenir des valeurs de types différents. Dans le cas des protocoles qui ne prennent pas en charge les valeurs de tableau de manière native, il est PRÉFÉRABLE de représenter ces valeurs sous forme de chaînes JSON.

## <a name="supported-processors"></a>Processeurs pris en charge :
 * Processeur d’attribut
 * Processeur d’étendue

## <a name="to-get-started"></a>Pour commencer

Créez un fichier de configuration nommé `applicationinsights.json`, puis placez-le dans le même répertoire que `applicationinsights-agent-***.jar`, avec le modèle suivant.

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

## <a name="includeexclude-spans"></a>Inclusion/exclusion d’étendues

Le processeur d’attributs et le processeur d’étendues offrent la possibilité d’indiquer un ensemble de propriétés d’une étendue à confronter pour déterminer si celle-ci doit ou non être incluse dans le processeur de télémétrie. Pour configurer cette option, sous `include` et/ou `exclude`, au moins un `matchType` et l’un des `spanNames` ou `attributes` sont requis. Plusieurs conditions peuvent être spécifiées pour la configuration d’inclusion/exclusion. Pour qu’une correspondance soit établie, toutes les conditions spécifiées doivent prendre la valeur true. 

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
      },
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
      },
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
      },
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
      },
    ]
  }
]
```
Pour l’action `hash`, les éléments suivants sont obligatoires :
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Cette fonctionnalité n’est disponible que dans la version 3.0.1 et les versions ultérieures.

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
      },
    ]
  }
]
```
Pour l’action `extract`, les éléments suivants sont obligatoires :
* `key`
* `pattern`
* `action` : `extract`

Pour plus d’informations, consultez la documentation des [exemples de processeurs de télémétrie](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processors"></a>Processeurs d’étendue

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