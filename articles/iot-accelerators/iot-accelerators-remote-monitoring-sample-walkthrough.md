---
title: Vue d’ensemble des accélérateurs de solution de surveillance à distance | Microsoft Docs
description: Cet article présente le déploiement de l’accélérateur de solution de surveillance à distance.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: dfe584532efeab1dbc0d2928b7afb0a6695a21ee
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39184943"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Vue d’ensemble des accélérateurs de solution de surveillance à distance

[L’accélérateur de solution](../iot-accelerators/about-iot-accelerators.md) de surveillance à distance implémente une solution de surveillance de bout en bout, destinée à plusieurs ordinateurs distants. Combinant les principaux services Azure pour fournir une implémentation générique du scénario d’entreprise. Vous pouvez utiliser la solution comme point de départ pour votre propre implémentation et la [personnaliser](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) pour répondre à vos propres exigences professionnelles.

Cet article vous présente certains des éléments clés de la solution Surveillance à distance pour vous permettre de comprendre son fonctionnement. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels.
* Concevoir votre propre solution IoT utilisant des services Azure.

## <a name="logical-architecture"></a>Architecture logique

Le diagramme suivant montre l’articulation des composants logiques de l’accélérateur de solution Surveillance à distance dans [l’architecture IoT](../iot-fundamentals/iot-introduction.md) :

![Architecture logique](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Intérêt des microservices

L’architecture cloud a évolué depuis que Microsoft a publié les premiers accélérateurs de solution. Les [microservices](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) ont émergé comme une pratique ayant fait ses preuves pour obtenir une mise à l’échelle et une flexibilité sans sacrifier la vitesse de développement. Plusieurs services de Microsoft utilisent ce modèle d’architecture en interne et obtiennent de très bons résultats en termes de scalabilité et de fiabilité. Les nouveaux accélérateurs de solution intègrent ces microservices pour que vous puissiez également en bénéficier.

> [!TIP]
> Pour en savoir plus sur les architectures de microservice, consultez [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architecture d’application .NET) et [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: une révolution des applications par le cloud).

## <a name="device-connectivity"></a>Connectivité des appareils

La solution inclut les composants suivants dans la partie de la connectivité des appareils de l’architecture logique :

### <a name="simulated-devices"></a>Simulations d’appareils

La solution inclut un microservice qui vous permet de gérer un pool d’appareils simulés pour tester le flux de bout en bout dans la solution. Les appareils simulés :

* Génèrent des données de télémétrie appareil-à-cloud.
* Répondent aux appels de méthode cloud-à-appareil provenant d’IoT Hub.

Le microservice fournit un point de terminaison RESTful vous permettant de créer, de démarrer et d’arrêter des simulations. Chaque simulation se compose d’un ensemble d’appareils virtuels de différents types, qui envoient des données de télémétrie et répondent aux appels de méthode.

Vous pouvez provisionner les appareils simulés à partir du tableau de bord dans le portail des solutions.

### <a name="physical-devices"></a>Appareils physiques

Vous pouvez connecter des appareils physiques à la solution. Vous pouvez implémenter le comportement de vos appareils simulés à l’aide des kits Azure IoT device SDK.

Vous pouvez provisionner les appareils physiques à partir du tableau de bord dans le portail des solutions.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub et le microservice iot-manager

[IoT Hub](../iot-hub/index.yml) ingère les données envoyées par les appareils au cloud et les met à disposition dans le microservice `telemetry-agent`.

L’instance IoT Hub de la solution effectue également ce qui suit :

* Conserve un registre des identités contenant les identifiants et clés d’authentification de tous les appareils autorisés à se connecter au portail. Vous pouvez activer et désactiver des appareils via le Registre des identités.
* Appelle des méthodes sur vos appareils pour le compte du portail de la solution.
* Gère les représentations d’appareil pour tous les appareils inscrits. Une représentation d’appareil stocke les valeurs des propriétés signalées par un appareil. Une représentation d’appareil stocke également les propriétés souhaitées, définies dans le portail de la solution, que l’appareil récupère lors de sa connexion suivante.
* Planifie des travaux pour définir des propriétés pour plusieurs appareils ou appeler des méthodes sur plusieurs appareils.

La solution inclut le microservice `iot-manager` pour gérer les interactions avec votre hub IoT telles que les suivantes :

* Création et gestion des appareils IoT
* Gestion des jumeaux d’appareil
* Appel de méthodes sur les appareils
* Gestion des informations d’identification IoT

Ce service exécute également des requêtes IoT Hub pour récupérer les appareils appartenant à des groupes définis par l’utilisateur.

Le microservice fournit un point de terminaison RESTful pour gérer les appareils et les jumeaux d’appareil, appeler des méthodes et exécuter des requêtes IoT Hub.

## <a name="data-processing-and-analytics"></a>Traitement et analyse des données

La solution inclut les composants suivants dans la partie de traitement et d’analyse des données de l’architecture logique :

### <a name="device-telemetry"></a>Télémétrie d’appareil

La solution inclut deux microservices pour gérer la télémétrie d’appareil.

Le microservice [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) :

* Stocke les données de télémétrie dans Azure Cosmos DB.
* Analyse le flux des données de télémétrie issu des appareils.
* Génère des alarmes en fonction des règles définies.

Les alarmes sont stockées dans Azure Cosmos DB.

Le microservice [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) permet au portail de la solution de lire les données de télémétrie envoyées à partir des appareils. Le portail de solutions utilise également ce service pour :

* Définir des règles de surveillance telles que les seuils qui déclenchent des alarmes.
* Récupérer la liste des alarmes passées.

Utilisez le point de terminaison RESTful fourni par ce microservice pour gérer les données de télémétrie, les règles et les alarmes.

### <a name="storage"></a>Stockage

Le microservice [storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) est un adaptateur situé devant le service de stockage principal, et utilisé pour l’accélérateur de solution. Son rôle se résume aux opérations de collecte et de stockage de paires clé/valeur.

Le déploiement standard de l’accélérateur de solution utilise Azure Cosmos DB comme service de stockage principal.

La base de données Azure Cosmos DB stocke les données de l’accélérateur de solution. Le microservice **storage-adapter** fait office d’adaptateur permettant aux autres microservices dans la solution d’accéder aux services de stockage.

## <a name="presentation"></a>Présentation

La solution inclut les composants suivants dans la partie présentation de l’architecture logique :

[L’interface utilisateur web est une application Javascript React](https://github.com/Azure/pcs-remote-monitoring-webui). L’application :

* Utilise Javascript React uniquement et s’exécute entièrement dans le navigateur.
* Est mise en forme en CSS.
* Interagit avec des microservices publics par le biais d’appels AJAX.

L’interface utilisateur présente toutes les fonctionnalités de l’accélérateur de solution et interagit avec d’autres services tels que ceux-ci :

* Le microservice [authentication](https://github.com/Azure/pcs-auth-dotnet) pour protéger les données utilisateur
* Le microservice [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) pour lister et gérer les appareils IoT

Le microservice [ui-config](https://github.com/Azure/pcs-config-dotnet) permet à l’interface utilisateur de stocker et de récupérer les paramètres de configuration.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez consulter le code source et la documentation de développement, commencez avec l’un des deux principaux dépôts GitHub :

* [Accélérateur de la solution Surveillance à distance avec Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Accélérateur de la solution Surveillance à distance avec Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagrammes détaillés de l’architecture de la solution :
* [Accélérateur de solution pour l’architecture de la solution Surveillance à distance](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Pour plus d’informations conceptuelles sur l’accélérateur de la solution Surveillance à distance, consultez [Personnaliser l’accélérateur de solution](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
