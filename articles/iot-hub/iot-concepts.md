---
title: Concepts de base pour les nouveaux utilisateurs Azure IoT Hub | Microsoft Docs
description: Cet article présente les concepts de base pour les nouveaux utilisateurs d’Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: 28c91ee86a2657e5483d6d0287be8d42bd1fac23
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562214"
---
# <a name="iot-concepts"></a>Concepts de l’IoT

IoT Hub est un service managé, hébergé dans le cloud, qui joue le rôle de hub de messages central pour la communication bidirectionnelle entre une application IoT et ses appareils attachés. Vous pouvez connecter des millions d’appareils et leurs solutions back-end de manière fiable et sécurisée. Presque tous les appareils peuvent être connectés à un hub IoT. 

Plusieurs modèles de messagerie sont pris en charge, notamment la télémétrie appareil-à-cloud, le chargement de fichiers à partir d’appareils et les méthodes demande-réponse pour contrôler vos appareils à partir du cloud. IoT Hub prend également en charge la supervision pour vous aider à effectuer le suivi de la création des appareils, de leur connexion et de leurs défaillances.

Les fonctionnalités d’IoT Hub vous permettent de générer des solutions IoT évolutives et complètes, notamment de gérer l’équipement industriel utilisé dans la fabrication, de suivre des ressources précieuses dans le secteur de la santé et de superviser l’utilisation de bâtiments de bureau.

Les appareils IoT ont des caractéristiques différentes de celles d’autres clients tels que les navigateurs et les applications mobiles. Les [kits SDK d’appareil](iot-hub-devguide-sdks.md) vous permettent de connecter des appareils au service back-end de manière fiable et sécurisée, ce qui peut parfois représenter un véritable défi. 

Plus précisément, les appareils IoT :

- sont souvent des systèmes intégrés, qui ne font appel à aucun opérateur humain (contrairement à un téléphone) ;
- peuvent être déployés sur des sites distants avec un accès physique coûteux ;
- sont accessibles uniquement via le serveur principal de la solution ;
- peuvent avoir des performances et/ou des ressources de traitement limitées ;
- peuvent avoir une connectivité réseau intermittente, lente ou coûteuse ;
- peuvent nécessiter l’utilisation des protocoles d’application personnalisés, propriétaires ou spécifiques à un secteur.

## <a name="securely-connect-and-communicate"></a>se connectent et communiquent en toute sécurité

L’authentification par appareil permet à chaque appareil de se connecter en toute sécurité à IoT Hub et d’être managé en toute sécurité. Vous avez un contrôle complet sur l’accès à l’appareil et pouvez contrôler les connexions au niveau de l’appareil.

### <a name="devices-have-a-secure-identity"></a>Les appareils ont une identité sécurisée

Chaque hub IoT a un registre des identités contenant des informations sur les appareils et modules autorisés à se connecter au hub IoT. Pour qu’un appareil ou module puisse se connecter à un hub IoT, une entrée correspondant à cet appareil ou module doit figurer dans le registre des identités du hub IoT. Un appareil ou module doit également s’authentifier auprès du hub IoT à l’aide des informations d’identification stockées dans le registre des identités.

Nous prenons en charge deux méthodes d’authentification entre l’appareil et le hub IoT. Dans un cas, vous pouvez utiliser une authentification basée sur un jeton SAS. L’autre méthode prise en charge utilise l’authentification par certificat X.509.

La méthode du jeton SAS fournit une authentification pour chaque appel effectué par l’appareil à IoT Hub en associant la clé symétrique à chaque appel. L’authentification basée sur le certificat X.509 permet l’authentification d’un appareil IoT au niveau physique dans le cadre de l’établissement d’une connexion TLS (Transport Layer Security) standard. La méthode basée sur le jeton de sécurité peut être utilisée sans l’authentification X.509 qui constitue un modèle moins sécurisé. Le choix entre les deux méthodes dépend principalement du niveau requis pour l’authentification de l’appareil et de la disponibilité d’un stockage sécurisé sur l’appareil (pour stocker la clé privée en toute sécurité).

