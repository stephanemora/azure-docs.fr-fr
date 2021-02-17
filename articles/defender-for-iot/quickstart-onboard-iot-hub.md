---
title: Intégrer à une solution basée sur un agent Defender pour IoT
description: Découvrez comment intégrer et activer le service de sécurité Defender pour IoT dans votre hub Azure IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809129"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>Intégrer à une solution basée sur un agent Defender pour IoT

Cet article explique comment activer le service Defender pour IoT sur votre hub IoT existant. Si vous n’avez pas de hub IoT, pour commencer, voir [Créer un hub IoT à l’aide du portail Azure](../iot-hub/iot-hub-create-through-portal.md).

Vous pouvez gérer votre sécurité IoT par le biais du hub IoT dans Defender pour IoT. Le portail de gestion situé dans le hub IoT vous permet d’effectuer les opérations suivantes : 

- Gérer la sécurité IoT Hub.

- Gestion de base de la sécurité d’un appareil IoT sans installation d’agent basée sur les données de télémétrie IoT Hub. 

- Gestion avancée pour la sécurité d’un appareil IoT basée sur le micro-agent.

> [!NOTE]
> Actuellement, Defender pour IoT prend uniquement en charge les hubs IoT de niveau standard.

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>Intégration à Defender pour IoT dans IoT Hub

Pour tous les nouveaux hubs IoT, Defender pour IoT est défini sur **Activé** par défaut. Vous pouvez vérifier que Defender pour IoT a la valeur **Activé** pendant le processus de création d’un hub IoT.

Pour vérifier que le bouton bascule est défini sur **Activé** :

1. Accédez au portail Azure.

1. Sélectionnez **IoT Hub** dans la liste des services Azure.

1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="Sélectionnez le bouton Créer dans la barre d’outils supérieure." lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. Sélectionnez l’onglet **Gestion**, puis vérifiez que le bouton bascule **Defender pour IoT** est défini sur **Activé**.

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="Vérifiez que le bouton bascule Defender pour IoT est défini sur Activé.":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>Intégration de Defender pour IoT à un hub IoT existant

Vous pouvez superviser vos modèles de communication appareil-à-cloud et cloud-à-appareil de gestion des identités d’appareils. Effectuez les opérations suivantes afin de démarrer le service : 

1. Accédez à IoT Hub. 

1. Sélectionnez le menu  **Vue d’ensemble de la sécurité**. 

1. Cliquez sur Sécuriser votre solution IoT, puis complétez le formulaire d’intégration. 


## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour configurer votre solution...

> [!div class="nextstepaction"]
> [Créer un jumeau de module pour le micro-agent Defender pour IoT (préversion)](quickstart-create-micro-agent-module-twin.md)
