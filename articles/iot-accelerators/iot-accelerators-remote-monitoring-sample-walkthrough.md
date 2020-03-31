---
title: Vue d’ensemble des accélérateurs de solution de supervision à distance | Microsoft Docs
description: Cet article propose une vue d’ensemble de certains des éléments clés de la solution de supervision à distance pour vous permettre de comprendre son fonctionnement.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 33005bc286f7dc2c0ebed74bd9df0309e71346f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824829"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Vue d’ensemble des accélérateurs de solution de supervision à distance

[L’accélérateur de solution](../iot-accelerators/about-iot-accelerators.md) de supervision à distance implémente une solution de supervision de bout en bout, destinée à plusieurs ordinateurs distants. Combinant les principaux services Azure pour fournir une implémentation générique du scénario d’entreprise. Vous pouvez utiliser la solution comme point de départ pour votre propre implémentation et la [personnaliser](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) pour répondre à vos propres exigences professionnelles.

Cet article vous présente certains des éléments clés de la solution de supervision à distance pour vous permettre de comprendre son fonctionnement. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels.
* Concevoir votre propre solution IoT utilisant des services Azure.

Le code de l’accélérateur de solution de supervision à distance est disponible sur GitHub :

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Architecture logique

Le diagramme suivant montre l’articulation des composants logiques de l’accélérateur de solution de supervision à distance dans [l’architecture IoT](../iot-fundamentals/iot-introduction.md) :

![Architecture logique](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Intérêt des microservices

L’architecture cloud a évolué depuis que Microsoft a publié les premiers accélérateurs de solution. Les [microservices](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) ont émergé comme une pratique ayant fait ses preuves pour obtenir une mise à l’échelle et une flexibilité sans sacrifier la vitesse de développement. Plusieurs services de Microsoft utilisent ce modèle d’architecture en interne et obtiennent de très bons résultats en termes de scalabilité et de fiabilité. Les nouveaux accélérateurs de solution intègrent ces microservices pour que vous puissiez également en bénéficier.

> [!TIP]
> Pour en savoir plus sur les architectures de microservice, consultez [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architecture d’application .NET) et [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservices: une révolution des applications par le cloud).

## <a name="device-connectivity"></a>Connectivité des appareils

La solution inclut les composants suivants dans la partie de la connectivité des appareils de l’architecture logique :

### <a name="real-devices"></a>Appareils réels

Vous pouvez connecter des appareils réels à la solution. Vous pouvez implémenter le comportement de vos appareils simulés à l’aide des kits Azure IoT device SDK.

Vous pouvez approvisionner des appareils réels à partir du tableau de bord du portail des solutions.

### <a name="device-simulation-microservice"></a>Microservice de simulation d’appareil

La solution inclut le [microservice de simulation d’appareil](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) qui vous permet de gérer un pool d’appareils simulés à partir du portail de la solution pour tester le flux de bout en bout dans la solution. Les appareils simulés :

* Génèrent des données de télémétrie appareil-à-cloud.
* Répondent aux appels de méthode cloud-à-appareil provenant d’IoT Hub.

Le microservice fournit un point de terminaison RESTful vous permettant de créer, de démarrer et d’arrêter des simulations. Chaque simulation se compose d’un ensemble d’appareils virtuels de différents types, qui envoient des données de télémétrie et répondent aux appels de méthode.

Vous pouvez provisionner les appareils simulés à partir du tableau de bord dans le portail des solutions.

### <a name="iot-hub"></a>IoT Hub

Le [hub IoT](../iot-hub/index.yml) ingère les données de télémétrie envoyées par les appareils réels et simulés dans le cloud. Le hub IoT met les données de télémétrie à disposition des services dans le serveur principal de la solution IoT pour le traitement.

L’instance IoT Hub de la solution effectue également ce qui suit :

* Conserve un registre des identités contenant les identifiants et clés d’authentification de tous les appareils autorisés à se connecter au portail.
* Appelle des méthodes sur vos appareils pour le compte de l’accélérateur de la solution.
* Gère les représentations d’appareil pour tous les appareils inscrits. Une représentation d’appareil stocke les valeurs des propriétés signalées par un appareil. Une représentation d’appareil stocke également les propriétés souhaitées, définies dans le portail de la solution, que l’appareil récupère lors de sa connexion suivante.
* Planifie des travaux pour définir des propriétés pour plusieurs appareils ou appeler des méthodes sur plusieurs appareils.

## <a name="data-processing-and-analytics"></a>Traitement et analyse des données

La solution inclut les composants suivants dans la partie de traitement et d’analyse des données de l’architecture logique :

### <a name="iot-hub-manager-microservice"></a>Microservice de gestion du IoT Hub

La solution inclut le [microservice de gestion du IoT Hub](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) pour gérer les interactions avec votre IoT Hub. En voici quelques exemples :

* Création et gestion des appareils IoT
* Gestion des jumeaux d’appareil
* Appel de méthodes sur les appareils
* Gestion des informations d’identification IoT

Ce service exécute également des requêtes IoT Hub pour récupérer les appareils appartenant à des groupes définis par l’utilisateur.

Le microservice fournit un point de terminaison RESTful pour gérer les appareils et les jumeaux d’appareil, appeler des méthodes et exécuter des requêtes IoT Hub.

### <a name="device-telemetry-microservice"></a>Microservice de télémétrie des appareils

Le [microservice de télémétrie des appareils](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) fournit un point de terminaison RESTful pour l’accès en lecture à la télémétrie des appareils stockée dans Time Series Insights. Le point de terminaison RESTful permet également des opérations CRUD sur les règles et un accès en lecture/écriture pour les définitions d’alarme à partir du stockage.

### <a name="storage-adapter-microservice"></a>Microservice d’adaptateur de stockage

Le [microservice d’adaptateur de stockage](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) gère les paires clé-valeur, en extrayant la sémantique du service de stockage et en présentant une interface simple pour stocker des données de n’importe quel format à l’aide d’Azure Cosmos DB.

Les valeurs sont organisées en collections. Vous pouvez travailler sur des valeurs individuelles ou récupérer des collections entières. Les structures de données complexes sont sérialisées par les clients et gérées en tant que charge utile de texte simple.

Le service fournit un point de terminaison RESTful pour les opérations CRUD sur les paires clé-valeur .

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Les déploiements d'accélérateurs de solution utilisent [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) pour stocker les règles, les alertes, les paramètres de configuration et tous les autres stockages froids.

### <a name="azure-stream-analytics-manager-microservice"></a>Microservice de gestion d’Azure Stream Analytics

Le [microservice de gestion d’Azure Stream Analytics](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gère les travaux Azure Stream Analytics (ASA), y compris la définition de leur configuration, leur démarrage et leur arrêt ainsi que la supervision de leur état.

Le travail ASA est pris en charge par deux jeux de données de référence. L’un des jeux de données définit les règles et l’autre définit des groupes d’appareils. Les données de référence des règles sont générées à partir des informations gérées par le microservice de télémétrie des appareils. Le microservice de gestion d’Azure Stream Analytics transforme les règles de télémétrie en logique de traitement de flux.

Les données de référence des groupes d’appareils sont utilisées pour identifier le groupe de règles à appliquer à un message de télémétrie entrant. Les groupes d’appareils sont gérés par le microservice de configuration et utilisent des requêtes de jumeau d’appareil IoT Hub.

Les travaux ASA fournissent les données de télémétrie depuis les appareils connectés à Time Series Insights pour le stockage et l’analyse.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) est un moteur de traitement des événements qui vous permet d’examiner de grands volumes de données diffusées à partir d’appareils.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) stocke les données de télémétrie à partir des appareils connectés à l’accélérateur de solution. Il permet également de visualiser et d’interroger les données de télémétrie des appareils dans l’interface utilisateur web de solution.

