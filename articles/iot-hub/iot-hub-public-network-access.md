---
title: Gestion de l’accès réseau public pour le hub IoT Azure
description: Documentation sur la désactivation et l’activation de l’accès au réseau public pour le hub IoT
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: fbbdaeb796dfa23906c8010a54af14eff6df0b97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026638"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Gestion de l’accès réseau public pour votre hub IoT

Pour limiter l’accès au seul [point de terminaison privé pour votre hub IoT dans votre réseau virtuel](virtual-network-support.md), désactivez l’accès au réseau public. Pour ce faire, utilisez le portail Azure ou l’API `publicNetworkAccess`. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Désactiver l’accès au réseau public à l’aide du portail Azure

1. Visitez le [portail Azure](https://portal.azure.com)
2. Accédez à votre hub IoT.
3. Sélectionnez **Mise en réseau** dans le menu Place de marché.
4. Sous « Autoriser l’accès du réseau public à », sélectionnez **Désactivé**
5. Sélectionnez **Enregistrer**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Image qui montre le portail Azure où désactiver l’accès au réseau public" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Pour activer l’accès au réseau public, sélectionnez **Tous les réseaux**, puis **Enregistrer**.

## <a name="accessing-the-iot-hub-after-disabling-public-network-access"></a>Accès à IoT Hub après la désactivation de l’accès au réseau public

Une fois que l’accès au réseau public est désactivé, IoT Hub est uniquement accessible via [son point de terminaison privé de réseau virtuel à l’aide d’une liaison privée Azure](virtual-network-support.md). Cette restriction comprend l’accès via le portail Azure, car les appels d’API au service IoT Hub sont effectués directement à l’aide de votre navigateur avec vos informations d’identification.

## <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>Point de terminaison IoT Hub, adresse IP et ports après la désactivation de l’accès au réseau public

IoT Hub étant une plateforme PaaS (Platform as a service) multilocataire, différents clients partagent le même pool de ressources matérielles de calcul, de réseau et de stockage. Les noms d’hôtes d’IoT Hub sont mappés à un point de terminaison public avec une adresse IP de routage public sur Internet. Différents clients partagent ce point de terminaison public IoT Hub, et tous les appareils IoT qui se trouvent sur des réseaux étendus et sur des réseaux locaux peuvent y accéder. 

La désactivation de l’accès au réseau public est appliquée à une ressource IoT Hub spécifique, garantissant ainsi l’isolation. Afin que le service demeure actif pour d’autres ressources client à l’aide du chemin public, son point de terminaison public peut toujours être résolu, les adresses IP sont détectables et les ports restent ouverts. Cela n’est pas une source de préoccupation, car Microsoft intègre plusieurs couches de sécurité pour garantir un isolement complet entre les locataires. Pour plus d’informations, consultez [Isolation dans le cloud public Azure](../security/fundamentals/isolation-choices.md#tenant-level-isolation).

## <a name="ip-filter"></a>Filtre IP 

Si l’accès au réseau public est désactivé, toutes les règles de [filtre IP](iot-hub-ip-filtering.md) sont ignorées. Cela est dû au fait que toutes les adresses IP de l’Internet public sont bloquées. Pour utiliser le filtre IP, utilisez l’option **Plage d’adresses IP sélectionnées**.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Résolution de bogue via un point de terminaison compatible avec Event Hub intégré

Il existe un bogue dans IoT Hub où le [point de terminaison intégré compatible avec Event Hub](iot-hub-devguide-messages-read-builtin.md) continue d’être accessible via l’Internet public alors que l’accès du réseau public à IoT Hub est désactivé. Pour en savoir plus et nous contacter concernant ce bogue, consultez [Désactiver l’accès du réseau public pour IoT Hub désactive l’accès au point de terminaison Event Hub intégré](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
