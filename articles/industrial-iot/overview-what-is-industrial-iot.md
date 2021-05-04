---
title: Vue d’ensemble d’Azure Industrial IoT
description: Cet article fournit une vue d’ensemble de l’IoT industriel (IIoT). Cet article explique les composants de connectivité et de sécurité d’atelier dans l’IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: ce582f810f483f2e5d3fdda2c3379ecad3842d51
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813269"
---
# <a name="what-is-industrial-iot-iiot"></a>Définition de l’IoT industriel (IIoT)

![IoT industriel](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial Internet of Things (IIoT) est une suite de modules et de services Azure qui intègrent la puissance du cloud dans des ateliers d’usine et de fabrication. À l’aide d’interfaces ouvertes standard, telles que l’[Open Platform Communications Unified Architecture (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/), Azure IIoT vous offre la possibilité d’intégrer des données à partir de ressources et de capteurs (y compris ceux fonctionnant déjà dans votre atelier) dans le cloud Azure. En disposant de vos données dans le cloud, vous pouvez les utiliser de façon plus rapide et flexible en tant que feedback pour le développement de processus métiers et industriels de transformation.

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Découvrir, inscrire et gérer vos ressources industrielles avec Azure

Azure Industrial IoT permet aux opérateurs d’usine de découvrir des serveurs OPC UA activés dans un réseau de l’usine et de les inscrire dans Azure IoT Hub. Le personnel des opérations peut s’abonner aux événements de l’usine et y réagir partout dans le monde, et il peut recevoir des alertes et des alarmes et y réagir en temps réel.

IIoT fournit un ensemble de microservices qui implémentent les fonctionnalités OPC UA. Les API REST des microservices reflètent le côté périphérique des services OPC UA. Ils sont sécurisés avec l’authentification OAUTH et l’autorisation sauvegardée par Azure Active Directory (AAD). Cela permet à vos applications cloud de parcourir les espaces d’adressage du serveur ou de lire/écrire des variables et d’exécuter des méthodes à l’aide des charges utiles HTTPS et de simple JSON d’agent utilisateur OPC. Les services à la périphérie sont implémentés en tant que modules Azure IoT Edge et exécutés en local. Les microservices Cloud sont implémentés en tant que microservices ASP.NET avec une interface REST et exécutés sur des services Azure Kubernetes gérés ou autonomes sur Azure App Service. Pour les services à la périphérie et dans le cloud, IIoT fournit les conteneurs Docker prédéfinis dans le Microsoft Container Registry (MCR), en supprimant cette étape pour le client. Les services à la périphérie et dans le cloud s’exploitent l’un l’autre et doivent être utilisés ensemble. IIoT fournit également des scripts de déploiement faciles à utiliser qui permettent de déployer l’ensemble de la plateforme à l’aide d’une seule commande.

De plus, les API REST peuvent être utilisées avec n’importe quel langage de programmation via sa spécification Open API exposée (Swagger). Cela signifie que lors de l’intégration d’OPC UA dans les solutions de gestion cloud, les développeurs sont libres de choisir une technologie qui correspond à leurs compétences, leurs centres d’intérêt et leurs choix d’architecture. Par exemple, un développeur web « full stack » qui développe une application pour un tableau de bord d’alarmes et d’événements peut écrire la logique permettant de répondre aux événements en JavaScript ou TypeScript à l’aide d’OPC Twin sans se former à C, C++, Java, C# ni au kit SDK OPC UA.

## <a name="manage-certificates-and-trust-groups"></a>Gérer les certificats et les groupes de confiance

Azure Industrial IoT gère les certificats d’application OPC UA et les listes d’approbation des systèmes de contrôle et des machines de l’atelier pour assurer la sécurité de la communication entre le client et le serveur OPC UA. Il détermine quel client est autorisé à communiquer avec quel serveur. Le stockage des clés privées et la signature des certificats sont assurés par Azure Key Vault, qui prend en charge la sécurité matérielle (HSM).

## <a name="industrial-iot-components"></a>Composants IoT industriel

Les solutions Azure IIoT sont créées à partir de composants spécifiques. Elles incluent les suivantes :

- **Au moins un hub Azure IoT.**
- **Appareils IoT Edge.**
- **Modules périphériques industriels.**

### <a name="iot-hub"></a>IoT Hub
[Azure IoT Hub] (https://azure.microsoft.com/services/iot-hub/ ) fait office de hub de messages central pour une communication bidirectionnelle sécurisée entre une application IoT et les appareils qu’elle gère. Il s’agit d’une plateforme cloud en tant que service (PaaS) ouverte et flexible qui prend en charge les kits SDK open source et plusieurs protocoles. 

La collecte de vos données industrielles et professionnelles sur un hub IoT vous permet de stocker vos données de manière sécurisée, d’effectuer des analyses commerciales et d’efficacité, et d’en générer des rapports. Vous pouvez également appliquer des services et des outils Microsoft Azure, tels que [Power BI](https://powerbi.microsoft.com), sur vos données regroupées.

### <a name="iot-edge-devices"></a>Appareils IoT Edge
Les [services à la périphérie](https://azure.microsoft.com/services/iot-edge/) sont implémentés en tant que modules Azure IoT Edge et exécutés localement. Les microservices Cloud sont implémentés en tant que microservices ASP.NET avec une interface REST et exécutés sur des services Azure Kubernetes gérés ou autonomes sur Azure App Service. Pour les services à la périphérie et dans le cloud, nous avons fourni les conteneurs Docker prédéfinis dans le Microsoft Container Registry (MCR), en supprimant cette étape pour le client. Les services à la périphérie et dans le cloud s’exploitent l’un l’autre et doivent être utilisés ensemble. Nous avons également fourni des scripts de déploiement faciles à utiliser qui permettent de déployer l’ensemble de la plateforme à l’aide d’une seule commande.

Un appareil IoT Edge est composé d’un runtime Edge et de modules Edge.
- Les **Modules Edge** sont des conteneurs docker, qui sont les plus petites unités de calcul, comme OPC Publisher et OPC Twin. 
- L’**Appareil Edge** est utilisé pour déployer de tels modules, qui jouent le rôle de médiateur entre le serveur OPC UA et IoT Hub dans le cloud.

### <a name="industrial-edge-modules"></a>Modules Edge industriels
- **OPC Publisher** : OPC Publisher s’exécute dans IoT Edge. Il se connecte aux serveurs OPC UA et publie des données de télémétrie encodées JSON à partir de ces serveurs au format OPC UA « Pub/Sub » dans Azure IoT Hub. Tous les protocoles de transport pris en charge par le Kit de développement logiciel (SDK) du client Azure IoT Hub peuvent être utilisés, par exemple : HTTPS, AMQP et MQTT.
- **OPC Twin** : OPC Twin se compose de microservices qui utilisent Azure IoT Edge et IoT Hub pour connecter le cloud et le réseau de la fabrique. OPC Twin fournit la découverte, l’inscription et le contrôle à distance des appareils industriels par le biais d’API REST. OPC Twin n’a pas besoin d’un kit de développement logiciel (SDK) .OPC Unified Architecture (OPC UA). Il est indépendant du langage de programmation et peut être inclus dans un workflow serverless.
- **Découverte** : le module de découverte, représenté par l’identité du découvreur, fournit des services de découverte à la périphérie, qui incluent la détection de serveur OPC UA. Si la détection est configurée et activée, le module envoie les résultats d’une sonde d’analyse via le chemin de télémétrie IoT Edge et IoT Hub vers le service d’Intégration. Le service traite les résultats et met à jour toutes les identités associées dans le Registre.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris ce qu’est l’IoT industriel, vous pouvez en savoir plus sur Industrial IoT Platform et OPC Publisher :

> [!div class="nextstepaction"]
> [Qu’est-ce qu’OPC Publisher ?](overview-what-is-opc-publisher.md)