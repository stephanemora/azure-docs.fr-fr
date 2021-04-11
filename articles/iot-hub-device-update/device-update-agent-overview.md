---
title: Comprendre la mise à jour des appareils pour Azure IoT Hub Agent | Microsoft Docs
description: Prenez connaissance de la mise à jour des appareils pour Azure IoT Hub Agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561234"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Vue d’ensemble de la mise à jour des appareils pour IoT Hub Agent

L’Agent de mise à jour des appareils se compose de deux couches conceptuelles :

* La couche d’interface s’appuie sur [Azure IoT Plug-and-Play (PNP),](../iot-pnp/overview-iot-plug-and-play.md), ce qui permet aux messages de circuler entre l’Agent de mise à jour des appareils et les Services de mise à jour des appareils.
* La couche de plateforme est en charge des actions générales de mise à jour du téléchargement, de l’installation et de l’application qui peuvent être spécifiques à la plateforme ou aux appareils.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Implémentations d’agent." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Couche d’interface

La couche d’interface est constituée de l’[interface ADU Core](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) et de l’[interface d’informations sur l’appareil](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Ces interfaces reposent sur un fichier de configuration pour les valeurs par défaut. Les valeurs par défaut sont aduc_manufacturer et aduc_model pour l’interface AzureDeviceUpdateCore, et un modèle et un fabricant pour l’interface DeviceInformation. [Découvrez-en plus](device-update-configuration-file.md) sur le fichier de configuration.

### <a name="adu-core-interface"></a>Interface ADU Core

L’interface « ADU Core » est le canal de communication principal entre l’Agent de mise à jour des appareils et les Services de mise à jour des appareils. [Découvrez-en plus](device-update-plug-and-play.md#adu-core-interface) sur cette interface.

### <a name="device-information-interface"></a>Interface d’informations sur l’appareil

L’interface d’informations sur l’appareil est utilisée pour implémenter l’interface `Azure IoT PnP DeviceInformation`. [Découvrez-en plus](device-update-plug-and-play.md#device-information-interface) sur cette interface.

## <a name="the-platform-layer"></a>Couche de plateforme

Il existe deux implémentations de la couche de plateforme. La couche de plateforme de simulateur a une implémentation simple des actions de mise à jour. Elle est utilisée pour tester et évaluer rapidement la Mise à jour des appareils pour les services et la configuration d’IoT Hub. Quand l’Agent de mise à jour des appareils est généré avec la couche de plateforme de simulateur, nous y faisons référence en tant qu’« Agent de simulateur de mise à jour des appareils » ou juste « simulateur ». [Découvrez-en plus](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) sur l’utilisation de l’agent de simulateur. La couche de plateforme Linux s’intègre à l’[optimisation de la distribution](https://github.com/microsoft/do-client) pour les téléchargements et est utilisée dans notre image de référence Raspberry Pi et dans tous les clients qui s’exécutent sur des systèmes Linux.

### <a name="simulator-platform-layer"></a>Couche de plateforme de simulateur

L’implémentation de la couche de plateforme de simulateur se trouve dans `src/platform_layers/simulator_platform_layer` et peut être utilisée pour tester et évaluer la Mise à jour des appareils pour IoT Hub.  La plupart des actions effectuées dans l’implémentation du « simulateur » sont simulées pour réduire les changements physiques à tester avec la Mise à jour des appareils pour IoT Hub.  Une mise à jour « simulée » de bout en bout peut être effectuée à l’aide de cette couche de plateforme. [Découvrez-en plus](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) sur l’exécution de l’agent de simulateur.

### <a name="linux-platform-layer"></a>Couche de plateforme Linux

L’implémentation de la couche de plateforme Linux se trouve dans `src/platform_layers/linux_platform_layer`. Elle s’intègre au [client d’optimisation de la distribution](https://github.com/microsoft/do-client/releases) pour les téléchargements et est utilisée dans notre image de référence Raspberry Pi et dans tous les clients qui s’exécutent sur des systèmes Linux.

Cette couche peut s’intégrer à différents gestionnaires de mise à jour pour implémenter le programme d’installation. Par exemple, le Gestionnaire de mise à jour `SWUpdate` appelle un script d’interpréteur de commandes pour appeler l’exécutable `SWUpdate` afin d’effectuer une mise à jour.

## <a name="update-handlers"></a>Gestionnaires de mise à jour

Les gestionnaires de mise à jour sont des composants qui gèrent des parties de la mise à jour spécifiques au contenu ou au programme d’installation. Les implémentations de gestionnaire de mise à jour se trouvent dans `src/content_handlers`.

### <a name="simulator-update-handler"></a>Gestionnaire de mise à jour du simulateur

Le Gestionnaire de mise à jour du simulateur est utilisé par la couche de plateforme de simulateur. Il peut l’être également avec la couche de plateforme Linux pour simuler des interactions avec un Gestionnaire de contenu. Le Gestionnaire de mise à jour du simulateur implémente les API de gestionnaire de mise à jour avec essentiellement des non-opérations. L’implémentation du Gestionnaire de mise à jour du simulateur se trouve ci-dessous :
* [Simulateur de mise à jour d’image](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Simulateur APT de mise à jour de package](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Remarque : Le champ InstalledCriteria de l’interface PnP AzureDeviceUpdateCore doit être le hachage SHA256 du contenu. Il s’agit du même hachage que celui qui est présent dans l’[objet de manifeste d’importation](import-update.md#create-device-update-import-manifest). [Découvrez-en plus](device-update-plug-and-play.md) sur `installedCriteria` et l’interface `AzureDeviceUpdateCore`.

### <a name="swupdate-update-handler"></a>Gestionnaire de mise à jour `SWUpdate`

Le Gestionnaire de mise à jour `SWUpdate` s’intègre à l’exécutable en ligne de commande `SWUpdate` et à d’autres commandes de l’interpréteur de commandes pour implémenter des mises à jour A/B spécifiquement pour l’image de référence Raspberry Pi. Recherchez l’image de référence Raspberry Pi la plus récente [ici](https://github.com/Azure/iot-hub-device-update/releases). Le Gestionnaire de mise à jour `SWUpdate` est implémenté dans [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>Gestionnaire de mise à jour APT

Le Gestionnaire de mise à jour APT traite un manifeste de mise à jour spécifique à APT et appelle APT pour installer ou mettre à jour le ou les packages Debian spécifiés.

## <a name="self-update-device-update-agent"></a>Mettre automatiquement à jour l’Agent de mise à jour des appareils

L’Agent de mise à jour des appareils et ses dépendances peuvent être mis à jour par le biais de la Mise à jour des appareils pour IoT Hub Pipeline. Si vous utilisez une mise à jour basée sur une image, incluez l’Agent de mise à jour des appareils le plus récent dans votre nouvelle image. Si vous utilisez une mise à jour basée sur un package, incluez l’Agent de mise à jour des appareils et sa version souhaitée dans le manifeste APT comme n’importe quel autre package. [Découvrez-en plus](device-update-apt-manifest.md) sur le manifeste APT. Vous pouvez vérifier la version installée de l’Agent de mise à jour des appareils et de l’Agent d’optimisation de la distribution dans la section Propriétés de l’appareil de votre [jumeau d’appareil IOT](../iot-hub/iot-hub-devguide-device-twins.md). [Découvrez-en plus sur les propriétés de l’appareil dans l’interface ADU Core](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Étapes suivantes
[Comprendre le fichier de configuration de l’Agent de mise à jour des appareils](device-update-configuration-file.md)