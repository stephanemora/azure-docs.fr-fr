---
title: Comprendre la prise en charge de la mise à jour des appareils déconnectés à l’aide du cache connecté Microsoft | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Comprendre la prise en charge de la mise à jour des appareils déconnectés à l’aide du cache connecté Microsoft
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678367"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Comprendre la prise en charge des mises à jour des appareils déconnectés

Dans un scénario de passerelle transparente, un ou plusieurs appareils peuvent transmettre leurs messages via un appareil de passerelle qui gère la connexion à Azure IoT Hub. Dans ce cas, les appareils enfants peuvent ne pas disposer d’une connexion Internet ou ne pas être autorisés à télécharger du contenu à partir d’Internet. Le module IoT Edge du cache connecté Microsoft (préversion) met à la disposition des clients utilisant le service Device Update pour Azure IoT Hub un cache intelligent intégré au réseau, ce qui leur permet d’effectuer des mises à jour basées sur les images et les packages des appareils Linux en arrière-plan et de la passerelle IoT Edge (appareils IoT Hub en aval) et également d’économiser de la bande passante.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Comment fonctionne le cache connecté Microsoft (préversion) pour le service Device Update pour Azure IoT Hub ?

Le cache connecté Microsoft est un cache transparent intelligent pour le contenu publié pour le contenu du service Device Update pour Azure IoT Hub et peut être personnalisé afin de mettre en cache du contenu provenant d’autres sources telles que les dépôts de packages. Le cache connecté Microsoft est un cache à froid qui est chauffé par les demandes des clients pour les plages de fichiers exactes demandées par le client d’optimisation de la distribution et qui ne prédéfinit pas le contenu. Le diagramme et la description pas à pas ci-dessous expliquent comment fonctionne le cache connecté Microsoft dans l’infrastructure du service Device Update pour Azure IoT Hub.

>[!Note]
>La définition de ce flux part du principe que la passerelle IoT Edge a une connectivité Internet. Pour le scénario de passerelle IoT Edge en aval (périphérie imbriquée), le « réseau de distribution de contenu » (CDN) peut être considéré comme le cache connecté Microsoft hébergé sur la passerelle IoT Edge parente.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Mise à jour des appareils déconnectés" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Le cache connecté Microsoft est déployé en tant que module IoT Edge sur le serveur local.
2. Les clients utilisant le service Device Update pour Azure IoT Hub sont configurés pour télécharger le contenu à partir du cache connecté Microsoft en vertu de l’attribut GatewayHostName de la chaîne de connexion de l’appareil pour les appareils de nœud terminal IoT **ou** du paramètre parent_hostname défini dans le fichier config.toml pour les appareils enfants IOT Edge.
3. Dans les deux cas, les clients utilisant le service Device Update pour Azure IoT Hub reçoivent les commandes de téléchargement du contenu des mises à jour du service Device Update pour Azure IoT Hub et demandent le contenu des mises à jour au cache Microsoft connecté plutôt qu’au CDN. Le cache connecté Microsoft par défaut est configuré pour écouter sur le port http 80 et le client d’optimisation de la distribution effectue la demande de contenu sur le port 80 ; le parent doit donc être configuré pour écouter sur ce port.  Seul le protocole http est pris en charge pour l’instant.
4. Le serveur du cache connecté Microsoft télécharge le contenu du CDN, amorce son cache local stocké sur le disque et remet le contenu au client utilisant le service Device Update pour Azure IoT Hub.
   
>[!Note]
>Quand vous utilisez des mises à jour basées sur des packages, le serveur du cache connecté Microsoft est configuré par l’administrateur avec le nom d’hôte requis pour les packages.

5. Les demandes suivantes émanant d’autres clients utilisant le service Device Update pour Azure IoT Hub pour le même contenu de mise à jour proviennent désormais du cache et le cache connecté Microsoft n’effectue pas de demandes auprès du CDN pour le même contenu.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Prise en charge de l’IoT industriel (IIoT) avec des scénarios d’hébergement parent/enfant

Quand une passerelle IoT Edge enfant ou en aval héberge le serveur du cache connecté Microsoft, elle est configurée pour demander du contenu de mise à jour à la passerelle IoT Edge parente qui héberge le serveur du cache connecté Microsoft. Cela est indispensable pour autant de niveaux que nécessaire jusqu’à la passerelle IoT Edge parente hébergeant un serveur de cache connecté Microsoft qui a accès à Internet. À partir du serveur connecté à Internet, le contenu est demandé auprès du CDN, à partir duquel il est remis à la passerelle IoT Edge enfant à l’origine de la demande du contenu. Le contenu est stocké sur le disque à tous les niveaux.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Accéder au cache connecté Microsoft (préversion) pour le service Device Update pour Azure IoT Hub

Le module IoT Edge de cache Microsoft connecté est disponible en préversion pour les clients qui déploient des solutions à l’aide du service Device Update pour Azure IoT Hub. L’accès à la préversion se fait sur invitation. [Demandez l’accès](https://aka.ms/MCCForDeviceUpdateForIoT) au cache connecté Microsoft (préversion) pour le service Device Update pour Azure IoT Hub et fournissez les informations demandées si vous souhaitez accéder au module.
