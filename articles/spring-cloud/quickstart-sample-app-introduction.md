---
title: Démarrage rapide - Présentation de l’exemple d’application - Azure Spring Cloud
description: Décrit l’exemple d’application utilisé dans cette série de guides de démarrage rapide concernant le déploiement dans Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 6c1837b6992090f1b02d89720db298fe5714d4c3
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108286494"
---
# <a name="introduction-to-the-sample-app"></a>Présentation de l’exemple d’application

::: zone pivot="programming-language-csharp"
Cette série de guides de démarrage rapide utilise un exemple d’application composé de deux microservices afin de montrer comment déployer une application .NET Core Steeltoe dans le service Azure Spring Cloud. Vous utiliserez les fonctionnalités Azure Spring Cloud, telles que la découverte de service, le serveur de configuration, les journaux, les métriques et le suivi distribué.

## <a name="functional-services"></a>Services fonctionnels

L’exemple d’application est composé de deux microservices :

* Le service `planet-weather-provider` retourne du texte relatif au climat en réponse à une requête HTTP qui spécifie le nom de la planète. Par exemple, il peut retourner « très chaud » pour la planète Mercure. Il obtient les données météorologiques du serveur de configuration. Le serveur de configuration obtient les données météorologiques d’un fichier YAML situé dans un dépôt Git, par exemple :

  ```yaml
  MercuryWeather: very warm
  VenusWeather: quite unpleasant
  MarsWeather: very cool
  SaturnWeather: a little bit sandy
  ```

* Le service `solar-system-weather` retourne des données pour quatre planètes en réponse à une requête HTTP. Il obtient les données en envoyant quatre requêtes HTTP à `planet-weather-provider`. Il utilise le service de découverte de serveur Eureka pour appeler `planet-weather-provider`. Il retourne du code JSON, par exemple :

  ```json
  [{
      "Key": "Mercury",
      "Value": "very warm"
  }, {
      "Key": "Venus",
      "Value": "quite unpleasant"
  }, {
      "Key": "Mars",
      "Value": "very cool"
  }, {
      "Key": "Saturn",
      "Value": "a little bit sandy"
  }]
  ```

Le diagramme suivant montre l’architecture de l’exemple d’application.

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Diagramme de l’exemple d’application":::

## <a name="code-repository"></a>Dépôt de code

L’exemple d’application se trouve dans le dossier [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) du dépôt Azure-Samples/Azure-Spring-Cloud-Samples situé sur GitHub.

Les instructions fournies dans les guides de démarrage rapide suivants font référence au code source.

::: zone-end

::: zone pivot="programming-language-java"
Dans ce guide de démarrage rapide, nous utilisons la version de microservices de l’exemple d’application [PetClinic](https://github.com/spring-petclinic/spring-petclinic-microservices) connu qui vous montrera comment déployer des applications sur le service Azure Spring Cloud. L’exemple **PetClinic** présente le modèle d’architecture de microservices et met en évidence la décomposition des services. Vous verrez comment les services sont déployés sur Azure avec les fonctionnalités Azure Spring Cloud, notamment la découverte de service, le serveur de configuration, les journaux, les métriques, le suivi distribué et la prise en charge d’outils conviviaux pour les développeurs. 

Pour suivre les exemples de déploiement Azure Spring Cloud, vous avez seulement besoin de l’emplacement du code source, qui est fourni quand c’est nécessaire.

![Architecture de PetClinic](media/build-and-deploy/microservices-architecture-diagram.jpg)

## <a name="functional-services-to-be-deployed"></a>Services fonctionnels à déployer

PetClinic est décomposée en 4 microservices principaux. Ce sont tous des applications qui peuvent être déployées de façon indépendante et organisées par domaine métier.

* **Service clients** : contient la logique générale et la validation des entrées utilisateur, notamment les informations sur les animaux et les propriétaires (Nom, Adresse, Ville, Téléphone).
* **Service des visites** : stocke et affiche les informations sur les visites concernant les commentaires de chaque animal.
* **Service des vétérinaires** : stocke et affiche les informations sur les vétérinaires, notamment les noms et les spécialisations.
* **Passerelle API** : il s’agit d’un point d’entrée unique dans le système, utilisé pour traiter les demandes et les router vers un service approprié ou pour appeler plusieurs services, puis agréger les résultats.  Les trois services principaux exposent une API externe au client. Dans les vrais systèmes, le nombre de fonctions peut grimper très rapidement avec la complexité du système. Des centaines de services peuvent être impliqués dans le rendu d’une page web complexe. 

## <a name="infrastructure-services-hosted-by-azure-spring-cloud"></a>Services d’infrastructure hébergés par Azure Spring Cloud

Il existe plusieurs modèles courants dans les systèmes distribués qui prennent en charge les services principaux. Azure Spring Cloud fournit des outils qui améliorent les applications Spring Boot pour implémenter les modèles suivants : 

* **Service de configuration** : la configuration Azure Spring Cloud est un service de configuration centralisé avec scalabilité horizontale pour les systèmes distribués. Il utilise un dépôt enfichable qui prend actuellement en charge le stockage local, Git et Subversion.
* **Découverte des services** : elle permet la détection automatique des emplacements réseau pour les instances de service, qui pourraient avoir des adresses affectées dynamiquement en raison de la mise à l’échelle automatique, des défaillances et des mises à niveau.

## <a name="database-configuration"></a>Configuration de la base de données
Dans sa configuration par défaut, **PetClinic** utilise une base de données en mémoire (HSQLDB) qui est renseignée au démarrage avec des données. Une configuration similaire est fournie pour MySql si une configuration de base de données persistante est nécessaire. La dépendance pour le Connecteur/J, le pilote JDBC MySQL, est déjà incluse dans les fichiers pom.xml.

## <a name="sample-usage-of-petclinic"></a>Exemple d’utilisation de PetClinic

Pour obtenir tous les détails sur l’implémentation, consultez notre branche de [PetClinic](https://github.com/Azure-Samples/spring-petclinic-microservices). Les exemples référencent le code source quand c’est nécessaire.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Provisionner une instance Azure Spring Cloud](./quickstart-provision-service-instance.md)