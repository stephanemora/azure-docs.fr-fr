---
title: Démarrage rapide - Introduction à l’exemple d’application Piggy Metrics - Azure Spring Cloud
description: Décrit l’exemple d’application Piggy Metrics utilisé dans le déploiement Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046780"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Présentation de l’exemple d’application Piggy Metrics

Dans ce guide de démarrage rapide, nous utilisons un exemple d’application de finances personnelles appelée Piggy Metrics pour vous montrer comment déployer une application sur le service Azure Spring Cloud. Piggy Metrics illustre le modèle d’architecture de microservices et met en évidence la décomposition des services. Vous verrez comment elle est déployée sur Azure avec les puissantes fonctionnalités d’Azure Spring Cloud, y compris la découverte de service, le serveur de configuration, les journaux, les métriques et le suivi distribué.

Pour suivre les exemples de déploiement Azure Spring Cloud, vous avez seulement besoin de l’emplacement du code source, qui est fourni quand c’est nécessaire.

## <a name="functional-services"></a>Services fonctionnels
Piggy Metrics se décompose en trois microservices principaux. Ce sont tous des applications qui peuvent être déployées de façon indépendante et organisées par domaine métier.

* **Service de compte (à déployer)**  : Contient la logique générale et la validation des entrées utilisateur : éléments des revenus/dépenses, économies et paramètres de compte.
* **Service de statistiques (non utilisé dans ce guide de démarrage rapide)**  : Effectue des calculs sur les paramètres statistiques majeurs et capture les séries chronologiques pour chaque compte. Datapoint contient des valeurs normalisées selon une devise de base et une période de temps. Ces données sont utilisées pour effectuer le suivi de la dynamique des flux de trésorerie pendant la durée de vie du compte.
* **Service de notification (non utilisé dans ce guide de démarrage rapide)**  : Stocke les informations de contact et les paramètres de notification des utilisateurs, comme la fréquence des rappels et des sauvegardes. Le Worker planifié collecte les informations nécessaires auprès d’autres services et envoie des messages par e-mail aux clients abonnés.

## <a name="infrastructure-services"></a>Services d’infrastructure
Il existe plusieurs modèles courants dans les systèmes distribués qui facilitent le fonctionnement des services de base. Azure Spring Cloud fournit des outils puissants qui améliorent le comportement des applications Spring Boot pour implémenter ces modèles : 

* **Service de configuration (hébergé par Azure Spring Cloud)**  : Le service de configuration Azure Spring Cloud est un service de configuration centralisé avec scalabilité horizontale pour les systèmes distribués. Il utilise un dépôt enfichable qui prend actuellement en charge le stockage local, Git et Subversion.
* **Service de découverte (hébergé par Azure Spring Cloud)**  : Il permet la détection automatique des emplacements réseau pour les instances de service, qui pourraient avoir des adresses affectées dynamiquement en raison de la mise à l’échelle automatique, des défaillances et des mises à niveau.
* **Service d’authentification (à déployer)** Les responsabilités liées aux autorisations sont complètement déportées sur un serveur distinct, qui accorde des jetons OAuth2 pour les services des ressources de back-end. Le serveur d’authentification traite les autorisations utilisateur et la communication entre machines au sein d’un périmètre.
* **Passerelle d’API (à déployer)**  : Les trois services principaux exposent une API externe au client. Dans les vrais systèmes, le nombre de fonctions peut grimper très rapidement avec la complexité du système. Des centaines de services peuvent être impliqués dans le rendu d’une page web complexe. La passerelle d’API est un point d’entrée unique dans le système, utilisé pour traiter les demandes et les router vers le service de back-end approprié ou pour appeler plusieurs services de back-end, en agrégeant les résultats. 

## <a name="sample-usage-of-piggy-metrics"></a>Exemple d’utilisation de Piggy Metrics
Pour plus d’informations sur l’implémentation complète, consultez [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics). Les exemples référencent le code source quand c’est nécessaire.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Provisionner une instance Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
