---
title: Remplacements d’échantillonnage (préversion) – Azure Monitor Application Insights pour Java
description: Découvrez comment configurer des remplacements d’échantillonnage dans Azure Monitor Application Insights pour Java.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: 7602392b78f53e5b896e92058836fca60de39d64
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448880"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>Remplacements d’échantillonnage (préversion) – Azure Monitor Application Insights pour Java

> [!NOTE]
> La fonctionnalité de remplacements d’échantillonnage est en préversion depuis la mise en production 3.0.3.

Les remplacements d’échantillonnage vous permettent de remplacer le [pourcentage d’échantillonnage par défaut](./java-standalone-config.md#sampling), par exemple :
 * Définir le pourcentage d’échantillonnage sur 0 (ou une valeur faible) pour les contrôles d’intégrité bruyants.
 * Définir le pourcentage d’échantillonnage sur 0 (ou une valeur faible) pour les appels de dépendance bruyants.
 * Définir le pourcentage d’échantillonnage sur 100 pour un type de demande important (par exemple `/login`), même si vous avez configuré l’échantillonnage par défaut sur une valeur inférieure.

## <a name="terminology"></a>Terminologie

Avant d’en apprendre davantage sur les remplacements d’échantillonnage, vous devez comprendre le terme *étendue*. Une étendue est un terme général désignant l’un de ces trois éléments :

* Une requête entrante.
* Une dépendance sortante (par exemple, un appel distant à un autre service).
* Une dépendance « in-process » (par exemple, une tâche effectuée par les sous-composants du service).

Pour les remplacements d’échantillonnage, les composantes d’étendue suivants sont importants :

* Attributs

Les attributs d’étendue représentent à la fois les propriétés standard et personnalisées d’une requête ou d’une dépendance donnée.

## <a name="getting-started"></a>Prise en main

Pour commencer, créez un fichier de configuration nommé *applicationinsights.json*. Enregistrez-le dans le même répertoire que le fichier *applicationinsights-agent-\*.jar*. Utilisez le modèle suivant.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>Fonctionnement

Au démarrage d’une étendue, les attributs présents sur l’étendue sont utilisés pour vérifier si l’un des remplacements d’échantillonnage correspond.

Si l’un des remplacements d’échantillonnage correspond, son pourcentage d’échantillonnage est utilisé pour décider de l’opportunité d’échantillonner l’étendue.

Seul le premier remplacement d’échantillonnage correspondant est utilisé.

Si aucun remplacement d’échantillonnage ne correspond :

* S’il s’agit du premier intervalle dans la trace, le [pourcentage d’échantillonnage normal](./java-standalone-config.md#sampling) est utilisé.
* S’il ne s’agit pas de la première étendue de la trace, la décision d’échantillonnage parente est utilisée.

> [!WARNING]
> Quand la décision est prise de ne pas collecter d’étendue, aucune des étendues en aval n’est collectée, même s’il existe des remplacements d’échantillonnage correspondant à l’étendue en aval.
> Ce comportement est nécessaire car, autrement, il en résulterait des traces rompues, avec la collecte en aval d’étendues apparentées à des étendues non collectées.

> [!NOTE]
> La décision d’échantillonnage est basée sur le hachage du traceId (également appelé operationId) sur un nombre compris entre 0 et 100, et ce hachage est ensuite comparé au pourcentage d’échantillonnage.
> Étant donné que toutes les étendues d’une trace donnée ont le même traceId, elles ont le même hachage, de sorte que la décision d’échantillonnage est cohérente dans l’ensemble de la trace.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>Exemple : supprimer la collecte de télémétrie pour les contrôles d’intégrité

Cela aura pour effet de supprimer la collecte de télémétrie pour toutes les demandes de `/health-checks`.

Cela aura également pour effet de supprimer la collecte des étendues en aval (dépendances) qui seraient normalement collectées sous `/health-checks`.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>Exemple : supprimer la collecte de télémétrie pour un appel de dépendance bruyant

Cela aura pour effet de supprimer la collecte de télémétrie pour tous les appels redis `GET my-noisy-key`.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>Exemple : collecter 100 % de la télémétrie pour un type de demande important

Cela aura pour effet de collecter 100 % de la télémétrie pour `/login`.

Étant donné que les étendues en aval (dépendances) respectent la décision d’échantillonnage parente (en l’absence de tout remplacement d’échantillonnage pour cette étendue en aval), elles seront également collectées pour toutes les demandes « /login ».

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>Attributs d’étendue courants

Cette section répertorie certains attributs d’étendue courants que des remplacements d’échantillonnage peuvent utiliser.

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
