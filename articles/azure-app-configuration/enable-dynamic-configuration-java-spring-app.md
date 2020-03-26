---
title: Utiliser la configuration dynamique dans une application Spring Boot
titleSuffix: Azure App Configuration
description: Découvrez comment mettre à jour dynamiquement les données de configuration pour les applications Spring Boot.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216756"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutoriel : Utiliser la configuration dynamique dans une application Java Spring

La bibliothèque de client Spring Boot App Configuration permet d’effectuer la mise à jour à la demande d’un ensemble de paramètres de configuration, sans entraîner le redémarrage de l’application. La bibliothèque de client met en cache chaque paramètre pour éviter un trop grand nombre d’appels au magasin de configurations. Tant que la valeur mise en cache d’un paramètre n’a pas expiré, l’opération d’actualisation ne met pas à jour la valeur, même si celle-ci a été modifiée dans le magasin de configurations. Par défaut, le délai d’expiration de chaque requête est de 30 secondes. Au besoin, ce délai peut être remplacé.

Vous pouvez vérifier à la demande les paramètres mis à jour en appelant la méthode `refreshConfigurations()` de `AppConfigurationRefresh`.

Vous pouvez également utiliser le package `spring-cloud-azure-appconfiguration-config-web`, qui prend une dépendance sur `spring-web` pour gérer l’actualisation automatisée.

## <a name="use-automated-refresh"></a>Utiliser l’actualisation automatisée

Pour utiliser l’actualisation automatisée, démarrez par une application Spring Boot qui utilise App Configuration, telle que l’application que vous pouvez créer en suivant le [guide de démarrage rapide Spring Boot pour App Configuration](quickstart-java-spring-app.md).

Ensuite, ouvrez le fichier *pom.xml* dans un éditeur de texte, puis ajoutez un `<dependency>` pour `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Enregistrez le fichier, puis créez et exécutez votre application comme d’habitude.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez permis à votre application Spring Boot d’actualiser dynamiquement les paramètres de configuration à partir d’App Configuration. Pour savoir comment utiliser une identité managée Azure afin de simplifier l’accès à App Configuration, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Intégration des identités managées](./howto-integrate-azure-managed-service-identity.md)