Les Kits de développement logiciel (SDK) Azure IoT Hub génèrent automatiquement les jetons sans configuration spéciale. Si vous n’utilisez pas le kit SDK, vous devrez générer les jetons de sécurité.

Vous pouvez configurer et approvisionner de nombreux appareils à la fois à l’aide du [Service IoT Hub Device Provisioning](../iot-dps/index.yml).

### <a name="devices-can-securely-communicate-with-an-iot-hub"></a>Les appareils peuvent communiquer en toute sécurité avec un hub IoT

Après avoir sélectionné vote méthode d'authentification, la connexion Internet entre l’appareil IoT et IoT Hub est sécurisée à l’aide de la norme TLS (Transport Layer Security). Azure IoT prend en charge TLS 1.2, TLS 1.1 et TLS 1.0, dans cet ordre. La prise en charge de TLS 1.0 est fournie uniquement à des fins de compatibilité descendante. Consultez Prise en charge de TLS dans IoT Hub pour savoir comment configurer votre hub de manière à utiliser TLS 1.2, car il offre le plus de sécurité.

## <a name="communication-patterns-with-a-device"></a>Modèles de communication avec un appareil

En règle générale, les appareils IoT envoient la télémétrie des capteurs aux services back-end dans le cloud. Toutefois, d’autres types de communication sont possibles, par exemple un service back-end qui envoie des commandes à vos appareils. Voici quelques exemples de différents types de communication : 

*  Un camion frigorifique envoie la température à un hub IoT toutes les 5 minutes
*  Un service back-end envoie une commande à un appareil pour changer sa fréquence d’envoi de la télémétrie en vue de diagnostiquer un problème
*  Un appareil qui supervise un réacteur discontinu dans une usine chimique envoie une alerte quand la température dépasse une certaine valeur

### <a name="telemetry-is-data-emitted-by-a-device"></a>La télémétrie est une donnée émise par un appareil 

Exemples de données de télémétrie reçues d’un appareil : données de capteur telles que la vitesse ou la température, message d’erreur tel qu’un événement manqué ou encore message d’information indiquant que l’appareil est en bonne santé. Les appareils IoT envoient des événements (notifications, accusés de réception, télémétrie) à l’application pour obtenir des insights. Les applications peuvent nécessiter des sous-ensembles spécifiques d’événements à des fins de traitement ou de stockage à des points de terminaison différents.

### <a name="properties-are-state-values-or-data-that-applications-can-access"></a>Les propriétés sont des valeurs d’état ou des données auxquelles les applications peuvent accéder. 

Par exemple, la version actuelle du microprogramme de l’appareil, ou les propriétés accessibles en écriture pouvant être mises à jour, telles que la température, sont des propriétés. Les propriétés peuvent être lues ou définies à partir du hub IoT et être utilisées pour envoyer des notifications lorsqu’une action est terminée. La température est un exemple de propriété spécifique sur un appareil. Il peut s’agir d’une propriété accessible en écriture pouvant être mise à jour sur l’appareil ou lue à partir d’un capteur de température fixé à l’appareil. 

