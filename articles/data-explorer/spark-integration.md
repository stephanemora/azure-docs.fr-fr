---
title: Connecteur Azure Data Explorer pour Apache Spark
description: Cet article explique comment utiliser le connecteur Azure Data Explorer pour Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824970"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Connecteur Azure Data Explorer pour Apache Spark

À l'aide d'Azure Data Explorer et d'Apache Spark, vous pouvez rapidement créer des applications évolutives ciblant des scénarios axés sur les données, comme le Machine Learning (ML), l'extraction, la transformation et le chargement (ETL) et Log Analytics.

## <a name="prerequisites"></a>Prérequis

Pour utiliser le connecteur, votre application doit disposer de ce qui suit :

* Kit de développement logiciel (SDK) Java 1.8
* Maven 3.x
* Spark version 2.3.2 ou version ultérieure

## <a name="link-to-data-explorer"></a>Lien vers Data Explorer

Pour les applications Scala et Java utilisant des définitions de projet Maven, liez votre application à l'artefact suivant :

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

Dans Maven, créez un lien avec ce qui suit :

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Commandes de génération

Pour générer un fichier jar et exécuter tous les tests :

```
mvn clean package
```

Pour générer un fichier jar, exécuter tous les tests et installer le fichier jar dans votre référentiel Maven local :

```
mvn clean install
```