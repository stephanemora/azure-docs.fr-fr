---
title: Mise à niveau à partir de la version 2.x – Azure Monitor Application Insights Java
description: Mise à niveau à partir de la version 2.x d’Azure Monitor Application Insights Java.
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 342c535cadb1a2d3f2d18478d8941d9ea61bdf72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448965"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Mise à niveau à partir du SDK Application Insights pour Java 2.x

Si vous utilisez déjà le SDK Application Insights pour Java 2.x dans votre application, il n’est pas nécessaire de le supprimer.
L’agent Java 3.0 le détectera, puis il capturera et mettra en corrélation toutes les données de télémétrie personnalisées que vous envoyez via le SDK Java 2.x, tout en empêchant la collecte automatique effectuée par le SDK Java 2.x, afin d’éviter d’avoir des données de télémétrie en double.

Si vous utilisez l’agent Application Insights 2.x, vous devez supprimer l’argument JVM `-javaagent:` qui pointait vers l’agent 2.x.

Le reste de ce document décrit les limitations et les modifications que vous pouvez constater lorsque vous effectuez la mise à niveau de la version 2.x vers la version 3.0, et fournit des solutions de contournement qui pourront vous être utiles.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers et TelemetryProcessors

Les TelemetryInitializer et les TelemetryProcessor du SDK 2.x ne sont pas exécutés lors de l’utilisation de l’agent 3.0.
La plupart des cas d’usage qui les demandaient précédemment peuvent être résolus dans la version 3.0 en configurant [des dimensions personnalisées](./java-standalone-config.md#custom-dimensions) ou en configurant [des processeurs de télémétrie](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Plusieurs applications dans une seule Machine virtuelle Java

Actuellement, la version 3.0 ne prend en charge qu’une seule [chaîne de connexion et un seul nom de rôle](./java-standalone-config.md#connection-string-and-role-name) par processus en cours d’exécution. En particulier, il n’est pas encore possible d’avoir plusieurs applications web Tomcat dans le même déploiement Tomcat en utilisant des chaînes de connexion ou des noms de rôles différents.

## <a name="operation-names"></a>Noms des opérations

Dans la version 3.0, le nom des opérations a été modifié de manière à offrir une meilleure vue de synthèse dans le portail Application Insights.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Noms des opérations dans la version 3.0":::

Si toutefois, pour certaines applications, vous préférez la vue fournie par l’ancien nom des opérations, vous pouvez utiliser les [processeurs de télémétrie](./java-standalone-telemetry-processors.md) (préversion) de la version 3.0 pour revenir au comportement précédent.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Préfixer le nom des opérations avec la méthode HTTP (`GET`, `POST`, etc.)

Dans le SDK 2.x, les noms d’opérations étaient préfixés avec la méthode HTTP (`GET`, `POST`, etc.), comme ici :

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Noms d’opérations préfixés avec la méthode HTTP":::

Depuis 3.0.3, vous pouvez rétablir ce comportement 2.x à l’aide de

```json
{
  "preview": {
    "httpMethodInOperationName": true
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>Définir le nom de l’opération dans le chemin complet

En outre, dans le SDK 2.x, les noms d’opérations contenaient parfois le chemin complet, comme ici :

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Noms d’opérations comprenant un chemin complet":::

L’extrait de code ci-dessous configure quatre processeurs de télémétrie qui s’associent pour répliquer le comportement précédent.
Les processeurs de télémétrie effectuent les actions suivantes (dans l’ordre indiqué) :

1. Le premier processeur de télémétrie est un processeur d’étendue (de type `span`), ce qui signifie qu’il s’applique à `requests` et `dependencies`.

   Il correspondra à n’importe quelle étendue comprenant un attribut nommé `http.url`.

   Ensuite, il mettra à jour le nom de l’étendue avec la valeur d’attribut `http.url`.

   Celle-ci se trouve à la fin du nom. Toutefois, `http.url` ressemble à `http://host:port/path`, alors que vous souhaitez probablement n’avoir que la partie `/path`.

2. Le deuxième processeur de télémétrie est également un processeur d’étendue.

   Il correspondra à n’importe quelle étendue ayant un attribut nommé `http.url` (en d’autres termes, toute étendue à laquelle le premier processeur correspond).

   Ensuite, il extraira la portion du nom de l’étendue qui correspond au chemin dans un attribut nommé `tempName`.

3. Le troisième processeur de télémétrie est également un processeur d’étendue.

   Il correspondra à n’importe quelle étendue comprenant un attribut nommé `tempPath`.

   Ensuite, il mettra à jour le nom de l’étendue à partir de l’attribut `tempPath`.

4. Le dernier processeur de télémétrie est un processeur d’attribut (de type `attribute`), ce qui signifie qu’il s’applique à toutes les données de télémétrie qui ont des attributs (`requests`, `dependencies` et `traces`).

   Il correspondra à n’importe quelles données de télémétrie comprenant un attribut nommé `tempPath`.

   Il supprimera ensuite l’attribut nommé `tempPath`, afin de ne pas être signalé comme une dimension personnalisée.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Noms des dépendances

Le nom des dépendances a également été modifié dans la version 3.0 de manière à offrir une meilleure vue de synthèse dans le portail Application Insights.

De même, si vous préférez la vue fournie par l’ancien nom des dépendances, vous pouvez utiliser les techniques similaires citées plus haut pour revenir au comportement précédent.

## <a name="operation-name-on-dependencies"></a>Nom de l’opération dans les dépendances

Précédemment, dans le SDK 2.x, le nom d’opération des données de télémétrie de la requête était également défini sur les données de télémétrie des dépendances.
Application Insights Java 3.0 ne renseigne plus le nom de l’opération dans les données de télémétrie des dépendances.
Si vous souhaitez afficher le nom de l’opération pour la requête parente des données de télémétrie des dépendances, vous pouvez écrire une requête Logs (Kusto) à joindre à la table des requêtes à partir de la table des dépendances, par exemple,

```
let start = datetime('...');
let end = datetime('...');
dependencies
| where timestamp between (start .. end)
| project timestamp, type, name, operation_Id
| join (requests
    | where timestamp between (start .. end)
    | project operation_Name, operation_Id)
    on $left.operation_Id == $right.operation_Id
| summarize count() by operation_Name, type, name
```

## <a name="2x-sdk-logging-appenders"></a>Appenders de journalisation SDK 2.x

L’agent 3.0 [collecte automatiquement la journalisation](./java-standalone-config.md#auto-collected-logging) sans qu’il soit nécessaire de configurer des appenders de journalisation.
Si vous utilisez des appenders de journalisation SDK 2.x, vous pouvez les supprimer car ils seront supprimés par l’agent 3.0.

## <a name="2x-sdk-spring-boot-starter"></a>Spring boot starter SDK 2.x

Il n’existe aucun spring boot starter 3.0.
L’installation et la configuration de l’agent 3.0 suivent les mêmes [étapes simples](./java-in-process-agent.md#quickstart), que vous utilisiez spring boot ou non.

Lorsque vous procédez à une mise à niveau à partir de spring boot starter SDK 2.x, notez que le nom du rôle cloud n’aura plus la valeur par défaut `spring.application.name`.
Consultez la [documentation sur la configuration 3.0](./java-standalone-config.md#cloud-role-name) pour définir le nom du rôle cloud dans 3.0 via la configuration json ou la variable d’environnement.