Vous pouvez activer des propriétés dans IoT Hub à l’aide de [jumeaux d’appareils ](iot-hub-devguide-device-twins.md) ou de la fonctionnalité [Plug and Play](../iot-develop/overview-iot-plug-and-play.md).

    
Pour en savoir plus sur les différences entre les jumeaux d’appareil et la fonctionnalité Plug and Play, consultez[Plug and Play](../iot-develop/concepts-digital-twin.md#device-twins-and-digital-twins).

### <a name="commands-can-be-used-to-execute-methods-directly-on-connected-devices"></a>Les commandes peuvent être utilisées pour exécuter des méthodes directement sur des appareils connectés. 

Un exemple de commande est le redémarrage de l’appareil. IoT Hub implémente des commandes en vous permettant d'invoquer des méthodes directes sur les appareils à partir du cloud. Les [méthodes directes](iot-hub-devguide-direct-methods.md) représentent une interaction de requête-réponse avec un appareil, similaire à un appel HTTP, dans la mesure où elles réussissent ou échouent immédiatement (après un délai d’attente spécifié par l’utilisateur). Cette approche est utile pour les scénarios où le plan d’action immédiate est différent selon que l’appareil a été en mesure ou non de répondre.

## <a name="view-and-act-on-data-collected-from-your-devices"></a>Afficher et agir sur les données collectées à partir de vos appareils

IoT Hub vous donne la possibilité de déverrouiller la valeur des données de vos appareils avec d’autres services Azure, afin que vous puissiez passer à la résolution prédictive des problèmes, plutôt qu’à une gestion réactive. Connectez votre hub IoT à d’autres services Azure pour accéder au Machine Learning, aux analyses et à l’IA afin d’agir sur des données en temps réel, d’optimiser le traitement et d’obtenir des insights plus détaillés.

### <a name="built-in-endpoint-collects-data-from-your-devices-by-default"></a>Le point de terminaison intégré collecte les données de vos appareils par défaut

Un point de terminaison intégré collecte les données de votre appareil par défaut. Les données sont collectées à l’aide d’un modèle requête-réponse sur des points de terminaison d’appareil IoT dédiés, elles sont disponibles pendant un maximum de sept jours et peuvent être utilisées pour exécuter des actions sur un appareil. Voici les données acceptées par le point de terminaison de l’appareil :

*    Envoyer des messages appareil-à-cloud. Un appareil utilise ce point de terminaison pour envoyer des messages appareil-à-cloud.

*    Recevoir des messages cloud-à-appareil. Un appareil utilise ce point de terminaison pour recevoir les messages cloud-à-appareil qui lui sont adressés.

*    Initier des téléchargements de fichiers. Un appareil utilise ce point de terminaison pour recevoir un URI SAP Azure Storage à partir d’IoT Hub pour télécharger un fichier.

*    Récupérer et mettre à jour les propriétés d’une représentation d’appareil. Un appareil utilise ce point de terminaison pour accéder aux propriétés de son jumeau d’appareil. 

*    Recevoir des requêtes de méthodes directes. Un appareil utilise ce point de terminaison pour écouter les requêtes des méthodes directes. 

Pour plus d’informations sur les points de terminaison IoT Hub, consultez [Points de terminaison IoT Hub - Guide pour développeurs](
iot-hub-devguide-endpoints.md#list-of-built-in-iot-hub-endpoints)

### <a name="use-message-routing-to-send-data-to-other-endpoints-for-processing"></a>Utiliser le routage des messages pour envoyer des données à d’autres points de terminaison en vue de leur traitement

Ces données peuvent également être acheminées vers différents services pour poursuivre leur traitement. À mesure que la solution IoT est mise à l’échelle, le nombre d’appareils, le volume d’événements, la diversité des événements et les différents services varient également. Une méthode flexible, évolutive, cohérente et fiable pour le routage des événements est nécessaire au traitement de ce modèle. Les données peuvent également être filtrées pour les envoyer à différents services. Une fois qu’un itinéraire de message a été créé, les données cessent de circuler vers le point de terminaison intégré, sauf si un itinéraire de secours a été configuré. Pour obtenir un didacticiel présentant plusieurs utilisations du routage des messages, consultez le [Didacticiel de routage](tutorial-routing.md).

IoT Hub s’intègre également à Event Grid qui vous permet de répartir les données sur plusieurs abonnés. Event Grid est un service d’événement entièrement géré qui vous permet de facilement gérer des événements dans de nombreux services et applications Azure différents. Conçu pour les performances et la mise à l’échelle, il simplifie la création d’applications pilotées par les événements et d’architectures serverless. En savoir plus sur Event Grid. Les différences entre le routage des messages et l’utilisation d’Event Grid sont expliquées dans [Comparaison entre le routage des messages et Event Grid](iot-hub-event-grid-routing-comparison.md)