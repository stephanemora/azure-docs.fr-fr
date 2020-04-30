---
title: Présentation de la préversion d’IoT Plug-and-Play | Microsoft Docs
description: En savoir plus sur la préversion d’IoT Plug-and-Play. IoT Plug-and-Play est basé sur un langage de modélisation ouvert qui permet aux appareils IoT de déclarer leurs fonctionnalités. Les appareils IoT présentent cette déclaration, appelée modèle de fonctionnalité d’appareil, lorsqu’ils se connectent à des solutions cloud telles qu’Azure IoT Central ou des applications partenaires. La solution cloud peut ensuite comprendre automatiquement l’appareil et commencer à interagir avec lui, tout cela sans écrire de code.
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 12f3febac2c5c8ed01b9b156a64dc77f6ed0704f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770432"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Qu’est ce que la préversion d’IoT Plug-and-Play ?

La préversion d’IoT Plug-and-Play permet aux développeurs de solutions d’intégrer des appareils avec leurs solutions sans écrire de code incorporé. Au cœur d’IoT Plug-and-Play est un schéma de _modèle de fonctionnalité d’appareil_ qui décrit les fonctionnalités de l’appareil. Ce schéma est un document JSON structuré sous la forme d’un ensemble d’interfaces comprenant les définitions des éléments suivants :

- _Propriétés_ qui représentent l’état en lecture seule et en lecture/écriture d’un appareil ou d’une autre entité. Par exemple, un numéro de série d’appareil peut être une propriété en lecture seule et une température cible sur un thermostat peut être une propriété en lecture/écriture.
- _Télémétrie_ qui correspond aux données émises par un appareil, qu’il s’agisse d’un flux régulier de relevés de capteurs, d’une erreur occasionnelle ou d’un message d’information.
- _Commandes_ qui décrivent une fonction ou une opération qui peut être effectuée sur un appareil. Par exemple, une commande peut redémarrer une passerelle ou prendre une photo à l’aide d’une caméra distante.

Vous pouvez réutiliser les interfaces entre les modèles de fonctionnalité d’appareil pour faciliter la collaboration et accélérer le développement.

