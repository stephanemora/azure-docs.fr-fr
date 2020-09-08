---
title: Passerelles pour les appareils en aval - Azure IoT Edge | Microsoft Docs
description: Utilisez Azure IoT Edge pour créer un appareil de passerelle proxy, opaque ou transparent qui envoie des données à partir de plusieurs appareils en aval vers le cloud ou qui traite ces données localement.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017020"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Guide pratique pour utiliser un appareil IoT Edge en tant que passerelle

Les passerelles des solutions IoT Edge fournissent des fonctionnalités de connectivité et d’analytique Edge aux appareils IoT qui, sans cela, n’en disposeraient pas. Vous pouvez utiliser Azure IoT Edge pour répondre à tous les besoins (qu’ils soient liés à la connectivité, à l’identité ou à l’analytique Edge) d’une passerelle IoT. Les modèles de passerelles dans cet article font uniquement référence aux caractéristiques de connectivité d’appareil en aval et d’identité d’appareil, et non à la façon dont les données de l’appareil sont traitées sur la passerelle.

## <a name="patterns"></a>Modèles

Il existe trois modèles pour l’utilisation d’un appareil IoT Edge en tant que passerelle : transparent, traduction de protocole et traduction d’identité.

Une différence essentielle entre les modèles est qu’une passerelle transparente transmet des messages entre des appareils en aval et IoT Hub sans nécessiter de traitement supplémentaire. Toutefois, la traduction de protocole et la traduction d’identité nécessitent un traitement sur la passerelle pour permettre la communication.

Toute passerelle peut utiliser des modules IoT Edge pour effectuer l’analyse ou le prétraitement à la périphérie avant de transmettre des messages d’appareils en aval à IoT Hub.

