---
title: Exemples de processeurs de télémétrie - Azure Monitor Application Insights pour Java
description: Exemples illustrant des processeurs de télémétrie dans Azure Monitor Application Insights pour Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: b9ad5347e146fc94b513180c591b00c4f449619f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146414"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Exemples de processeurs de télémétrie - Azure Monitor Application Insights pour Java

## <a name="includeexclude-samples"></a>Inclure/Exclure des exemples

### <a name="1-include-spans"></a>1. Inclure des étendues

L’exemple suivant illustre l’inclusion d’étendues pour ce processeur d’attributs. Toutes les autres étendues ne correspondant pas aux propriétés ne sont pas traitées par ce processeur.

Les conditions suivantes doivent être remplies pour une correspondance :
* Le nom de l’étendue doit correspondre à « spanA » ou « spanB » 

Les étendues suivantes correspondent aux propriétés d’inclusion et les actions de processeur sont appliquées.
* Span1 Name: 'spanA' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Les étendues suivantes ne correspondent pas aux propriétés d’inclusion et les actions de processeur ne sont pas appliquées :
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

### <a name="2-exclude-spans"></a>2. Exclure des étendues

L’exemple suivant illustre l’exclusion d’étendues pour ce processeur d’attributs. Toutes les étendues correspondant aux propriétés ne sont pas traitées par ce processeur.

Les conditions suivantes doivent être remplies pour une correspondance :
* Le nom de l’étendue doit correspondre à « spanA » ou « spanB » 

Les étendues suivantes correspondent aux propriétés d’exclusion et les actions de processeur ne sont pas appliquées.
* Span1 Name: 'spanA' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: 'spanB' Attributes: {env: dev, test_request: false}
* Span3 Name: 'spanA' Attributes: {env: 1, test_request: dev, credit_card: 1234}

Les étendues suivantes ne correspondent pas aux propriétés d’exclusion et les actions de processeur sont appliquées :
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

### <a name="3-excludemulti-spans"></a>3. Étendues ExcludeMulti

L’exemple suivant illustre l’exclusion d’étendues pour ce processeur d’attributs. Toutes les étendues correspondant aux propriétés ne sont pas traitées par ce processeur.

Les conditions suivantes doivent être remplies pour une correspondance :
* Un attribut (« env », « dev ») doit être présent dans l’étendue à des fins de correspondance.
* Tant qu’il existe un attribut avec la clé « test_request » dans l’étendue, il existe une correspondance.

Les étendues suivantes correspondent aux propriétés d’exclusion et les actions de processeur ne sont pas appliquées.
* Span1 Name: 'spanB' Attributes: {env: dev, test_request: 123, credit_card: 1234}
* Span2 Name: 'spanA' Attributes: {env: dev, test_request: false}

Les étendues suivantes ne correspondent pas aux propriétés d’exclusion et les actions de processeur sont appliquées :
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

### <a name="4-selective-processing"></a>4. Traitement sélectif

L’exemple suivant montre comment spécifier le jeu de propriétés d’étendue pour indiquer les étendues auxquelles ce processeur doit être appliqué. Les propriétés `include` indiquent les étendues à inclure, et les propriétés `exclude` filtrent les plages qui ne doivent pas être traitées.

Avec la configuration ci-dessous, les étendues suivantes correspondent aux propriétés et les actions de processeur sont appliquées :

* Span1 Name: 'spanB' Attributes: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 Name: 'spanA' Attributes: {env: staging, test_request: false, redact_trace: true}

Les étendues suivantes ne correspondent pas aux propriétés include et les actions de processeur ne sont pas appliquées :
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
## <a name="attribute-processor-samples"></a>Exemples de processeur d’attributs

### <a name="insert"></a>Insérer

L’exemple suivant insère un nouvel attribut {"attribute1" : "attributeValue1"} dans les étendues où la clé "attribute1" n’existe pas.

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

L’exemple suivant utilise la valeur de l’attribut "anotherkey" pour insérer un nouvel attribut {"newKey": "value from attribute 'anotherkey'} dans les étendues où la clé "newKey" n’existe pas. Si l’attribut ’anotherkey’ n’existe pas, aucun nouvel attribut n’est inséré dans les étendues.

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

L’exemple suivant met à jour l’attribut avec la valeur { "db.secret": "redacted"} et met à jour l’attribut ’boo’ à l’aide de la valeur de l’attribut ’foo'. Les étendues sans l’attribut ’boo’ne seront pas modifiées.

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

### <a name="delete"></a>Supprimer

L’exemple suivant illustre la suppression d’un attribut avec la clé ’credit_card'.

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

L’exemple suivant illustre les valeurs d’attribut existantes de hachage.

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

L’exemple suivant illustre l’utilisation de regex pour créer de nouveaux attributs en fonction de la valeur d’un autre attribut.
Par exemple, compte tenu de http.url = http://example.com/path?queryParam1=value1, queryParam2 = value2, les attributs suivants seront insérés :
* httpProtocol: http
* httpDomain: example.com
* httpPath: path
* httpQueryParams: queryParam1=value1,queryParam2=value2
* La valeur http.url ne change pas.

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

L’exemple suivant montre comment traiter des étendues dont le nom correspond à des modèles regexps.
Ce processeur supprime l’attribut « token » et obfusque l’attribut « password » dans les étendues dont le nom correspond à « auth.\* » et ne correspond pas à « login.\* ».

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


## <a name="span-processor-samples"></a>Exemples de processeur d’étendue

### <a name="name-a-span"></a>Nommer une étendue

L’exemple suivant spécifie que les valeurs d’attribut « db.svc », « operation », et « id » formeront le nouveau nom de l’étendue, dans cet ordre, séparées par la valeur « :: ».
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

### <a name="extract-attributes-from-span-name"></a>Extraire des attributs d’une nom d’étendue

Supposons que le nom d’étendue en entrée est /API/v1/document/12345678/update. L’application des résultats suivants dans le nom d’étendue en sortie /api/v1/document/{documentId}/update ajoute un nouvel attribut "documentId"="12345678" à l’étendue.
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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Extraire les attributs du nom d’étendue avec include et exclude

L’exemple suivant montre comment renommer le nom d’étendue en « {operation_website} », et ajouter l’attribut {Key: operation_website, Value: oldSpanName } lorsque l’étendue a les propriétés suivantes :
- Le nom d’étendue contient « / » quelque part dans la chaîne.
- Le nom d’étendue n’est pas « donot/change ».
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