Pour que IoT Plug-and-Play fonctionne de manière fluide avec [Azure Digital Twins](../digital-twins/about-digital-twins.md), le schéma IoT Plug-and-Play est défini à l’aide de [DTDL (Digital Twin Definition Language)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Plug-and-Play et DTDL sont ouverts à la communauté, et Microsoft accepte la collaboration avec les clients, les partenaires et le secteur. Les deux sont basés sur des standards W3C ouverts, tels que JSON-LD et RDF, qui permettent une adoption plus facile des services et outils. En outre, il n’existe aucun coût supplémentaire pour l’utilisation d’IoT Plug-and-Play et de DTDL. Les tarifs standard pour [Azure IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/core/overview-iot-central.md) et d’autres services Azure restent les mêmes.

Les solutions basées sur IoT Hub ou IoT Central peuvent tirer parti d’IoT Plug-and-Play.

Cet article décrit :

- Les rôles typiques associés à un projet qui utilise IoT Plug-and-Play.
- Comment utiliser les appareils IoT Plug-and-Play dans votre application.
- Comment développer une application d’appareil IoT qui prend en charge IoT Plug-and-Play.
- Comment certifier un appareil IoT Plug-and-Play et le publier sur le [catalogue d’appareils Certified pour IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Rôles d’utilisateur

IoT Plug-and-Play est utile pour deux types de développeurs :

- Un _développeur de solutions_ est responsable du développement d’une solution IoT à l’aide d’Azure IoT et d’autres ressources Azure, et de l’identification des appareils IoT à intégrer.
- Un _développeur d’appareil_ crée le code qui s’exécute sur un appareil connecté à votre solution.

## <a name="use-iot-plug-and-play-devices"></a>Utiliser des appareils IoT Plug-and-Play

En tant que développeur de solutions, vous pouvez développer une solution IoT hébergée dans le cloud qui utilise des appareils IoT Plug-and-Play. Vous pouvez utiliser un des services Azure suivants :

- [IoT Central](../iot-central/core/overview-iot-central.md) : solution IoT Software-as-a-Service (SaaS) complètement managée qui permet de créer facilement des produits qui connectent les mondes physiques et numériques.
- [IoT Hub](../iot-hub/about-iot-hub.md) : service managé, hébergé dans le cloud, qui agit en tant que hub de messages pour la communication bidirectionnelle et sécurisée entre votre application IoT et vos appareils.

Vous pouvez trouver des appareils IoT Plug-and-Play dans le catalogue d’appareils Azure Certified pour IoT. Chaque appareil IoT Plug-and-Play du catalogue a été validé et possède un modèle de fonctionnalité d’appareil. Affichez le modèle de fonctionnalité d’appareil pour comprendre la fonctionnalité de l’appareil ou utilisez-le pour simuler l’appareil dans Azure IoT Central.

Quand vous connectez un appareil IoT Plug-and-Play, vous pouvez afficher son modèle de fonctionnalité d’appareil, les interfaces incluses dans le modèle et les données de télémétrie, les propriétés et les commandes définies dans ces interfaces.

## <a name="develop-an-iot-device-application"></a>Développer une application d’appareil IoT

En tant que développeur d’appareils, vous pouvez développer un produit matériel IoT qui prend en charge IoT Plug-and-Play. Le processus se compose de deux étapes clés :

1. Définir un modèle de fonctionnalité d’appareil et les interfaces associées. Vous créez un ensemble de fichiers JSON qui déclarent les capacités de votre appareil à l’aide de [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Un modèle de fonctionnalité d’appareil décrit une entité complète, telle qu’un produit physique, et définit l’ensemble des interfaces implémentées par cette entité. Les interfaces sont des contrats partagés qui identifient de façon unique les données de télémétrie, les propriétés et les commandes prises en charge par un appareil. Les interfaces peuvent être réutilisées dans différents modèles de fonctionnalité d’appareil.

1. Créez le logiciel ou le microprogramme de l’appareil qui implémente les fonctionnalités déclarées dans le modèle de fonctionnalité d’appareil et les interfaces. Le SDK Azure IoT inclut des API permettant d’implémenter des modèles de fonctionnalité d’appareil.

Le pack d’extension [Azure IoT Tools pour VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) met à votre disposition une multitude de fonctionnalités, vous apportant une aide précieuse. Par exemple, en tant que développeur d’appareils, vous pouvez utiliser une extension pour générer un projet squelette C à partir d’un modèle de fonctionnalité. Toutefois, vous pouvez utiliser n’importe quel IDE pour créer et implémenter des modèles de fonctionnalités d’appareil.

## <a name="certify-an-iot-plug-and-play-device"></a>Certifier un appareil IoT Plug-and-Play

En tant que développeur d’appareils, vous pouvez envoyer des produits matériels IoT pour certification. Vous pouvez publier un appareil certifié dans le catalogue d’appareils Certified pour IoT. Les étapes du processus de certification sont les suivantes :

- Rejoignez le [Microsoft Partner Network](https://partner.microsoft.com).
- Intégrez le portail Azure Certified pour IoT.
- Soumettez un modèle de fonctionnalité d’appareil IoT Plug-and-Play et des informations de marketing pour créer un nouvel enregistrement d’appareil.
- Transmettez un ensemble automatisé de tests de validation pour l’appareil.
- Publiez dans le catalogue d’appareils Certified pour IoT.

## <a name="regional-availability"></a>Disponibilité régionale

En préversion publique, IoT Plug-and-Play est disponible dans toutes les régions.

## <a name="message-quotas-in-iot-hub"></a>Quotas de message dans IoT Hub
Pendant la préversion publique, les appareils IoT Plug-and-Play envoient des messages séparés par interface, ce qui peut augmenter le nombre de messages comptabilisés dans le [quota de messages](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous disposez d’une vue d’ensemble d’IoT Plug-and-Play, l’étape suivante suggérée consiste à essayer l’un des démarrages rapides suivants :

- [Utiliser un modèle de fonctionnalité d’appareil pour créer un appareil IoT Plug-and-Play](./quickstart-create-pnp-device-windows.md)
- [Connecter un appareil à IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Se connecter à un appareil dans une solution](./quickstart-connect-pnp-device-solution-node.md)
