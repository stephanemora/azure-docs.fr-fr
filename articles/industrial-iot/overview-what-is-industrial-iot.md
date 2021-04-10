---
title: Vue d’ensemble d’Azure Industrial IoT
description: Cet article fournit une vue d’ensemble de l’IoT industriel (IIoT). Cet article explique les composants de connectivité et de sécurité d’atelier dans l’IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 940391d26b5a8455fef01c8094cd08e05ab51290
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787434"
---
# <a name="what-is-industrial-iot-iiot"></a>Définition de l’IoT industriel (IIoT)

L’IIoT (IoT industriel) améliore l’efficacité industrielle par le biais de l’application de l’IoT dans le secteur de la fabrication.

![IoT industriel](media/overview-what-is-Industrial-IoT/icon-255-px.png)

## <a name="improve-industrial-efficiencies"></a>Améliorer l’efficacité industrielle
Améliorez votre productivité opérationnelle et votre rentabilité avec Azure Industrial IoT. Réduisez le processus chronophage d’accès aux ressources sur site. Connectez et analysez vos équipements et appareils industriels dans le cloud, notamment vos ordinateurs opérant au niveau de la production. Analysez vos données IoT pour en extraire des renseignements qui vous aident à augmenter le niveau de performance du site dans son ensemble.

## <a name="industrial-iot-components"></a>Composants IoT industriel

**Appareils IoT Edge** : un appareil IoT Edge est composé d’un Runtime Edge et de Modules Edge. 
- Les *Modules Edge* sont des conteneurs docker, qui sont les plus petites unités de calcul, comme OPC Publisher et OPC Twin. 
- L’*Appareil Edge* est utilisé pour déployer de tels modules, qui jouent le rôle de médiateur entre le serveur OPC UA et IoT Hub dans le cloud. Pour en savoir plus sur IoT Edge, cliquez [ici](https://azure.microsoft.com/services/iot-edge/).

**IoT Hub** : IoT Hub agit en tant que hub de messages central pour la communication bidirectionnelle entre une application IoT et les appareils qu’elle gère. Il s’agit d’une plateforme cloud ouverte et flexible en tant que service qui prend en charge les kits de développement logiciel (SDK) open source et plusieurs protocoles. En savoir plus sur IoT Hub, [ici](https://azure.microsoft.com/services/iot-hub/).

**Modules Edge industriels**
- *OPC Publisher* : OPC Publisher s’exécute dans IoT Edge. Il se connecte aux serveurs OPC UA et publie des données de télémétrie encodées JSON à partir de ces serveurs au format OPC UA « Pub/Sub » dans Azure IoT Hub. Tous les protocoles de transport pris en charge par le Kit de développement logiciel (SDK) du client Azure IoT Hub peuvent être utilisés, par exemple : HTTPS, AMQP et MQTT.
- *OPC Twin* : OPC Twin se compose de microservices qui utilisent Azure IoT Edge et IoT Hub pour connecter le cloud et le réseau de la fabrique. OPC Twin fournit la découverte, l’inscription et le contrôle à distance des appareils industriels par le biais d’API REST. OPC Twin n’a pas besoin d’un kit de développement logiciel (SDK) .OPC Unified Architecture (OPC UA). Il est indépendant du langage de programmation et peut être inclus dans un workflow serverless.
- *Découverte* : le module de découverte, représenté par l’identité du découvreur, fournit des services de découverte à la périphérie, qui incluent la détection de serveur OPC UA. Si la détection est configurée et activée, le module envoie les résultats d’une sonde d’analyse via le chemin de télémétrie IoT Edge et IoT Hub vers le service d’Intégration. Le service traite les résultats et met à jour toutes les identités associées dans le Registre.


**Découvrez, inscrivez et gérez vos ressources industrielles avec Azure** Azure Industrial IoT permet aux opérateurs d’usine de découvrir des serveurs OPC UA activés dans un réseau de fabrique et de les inscrire dans Azure IoT Hub. Le personnel des opérations peut s’abonner et réagir aux événements de la fabrique n’importe où dans le monde. Les API REST des microservices reflètent le côté périphérique des services OPC UA. Ils sont sécurisés avec l’authentification OAUTH et l’autorisation sauvegardée par Azure Active Directory (AAD). Cela permet à vos applications cloud de parcourir les espaces d’adressage du serveur ou de lire/écrire des variables et d’exécuter des méthodes à l’aide des charges utiles HTTPS et de simple JSON d’agent utilisateur OPC.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris ce qu’est l’IoT industriel, vous pouvez en savoir plus sur Industrial IoT Platform et OPC Publisher :

> [!div class="nextstepaction"]
> [Qu’est-ce que la plateforme Industrial IoT ?](overview-what-is-industrial-iot-platform.md)

> [!div class="nextstepaction"]
> [Qu’est-ce qu’OPC Publisher ?](overview-what-is-opc-publisher.md)