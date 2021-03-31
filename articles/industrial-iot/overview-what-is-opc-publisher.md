---
title: Microsoft OPC Publisher
description: Cet article donne une vue d’ensemble du module OPC Publisher Edge.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800531"
---
# <a name="what-is-the-opc-publisher"></a>Qu’est-ce qu’OPC Publisher ?

OPC Publisher est un produit Microsoft entièrement pris en charge, développé en extérieur, qui comble le fossé entre les ressources industrielles et le cloud Microsoft Azure. Pour ce faire, il se connecte à des ressources compatibles avec OPC UA ou à un logiciel de connectivité industrielle et publie des données de télémétrie dans Azure IoT Hub dans différents formats, notamment le format standard IEC62541 OPC UA PubSub (à partir de la version 2.6).

Il s’exécute sur Azure IoT Edge en tant que module ou sur Docker en tant que conteneur. Étant donné qu’il tire parti du runtime multiplateforme .NET, il s’exécute également en mode natif sur Linux et Windows 10.

OPC Publisher est une implémentation de référence qui montre comment :

- Se connecter à des serveurs OPC UA existants.
- Publier les données de télémétrie encodées JSON des serveurs OPC UA au format Pub/Sub OPC UA sur Azure IoT Hub, à l’aide d’une charge utile JSON.

Vous pouvez utiliser un des protocoles de transport pris en charge par le SDK du client Azure IoT Hub : HTTPS, AMQP et MQTT.

L’implémentation de référence comprend :

- Un *client* OPC UA pour se connecter aux serveurs OPC UA existants sur votre réseau.
- Un *serveur* OPC UA sur le port 62222, que vous pouvez utiliser pour gérer ce qui est publié et qui offre à IoT Hub des méthodes directes pour en faire de même.

Vous pouvez télécharger [l’implémentation de référence d’OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) à partir de GitHub.

L’application est implémentée avec la technologie .NET Core et peut s’exécuter sur toute plateforme prise en charge par .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Que fait OPC Publisher ?

OPC Publisher implémente la logique de nouvelle tentative pour établir des connexions aux points de terminaison qui ne répondent pas à un certain nombre de demandes de connexion persistante. Cela est utile si, par exemple, si un serveur OPC UA cesse de répondre en raison d’une panne d’alimentation.

Pour chaque intervalle de publication sur un serveur OPC UA, l’application crée un abonnement distinct sur lequel tous les nœuds avec cet intervalle de publication sont mis à jour.

Pour réduire la charge réseau, OPC Publisher prend en charge le traitement par lot des données envoyées à IoT Hub. Ce traitement par lot envoie un paquet à IoT Hub uniquement si la taille de paquet configurée est atteinte.

Cette application utilise la pile de référence OPC UA de l’OPC Foundation dans les packages NuGet. Consultez [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) pour les conditions des licences.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris ce qu’est OPC Publisher, vous pouvez commencer par le déployer :

> [!div class="nextstepaction"]
> [Déployer OPC Publisher en mode autonome](tutorial-publisher-deploy-opc-publisher-standalone.md)
