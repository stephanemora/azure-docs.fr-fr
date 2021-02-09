---
title: Journal des applications structuré pour Azure Spring Cloud | Microsoft Docs
description: Cet article explique comment générer et collecter des données de journal structuré des applications dans Azure Spring Cloud.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: brendanm
ms.custom: devx-track-java
ms.openlocfilehash: c5c35fe8a352a1bc3467e9512a7fcbc068375bfb
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056778"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Journal des applications structuré pour Azure Spring Cloud

Cet article explique comment générer et collecter des données de journal structuré des applications dans Azure Spring Cloud. Avec une configuration adéquate, Azure Spring Cloud permet d’interroger et d’analyser les journaux des applications grâce à Log Analytics.

## <a name="log-schema-requirements"></a>Configuration requise pour le schéma des journaux
Afin d’améliorer l’expérience d’interrogation des journaux, un journal d’application doit être au format JSON et conforme à un schéma. Azure Spring Cloud utilise ce schéma pour analyser votre application et transmettre le résultat à Log Analytics. 

**Configuration requise pour le schéma JSON :**

| Clé JSON      | Type de valeur JSON|  Obligatoire | Colonne dans Log Analytics| Description |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | string      |     Oui   | AppTimestamp    | timestamp au format UTC  |
| logger        | string      |     Non    | Enregistreur          | logger                   |
| niveau         | string      |     Non    | CustomLevel     | log level                |
| thread        | string      |     Non    | Thread          | thread                   |
| message       | string      |     Non    | Message         | message du journal              |
| stackTrace    | string      |     Non    | StackTrace      | arborescence des appels de procédure des exceptions    |
| exceptionClass| string      |     Non    | ExceptionClass  | nom de la classe d’exception     |
| mdc           | JSON imbriqué |     Non    |                 | contexte de diagnostic mappé|
| mdc.traceId   | string      |     Non    | TraceId         |ID de trace pour le suivi distribué|
| mdc.spanId    | string      |     Non    | SpanId          |ID d’étendue pour le suivi distribué |
|               |             |           |                 |                          |

* Le champ « timestamp » est obligatoire et doit être au format UTC, tous les autres champs sont facultatifs.
* « traceId » et « spanId » dans le champ « mdc » sont utilisés à des fins de suivi.
* Consignez chaque enregistrement JSON sur une seule ligne. 

**Exemple d’enregistrement de journal** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Générer un journal JSON conforme au schéma  
Pour les applications Spring, vous pouvez générer le format de journal JSON attendu en utilisant des [frameworks de journalisation courants](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), tels que [logback](http://logback.qos.ch/) et [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Consigner avec logback 
Lorsque vous utilisez des démarreurs Spring Boot, logback est utilisé par défaut. Pour les applications logback, utilisez [logstash-encoder](https://github.com/logstash/logstash-logback-encoder) pour générer un journal au format JSON. Cette méthode est prise en charge dans Spring Boot version 2.1+. 

La procédure :

1. Ajoutez la dépendance logstash dans votre fichier pom.xml. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Mettez à jour votre fichier config logback.xml pour définir le format JSON.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Consigner avec log4j2 

Pour les applications log4j2, utilisez [json-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) pour générer un journal au format JSON. Cette méthode est prise en charge dans Spring Boot version 2.1+.

La procédure :

1. Excluez `spring-boot-starter-logging` de `spring-boot-starter`, ajoutez des dépendances `spring-boot-starter-log4j2` et `log4j-layout-template-json` dans votre fichier pom.xml.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Préparez un fichier de modèle de mise en page JSON jsonTemplate.json dans votre chemin d’accès de classe.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Utilisez ce modèle de mise en page JSON dans votre fichier config log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analyser les journaux dans Log Analytics

Une fois que votre application est correctement configurée, le journal de votre console d’application sera transmis en continu à Log Analytics. La structure permet une interrogation efficace dans Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Vérifier la structure du journal dans Log Analytics
Procédez comme suit :
1. Accédez à la page de la vue d’ensemble du service de votre instance de service.
2. Cliquez sur l’entrée `Logs` sous la section `Monitoring`.
3. Exécutez cette requête.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Les journaux des applications sont renvoyés comme indiqué dans l’image suivante :

![Illustration du journal au format JSON](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Afficher les entrées de journal contenant des erreurs

Pour consulter les entrées de journal qui contiennent une erreur, exécutez la requête suivante :

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Utilisez cette requête pour rechercher des erreurs ou modifiez les termes de la requête pour rechercher une classe d’exception ou un code d’erreur spécifique. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Afficher les entrées de journal pour un traceId spécifique
Pour consulter les entrées de journal d’un ID de suivi « trace_id » spécifique, exécutez la requête suivante :

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’interrogation du journal, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries).