![Diagramme - Modèles de passerelles d’identité, de protocole et transparentes](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>Modèle transparent

Dans un modèle de passerelle *transparente*, les appareils qui, en théorie, peuvent se connecter à IoT Hub, peuvent se connecter à un appareil de passerelle à la place. Les appareils en aval ont leurs propres identités IoT Hub et utilisent le protocole MQTT, AMQP ou HTTP. La passerelle se contente de transférer les communications entre les appareils et IoT Hub. Les appareils et les utilisateurs qui interagissent avec eux par le biais d’IoT Hub ne savent pas qu’une passerelle effectue la médiation de leurs communications. Cette ignorance signifie que la passerelle est considérée comme *transparente*.

Le runtime IoT Edge inclut des fonctionnalités de passerelle transparentes. Pour plus d’informations, consultez [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md).

### <a name="protocol-translation-pattern"></a>Modèle de traduction de protocole

Une passerelle de *traduction de protocole* est également qualifiée d’*opaque*, contrairement au modèle de passerelle transparente. Dans ce modèle, les appareils qui ne prennent pas en charge MQTT, AMQP ou HTTP peuvent utiliser un appareil de passerelle pour envoyer des données à IoT Hub en leur nom. La passerelle comprend le protocole utilisé par les appareils en aval. En outre, il s’agit du seul appareil doté d’une identité dans IoT Hub. Toutes les informations semblent provenir d’un seul appareil, la passerelle. Les appareils situés en aval doivent intégrer des informations d'identification supplémentaires dans leurs messages si les applications cloud veulent analyser les données pour chaque appareil. De plus, les primitives d’IoT Hub comme les jumeaux et les méthodes sont disponibles seulement pour l’appareil de passerelle, et non pas pour les appareils en aval.

Le runtime IoT Edge n’inclut pas de fonctionnalités de traduction de protocole. Ce modèle nécessite des modules personnalisés ou tiers qui sont souvent spécifiques du matériel et du protocole utilisés. La [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) contient plusieurs modules de traduction de protocole.

### <a name="identity-translation-pattern"></a>Modèle de traduction d’identité

Dans un modèle de passerelle de *traduction d’identité*, les appareils qui n’ont pas accès à IoT Hub peuvent se connecter à un appareil de passerelle. La passerelle fournit une traduction d’identité IoT Hub et de protocole pour le compte des appareils en aval. La passerelle est suffisamment intelligente pour comprendre le protocole utilisé par les appareils en aval, leur fournir une identité et traduire les primitives IoT Hub. Les appareils en aval s’affichent dans IoT Hub comme des appareils de premier niveau avec des représentations et des méthodes. Un utilisateur peut interagir avec les appareils dans IoT Hub et n’a pas connaissance de l’appareil de passerelle intermédiaire.

Le runtime IoT Edge n’inclut pas de fonctionnalités de traduction d’identité. Ce modèle nécessite des modules personnalisés ou tiers qui sont souvent spécifiques du matériel et du protocole utilisés. Pour un exemple qui utilise le modèle de traduction d’identité, consultez [Kit de démarrage LoRaWAN d’Azure IoT Edge](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="use-cases"></a>Cas d'utilisation

Tous les modèles de passerelles fournissent les avantages suivants :

* **Analytique en périphérie** : utilisez les services IA localement pour traiter les données provenant des appareils en aval, sans envoyer de données de télémétrie haute fidélité au cloud. Recherchez des insights localement, réagissez-y, et envoyez uniquement un sous-ensemble de données à IoT Hub.
* **Isolation d’appareil en aval** : l’appareil de passerelle peut protéger tous les appareils en aval contre une exposition sur Internet. Il peut être placé entre un réseau OT qui n’a pas de connectivité et un réseau IT qui fournit l’accès au web.
* **Multiplexage des connexions** : tous les appareils qui se connectent à IoT Hub via une passerelle IoT Edge utilisent la même connexion sous-jacente.
* **Lissage du trafic** : l’appareil IoT Edge implémente automatiquement une interruption exponentielle si IoT Hub limite le trafic, tout en assurant une conservation locale des messages. Cet avantage rend votre solution résiliente face aux pics de trafic.
* **Prise en charge hors connexion** : l’appareil de passerelle stocke les messages et les mises à jour du jumeau qui ne peuvent pas être remis à IoT Hub.

Une passerelle qui effectue la traduction de protocole peut prendre en charge des appareils existants et de nouveaux appareils avec contraintes de ressources. De nombreux appareils existants produisent des données qui peuvent alimenter les insights métier : toutefois, ils n’ont pas été conçus pour la connectivité au cloud. Les passerelles opaques permettent le déverrouillage et l’utilisation de ces données dans une solution IoT.

Une passerelle qui effectue la traduction d’identité fournit les avantages de la traduction de protocole et permet, en plus, la gestion complète des appareils en aval à partir du cloud. Tous les appareils associés à votre solution IoT s’affichent dans IoT Hub, quel que soit le protocole qu’ils utilisent.

## <a name="cheat-sheet"></a>Aide-mémoire

Voici un aide-mémoire qui compare les primitives IoT Hub lors de l’utilisation de passerelles transparentes, opaques (protocole) et de proxy.

| Primitives | Passerelle transparente | Traduction de protocole | Traduction d’identité |
|--------|-------------|--------|--------|
| Identités stockées dans le registre des identités IoT Hub | Identités de tous les appareils connectés | Uniquement l’identité de l’appareil de passerelle | Identités de tous les appareils connectés |
| Jumeau d’appareil | Chaque appareil connecté possède son propre jumeau d’appareil | Seule la passerelle a un appareil et des jumeaux de module | Chaque appareil connecté possède son propre jumeau d’appareil |
| Méthodes directes et messages du cloud vers l’appareil | Le cloud peut porter individuellement sur chaque appareil connecté | Le cloud ne peut porter que sur l’appareil de passerelle | Le cloud peut porter individuellement sur chaque appareil connecté |
| [Limitations et quotas d’IoT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Appliquer à chaque appareil | Appliquer à l’appareil de passerelle | Appliquer à chaque appareil |

Lorsque vous utilisez un modèle de passerelle opaque (traduction de protocole), tous les appareils qui se connectent via cette passerelle partagent la même file d’attente cloud sur l’appareil, qui peut contenir au maximum 50 messages. De ce fait, le modèle de passerelle opaque doit être uniquement utilisé quand peu d’appareils se connectent via la passerelle de chaque champ et que le trafic entre le cloud et l’appareil est faible.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les trois étapes de configuration d’une passerelle transparente :

* [Configurer un appareil IoT Edge en tant que passerelle transparente](how-to-create-transparent-gateway.md)
* [Authentifier un appareil en aval auprès d’Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Connecter un appareil en aval à une passerelle Azure IoT Edge](how-to-connect-downstream-device.md)
