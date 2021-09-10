---
title: Workflow de l’interface du module Speech Azure Percept
description: Décrit le workflow et les méthodes disponibles pour le module Speech Azure Percept
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 7/19/2021
ms.custom: template-concept
ms.openlocfilehash: 8dec6a6c4162225b4f5e9ddf3ce74b739fd39562
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222828"
---
# <a name="azure-percept-speech-module-interface-workflow"></a>Workflow de l’interface du module Speech Azure Percept

Cet article décrit comment le module Speech Azure Percept interagit avec IoT Hub. Pour ce faire, il utilise un jumeau de module et une méthode de module. Il répertorie en outre les appels de méthode directs utilisés pour appeler le module Speech.

## <a name="speech-module-interaction-with-iot-hub-via-module-twin-and-module-method"></a>Interaction du module Speech avec IoT Hub via un jumeau de module et une méthode de module
- IoT Hub utilise un jumeau de module pour déployer les paramètres de module Speech. Les paramètres sont enregistrés dans les propriétés. Le module Speech peut mettre à jour les informations d’appareil et les données de télémétrie dans IoT Hub par les propriétés signalées par le jumeau de module.
- IoT Hub peut envoyer des demandes de contrôle au module Speech via la méthode de module.
- IoT Hub peut récupérer l’état du module Speech via la méthode de module.

Pour plus d’informations, consultez [Comprendre et utiliser les jumeaux de module dans IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md).


## <a name="speech-module-states"></a>États du module Speech
- **IoTInitialized** : indique que le module IoT est initialisé et que le réseau entre le module Speech et le module Edge Hub est connecté.
- **Authenticating** : l’authentification d’appareil Azure Audio est en cours.
- **Authenticated** : l’authentification d’appareil Azure Audio est terminée. En cas d’échec, IoT Hub reçoit un message d’erreur.
- **MicDiscovering** : commencer à énumérer le réseau de microphones via l’interface ALSA.
- **MicDiscovered** : l’énumération du réseau de microphones est terminée. En cas d’échec, IoT Hub reçoit un message d’erreur.
- **SpeechConfigured** : la configuration CC est terminée. En cas d’échec, IoT Hub reçoit un message d’erreur.
- **SpeechStarted** : indique que le bot est configuré et exécuté.
- **SpeechStopped** : indique que le bot est arrêté.
- **DeviceRemoved** : indique que l’appareil Azure Audio est supprimé.


## <a name="speech-bot-states"></a>États du bot Speech
L’interrogation des états du bot Speech est seulement prise en charge sous l’état de module Speech **SpeechStarted**.
- **Ready** : KWS est prêt et attend l’activation vocale.
- **Listening** : le bot écoute l’entrée vocale.
- **Thinking** : le bot attend une réponse.
- **Speaking** : le bot obtient une réponse et la communique.

## <a name="interaction-between-iot-hub-and-the-speech-module"></a>Interaction entre IoT Hub et le module Speech 
Cette section décrit l’interaction de IoT Hub avec le module Speech. Comme indiqué dans le diagramme, il existe trois types de messages.
- Déploiement avec les propriétés et mises à jour nécessaires avec les propriétés signalées
- Appel de la méthode de module
- Mise à jour de la télémétrie

:::image type="content" source="media/speech-module-interface-workflow/speech-module-diagram.png" alt-text="Diagramme montrant l’interaction entre IoT Hub et le module Speech":::

IoT Hub appelle la méthode de module avec deux paramètres :
- nom de la méthode de module (sensible à la casse) ;
- charge utile de la méthode.

Le module Speech répond avec :
- Code d’état
    - **0** = inactif
    - **102** = traitement en cours
    - **200** = réussite
    - **202** = en attente
    - **500** = échec
    - **501** = non présent
- Charge utile d’état

Voici un exemple d’utilisation de la méthode de module GetModuleState :
1. Appelez la méthode avec les paramètres suivants :
    - Chaîne : "GetModuleState"
    - Non spécifié
1. Réponse :
    - Code d’état : 200
    - Charge utile : "DeviceRemoved"

## <a name="next-steps"></a>Étapes suivantes
Essayez d’appliquer ces concepts lors de la [configuration d’une application d’assistant vocal à l’aide d’Azure IoT Hub](./how-to-configure-voice-assistant.md).