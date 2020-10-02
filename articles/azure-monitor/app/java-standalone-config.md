---
title: Superviser les applications Java partout - Azure Monitor Application Insights
description: Supervision des performances des applications Java sans code s’exécutant dans tout environnement sans instrumenter l’application. Recherchez la cause racine des problèmes à l’aide du suivi distribué et de la cartographie d’application.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 561a6405a49d8f15affbf6d8d4de1a7f4886826a
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056096"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Options de configuration - Agent autonome Java pour Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Chaîne de connexion et nom de rôle

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

La chaîne de connexion est requise, et le nom de rôle est important chaque fois que vous envoyez des données à partir de différentes applications vers la même ressource Application Insights.

Vous trouverez plus de détails ainsi que des options de configuration supplémentaires ci-dessous.

## <a name="configuration-file-path"></a>Chemin d'accès au fichier de configuration

Par défaut, la préversion Application Insights Java 3.0 s’attend à ce que le fichier de configuration soit nommé `ApplicationInsights.json` et se trouve dans le même répertoire que `applicationinsights-agent-3.0.0-PREVIEW.5.jar`.

Vous pouvez spécifier votre propre chemin d’accès au fichier de configuration à l’aide d'un des éléments suivants :

* variable d’environnement `APPLICATIONINSIGHTS_CONFIGURATION_FILE`, ou
* propriété système Java `applicationinsights.configurationFile`

Si vous spécifiez un chemin d’accès relatif, il sera résolu par rapport au répertoire où se trouve `applicationinsights-agent-3.0.0-PREVIEW.5.jar`.

## <a name="connection-string"></a>Chaîne de connexion

Cela est nécessaire. Vous pouvez trouver votre chaîne de connexion dans votre ressource Application Insights :

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Chaîne de connexion Application Insights":::

Vous pouvez également définir la chaîne de connexion à l'aide de la variable d’environnement `APPLICATIONINSIGHTS_CONNECTION_STRING`.

## <a name="cloud-role-name"></a>Nom du rôle cloud

Le nom du rôle cloud est utilisé pour étiqueter le composant sur la cartographie d’application.

Si vous souhaitez définir le nom du rôle cloud :

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Si le nom du rôle cloud n’est pas défini, le nom de la ressource Application Insights sera utilisé pour étiqueter le composant sur la cartographie d’application.

Vous pouvez également définir le nom de rôle cloud à l'aide de la variable d’environnement `APPLICATIONINSIGHTS_ROLE_NAME`.

## <a name="cloud-role-instance"></a>Instance de rôle cloud

Par défaut, l’instance de rôle cloud correspond au nom de la machine.

Si vous souhaitez définir l'instance de rôle cloud sur autre chose que le nom de la machine :

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Vous pouvez également définir l'instance de rôle cloud à l'aide de la variable d’environnement `APPLICATIONINSIGHTS_ROLE_INSTANCE`.

## <a name="application-log-capture"></a>Capture du journal des applications

La préversion Application Insights Java 3.0 capture automatiquement la journalisation d'application via Log4j, Logback et java.util.logging.

Par défaut, elle capture toute la journalisation effectuée au niveau `WARN` ou supérieur.

Si vous souhaitez modifier ce seuil :

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Voici les valeurs `threshold` valides que vous pouvez spécifier dans le fichier `ApplicationInsights.json` et leur correspondance avec les niveaux de journalisation dans les différentes infrastructures de journalisation :

| valeur de seuil   | Log4j  | Logback | JUL     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | ERROR   | SEVERE  |
| ERROR (ou SEVERE) | ERROR  | ERROR   | SEVERE  |
| WARN (ou WARNING) | WARN   | WARN    | WARNING |
| INFO              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG (ou FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE (ou FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Métriques JMX

Si vous souhaitez capturer certaines métriques JMX :

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (y compris les métriques de Spring Boot Actuator)

Si votre application utilise [Micrometer](https://micrometer.io), Application Insights 3.0 (à partir de la préversion 2) capture désormais les métriques envoyées au registre global Micrometer.

Si votre application utilise [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), Application Insights 3.0 (à partir de la préversion 4) capture désormais les métriques configurées par Spring Boot Actuator (qui utilise Micrometer, mais n’utilise pas le registre global Micrometer).

Si vous souhaitez désactiver ces fonctionnalités :

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Heartbeat

Par défaut, la préversion Application Insights Java 3.0 envoie une métrique de pulsation toutes les 15 minutes. Si vous utilisez la métrique de pulsation pour déclencher des alertes, vous pouvez augmenter la fréquence de cette pulsation :

```json
{
  "instrumentationSettings": {
    "preview": {
      "heartbeat": {
        "intervalSeconds": 60
      }
    }
  }
}
```

> [!NOTE]
> Vous ne pouvez pas réduire la fréquence de cette pulsation, car les données de pulsation sont également utilisées pour suivre l’utilisation d'Application Insights.

## <a name="sampling"></a>échantillonnage

L’échantillonnage s'avère utile s'il vous faut réduire les coûts.
L’échantillonnage s'effectue sous forme de fonction sur l’ID d’opération (également appelé ID de trace) pour permettre au même ID d’opération de générer la même décision d’échantillonnage. Ainsi, vous n'obtenez pas de parties de transaction distribuée échantillonnées quand d'autres ne le sont pas.

Par exemple, si vous définissez l’échantillonnage sur 10 %, vous ne verrez que 10 % de vos transactions, mais chacun de ces 10 % présentera les détails des transactions de bout en bout.

Voici un exemple montrant comment définir l'échantillonnage sur **10 % de toutes les transactions**. Vérifiez que le taux d'échantillonnage est correct pour votre cas d’utilisation :

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10
        }
      }
    }
  }
}
```

## <a name="http-proxy"></a>Proxy HTTP

Si votre application se trouve derrière un pare-feu et n'est pas en mesure de se connecter directement à Application Insights (voir [Adresses IP utilisées par Application Insights](./ip-addresses.md)), vous pouvez configurer la préversion Application Insights Java 3.0 pour utiliser un proxy HTTP :

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Autodiagnostics

Les « autodiagnostics » font référence à la journalisation interne à partir de la préversion Application Insights Java 3.0.

Ils permettent de détecter et diagnostiquer les problèmes liés à Application Insights.

Par défaut, la journalisation intervient sur la console avec le niveau `warn`, correspondant à la configuration suivante :

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "console",
        "level": "WARN"
      }
    }
  }
}
```

Les niveaux valides sont `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG` et `TRACE`.

Si vous souhaitez procéder à la journalisation dans un fichier plutôt que sur la console :

```json
{
  "instrumentationSettings": {
    "preview": {
      "selfDiagnostics": {
        "destination": "file",
        "directory": "/var/log/applicationinsights",
        "level": "WARN",
        "maxSizeMB": 10
      }
    }
  }
}
```

En termes de journalisation de fichiers, lorsque le fichier atteint `maxSizeMB`, il se substitue, en conservant uniquement le fichier journal le plus récemment complété en plus du fichier journal actuel.