> [!NOTE]
> Time Series Insights n’est pas encore disponible dans le cloud Azure Chine. Les nouveaux déploiements d’accélérateur de solution de supervision à distance dans le cloud Azure Chine utilisent Cosmos DB pour l’intégralité du stockage.

### <a name="configuration-microservice"></a>Microservice de configuration

Le [microservice de configuration](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fournit un point de terminaison RESTful pour les opérations CRUD sur les groupes d’appareils, les paramètres de solution et les paramètres utilisateur dans l’accélérateur de solution. Il fonctionne avec le microservice d’adaptateur de stockage pour conserver les données de configuration.

### <a name="authentication-and-authorization-microservice"></a>Microservice d’authentification et d’autorisation

Le [microservice d’authentification et d’autorisation](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gère les utilisateurs autorisés à accéder à l’accélérateur de solution. La gestion des utilisateurs peut être réalisée à l’aide de n’importe quel fournisseur de services d’identité qui prend en charge [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Les déploiements d’accélérateurs de solution utilisent [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) comme fournisseur OpenID Connect. Azure Active Directory stocke les informations utilisateur et fournit des certificats pour valider les signatures de jeton JWT.

## <a name="presentation"></a>Présentation

La solution inclut les composants suivants dans la partie présentation de l’architecture logique :

[L’interface utilisateur web est une application Javascript React](https://github.com/Azure/pcs-remote-monitoring-webui). L’application :

* Utilise Javascript React uniquement et s’exécute entièrement dans le navigateur.
* Est mise en forme en CSS.
* Interagit avec des microservices publics par le biais d’appels AJAX.

L’interface utilisateur présente toutes les fonctionnalités de l’accélérateur de solution et interagit avec d’autres microservices tels que ceux-ci :

* Le microservice d’authentification et d’autorisation pour protéger les données utilisateur.
* Le microservice de gestion du IoT Hub pour lister et gérer les appareils IoT.

L’interface utilisateur s’intègre à l’explorateur Azure Time Series Insights pour permettre l’interrogation et l’analyse des données de télémétrie des appareils.

Le microservice de configuration permet à l’interface utilisateur de stocker et de récupérer les paramètres de configuration.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez consulter le code source et la documentation de développement, commencez par l’un des deux dépôts GitHub :

* [Accélérateur de la solution Surveillance à distance avec Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Accélérateur de la solution Surveillance à distance avec Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagrammes détaillés de l’architecture de la solution :
* [Accélérateur de solution pour l’architecture de la solution Surveillance à distance](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Pour plus d’informations conceptuelles sur l’accélérateur de la solution de supervision à distance, consultez [Personnaliser l’accélérateur de solution](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
