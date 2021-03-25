---
title: Exemples de processeurs de télémétrie - Azure Monitor Application Insights pour Java
description: Explorez des exemples illustrant des processeurs de télémétrie dans Azure Monitor Application Insights pour Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734585"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Exemples de processeurs de télémétrie - Azure Monitor Application Insights pour Java

Cet article fournit des exemples de processeurs de télémétrie dans Application Insights pour Java. Vous trouverez des exemples pour les configurations d’inclusion et d’exclusion. Vous y trouverez également des exemples pour les processeurs d’attributs et les processeurs d’étendue.
## <a name="include-and-exclude-samples"></a>Exemples d’inclusion et d’exclusion

Dans cette section, vous allez apprendre à inclure et exclure des étendues. Vous verrez également comment exclure plusieurs étendues et appliquer un traitement sélectif.
### <a name="include-spans"></a>Inclure des étendues

Cette section montre comment inclure des étendues pour un processeur d’attributs. Les étendues qui ne correspondent pas aux propriétés ne sont pas traitées par le processeur.

Une correspondance exige que le nom de l’étendue soit égal à `spanA` ou `spanB`. 

Ces étendues correspondent aux propriétés d’inclusion et les actions de processeur sont appliquées :
* Span1 Name: 'spanA' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Cette étendue ne correspond pas aux propriétés d’inclusion et les actions du processeur ne sont pas appliquées :
* Span4 Name: 'spanC' Attributes: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
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
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>Exclure des étendues

Cette section montre comment exclure des étendues pour un processeur d’attributs. Les étendues correspondant aux propriétés ne sont pas traitées par ce processeur.

Une correspondance exige que le nom de l’étendue soit égal à `spanA` ou `spanB`.

Les étendues suivantes correspondent aux propriétés d’exclusion et les actions du processeur ne sont pas appliquées :
* Span1 Name: 'spanA' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Cette étendue ne correspond pas aux propriétés d’exclusion et les actions du processeur sont appliquées :
* Span4 Name: 'spanC' Attributes: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>Exclure des étendues à l’aide de plusieurs critères

Cette section montre comment exclure des étendues pour un processeur d’attributs. Les étendues correspondant aux propriétés ne sont pas traitées par ce processeur.

Une correspondance requiert que les conditions suivantes soient remplies :
* Un attribut (par exemple `env` ou `dev`) doit exister dans l’étendue.
* L’étendue doit avoir un attribut qui a la clé `test_request`.

Les étendues suivantes correspondent aux propriétés d’exclusion et les actions du processeur ne sont pas appliquées.
* Span1 Name: 'spanB' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: 'spanA' Attributes: {env: dev, test_request: false}

Les étendues suivantes ne correspondent pas aux propriétés d’exclusion et les actions du processeur sont appliquées :
* Span3 Name: 'spanB' Attributes: {env: 1, test_request: dev, credit_card: 1234}
* Span4 Name: 'spanC' Attributes: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>Traitement sélectif

Cette section montre comment spécifier le jeu de propriétés d’étendue qui indiquent les étendues auxquelles ce processeur doit être appliqué. Les propriétés d’inclusion indiquent les étendues à traiter. Les propriétés d’exclusion filtrent les étendues qui ne doivent pas être traitées.

Dans la configuration suivante, les étendues correspondent aux propriétés et les actions du processeur sont appliquées :

* Span1 Name: 'spanB' Attributes: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 Name: 'spanA' Attributes: {env: staging, test_request: false, redact_trace: true}

Ces étendues ne correspondent pas aux propriétés d’inclusion et les actions du processeur ne sont pas appliquées :
* Span3 Name: 'spanB' Attributes: {env: production, test_request: true, credit_card: 1234, redact_trace: false}
* Span4 Name: 'spanC' Attributes: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
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
  }
}
```
## <a name="attribute-processor-samples"></a>Exemples de processeurs d’attributs

### <a name="insert"></a>Insérer

L’exemple suivant insère le nouvel attribut `{"attribute1": "attributeValue1"}` dans des étendues où la clé `attribute1` n’existe pas.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Insérer à partir d’une autre clé

L’exemple suivant utilise la valeur de l’attribut `anotherkey` pour insérer le nouvel attribut `{"newKey": "<value from attribute anotherkey>"}` dans des étendues où la clé `newKey` n’existe pas. Si l’attribut `anotherkey` n’existe pas, aucun nouvel attribut n’est inséré dans les étendues.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Update

L’exemple suivant met à jour l’attribut avec la valeur `{"db.secret": "redacted"}`. Il met à jour l’attribut `boo` en utilisant la valeur de l’attribut `foo`. Les étendues qui n’ont pas l’attribut `boo` ne changent pas.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>DELETE

L’exemple suivant montre comment supprimer un attribut qui a la clé `credit_card`.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Hachage

L’exemple suivant montre comment hacher des valeurs d’attribut existantes.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Extract

L’exemple suivant montre comment utiliser une expression régulière (regex) pour créer de nouveaux attributs basés sur la valeur d’un autre attribut.
Par exemple, avec `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2`, les attributs suivants sont insérés :
* httpProtocol : `http`
* httpDomain : `example.com`
* httpPath : `path`
* httpQueryParams : `queryParam1=value1,queryParam2=value2`
* http.url : *aucun* changement

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

L’exemple suivant montre comment traiter des étendues dont le nom correspond à des modèles d’expression régulière.
Ce processeur supprime l’attribut `token`. Il obscurcit l’attribut `password` dans des étendues où le nom correspond à `auth.*` et où le nom ne correspond pas à `login.*`.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Exemples de processeurs d’étendue

### <a name="name-a-span"></a>Nommer une étendue

L’exemple suivant spécifie les valeurs des attributs `db.svc`, `operation` et `id`. Il forme le nouveau nom de l’étendue en utilisant ces attributs, dans cet ordre, séparés par la valeur `::`.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name"></a>Extraire des attributs d’un nom d’étendue

Supposons que le nom d’étendue en entrée est `/api/v1/document/12345678/update`. L’exemple suivant donne le nom d’étendue en sortie `/api/v1/document/{documentId}/update`. Il ajoute le nouvel attribut `documentId=12345678` à l’étendue.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Extraire des attributs d’un nom d’étendue avec les opérations d’inclusion (include) et d’exclusion (exclude)

L’exemple suivant montre comment modifier le nom d’étendue en `{operation_website}`. Il ajoute un attribut avec la clé `operation_website` et la valeur `{oldSpanName}` lorsque l’étendue a les propriétés suivantes :
- Le nom d’étendue contient `/` dans la chaîne.
- Le nom d’étendue n’est pas `donot/change`.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```
