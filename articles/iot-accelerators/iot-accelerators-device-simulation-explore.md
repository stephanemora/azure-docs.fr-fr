---
title: Prise en main de la solution de simulation d’appareil - Azure | Microsoft Docs
description: La solution de simulation d’accélérateur de solution IoT Suite est un outil qui peut être utilisé pour faciliter le développement et le test d’une solution IoT. Le service de simulation constitue une offre autonome, que vous pouvez utiliser avec d’autres accélérateurs de solution ou avec vos propres solutions personnalisées.
author: troyhopwood
manager: ''
ms.author: troyhop
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/15/2017
ms.topic: conceptual
ms.openlocfilehash: c427f2640e605533324eb349579c6a40a2a6a47f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627123"
---
# <a name="device-simulation-walkthrough"></a>Présentation de la simulation d’appareil

La simulation d’appareil Azure IoT est un outil qui peut être utilisé pour faciliter le développement et le test d’une solution IoT. La simulation d’appareil constitue une offre autonome, que vous pouvez utiliser avec d’autres accélérateurs de solution ou avec vos propres solutions personnalisées.

Ce didacticiel vous présente certaines des fonctionnalités de la simulation d’appareil. Il vous montre leur fonctionnement et vous permet de les utiliser pour tester vos propres solutions IoT.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Configurer une simulation
> * Démarrage et arrêt d’une simulation
> * Afficher les données de télémétrie

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin d’une instance déployée de la simulation d’appareil Azure IoT dans votre abonnement Azure.

Si vous n’avez pas encore déployé la simulation d’appareil, vous devez suivre le didacticiel [Déployer la simulation d’appareil Azure IoT](iot-accelerators-device-simulation-deploy.md).

## <a name="configuring-device-simulation"></a>Configuration de la simulation d’appareil

Vous pouvez configurer et exécuter la simulation d’appareil intégralement à partir du tableau de bord. Ouvrez le tableau de bord dans la page [Solutions approvisionnées](https://www.azureiotsolutions.com/) de l’accélérateur de solution IoT. Cliquez sur **Lancer** sous votre nouveau déploiement de simulation d’appareil.

### <a name="target-iot-hub"></a>IoT Hub cible

Vous pouvez utiliser la simulation d’appareil avec un IoT Hub préconfiguré ou avec n’importe quel autre IoT Hub :

![IoT Hub cible](./media/iot-accelerators-device-simulation-explore/targethub.png)

> [!NOTE]
> Vous pouvez utiliser un IoT Hub préconfiguré uniquement si vous avez choisi de créer un IoT Hub lorsque vous avez déployé la simulation d’appareil. Si vous n’avez pas d’IoT Hub, vous pouvez toujours en créer un à partir du [portail Azure](https://portal.azure.com).

Pour cibler un IoT Hub spécifique, vous devez fournir la chaîne de connexion **iothubowner**. Vous pouvez obtenir cette chaîne de connexion auprès du [portail Azure](https://portal.azure.com) :

1. Dans la page de configuration IoT Hub dans le portail Azure, cliquez sur **Shared access policies** (Stratégies d’accès partagé).
1. Cliquez sur **iothubowner**.
1. Copiez la clé primaire ou secondaire.
1. Collez cette valeur dans la zone de texte sous IoT Hub cible.

![IoT Hub cible](./media/iot-accelerators-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Modèle de l'appareil

Le modèle d’appareil vous permet de choisir le type d’appareil à simuler. Vous pouvez choisir un des modèles d’appareil préconfigurés ou définir une liste de capteurs pour un modèle d’appareil personnalisé :

![Modèle de l'appareil](./media/iot-accelerators-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Modèles d’appareil préconfigurés

La simulation d’appareil propose trois modèles d’appareil préconfigurés. Les modèles d’appareil Chiller (Refroidisseur), Elevator (Élévateur) et Truck (Camion) sont disponibles.

Les modèles d’appareil préconfigurés incluent plusieurs capteurs avec des comportements avancés définis dans un fichier JavaScript. Ces comportements personnalisés ne sont pas pris en charge dans l’interface utilisateur web. 

Le tableau suivant présente une liste des configurations pour chaque modèle d’appareil préconfiguré :

| Modèle de l'appareil | Capteur | Unité | 
| -------------| ------ | -----| 
| Chiller (Refroidisseur) | humidity | % |
| | pressure | psig | 
| | temperature | F | 
| Elevator (Élévateur) | Floor | 
| | Vibration | MM | 
| | Température | F | 
| Truck (Camion) | Latitude | |
| | Longitude | | 
| | speed | mph | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>Modèle d’appareil personnalisé

Les modèles d’appareil personnalisés vous permettent de définir des capteurs qui représentent plus fidèlement vos propres appareils. Un appareil personnalisé peut avoir jusqu'à 10 capteurs personnalisés.

Lorsque vous sélectionnez le type de modèle d’appareil personnalisé, vous pouvez ajouter de nouveaux capteurs en cliquant sur **+Add sensor** (+Ajouter capteur).

![Ajouter des capteurs](./media/iot-accelerators-device-simulation-explore/customsensors.png)

Les capteurs personnalisés possèdent les propriétés suivantes :

| Champ | Description |
| ----- | ----------- |
| Nom du capteur | Nom convivial pour le capteur, par exemple **température** ou **vitesse**. |
| Comportement | Les comportements permettent de faire varier les données de télémétrie d’un message à l’autre, afin de simuler des données réelles. **Incrément** augmente la valeur d’une unité à chaque message envoyé, en commençant par la valeur minimale. Une fois que la valeur maximale est atteinte, la propriété repart de la valeur minimale. **Décrément** se comporte de la même façon que **Incrément** mais dans le sens inverse. Le comportement **Aléatoire** génère une valeur aléatoire comprise entre la valeur minimale et la valeur maximale. |
| Valeur minimale | Le plus petit nombre de la plage acceptable. |
| Valeur maximale | Le plus grand nombre de la plage acceptable. |
| Unité | L’unité de mesure pour le capteur, par exemple °F ou MPH. |

### <a name="number-of-devices"></a>Nombre d’appareils

À l’heure actuelle, la simulation d’appareil vous permet de simuler jusqu’à 20 000 appareils.

![Nombre d’appareils](./media/iot-accelerators-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Fréquence de télémétrie

La fréquence de télémétrie vous permet d’indiquer la fréquence à laquelle vos appareils simulés doivent envoyer des données à l’IoT Hub. Vous pouvez aussi bien envoyer des données très fréquemment (toutes les 10 secondes) ou très rarement (toutes les 99 heures, 59 minutes et 59 secondes).

![Fréquence de télémétrie](./media/iot-accelerators-device-simulation-explore/frequency.png)

> [!NOTE]
> Si vous utilisez un IoT Hub différent de l’IoT Hub préapprovisionné, vous devez envisager des limites de message pour votre IoT Hub cible. Par exemple, si vous avez 1 000 appareils simulés qui envoient des données de télémétrie toutes les 10 secondes à un hub S1, vous atteindrez la limite de télémétrie de l’hub en un peu plus d’une heure.

### <a name="simulation-duration"></a>Durée de la simulation

Vous pouvez choisir d’exécuter votre simulation pour une durée spécifique ou jusqu'à ce que vous l’arrêtiez explicitement. Lorsque vous choisissez une durée spécifique, celle-ci peut être comprise entre 10 minutes et 99 heures, 59 minutes et 59 secondes.

![Durée de la simulation](./media/iot-accelerators-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Démarrer et arrêter la simulation

Une fois toutes les données de configuration nécessaires ajoutées dans le formulaire, le bouton **Démarrer la simulation** devient actif. Pour démarrer la simulation, cliquez sur ce bouton.

![Démarrer la simulation](./media/iot-accelerators-device-simulation-explore/start.png)

Si vous avez indiqué une durée spécifique pour votre simulation, elle s’arrête automatiquement à la fin du temps prévu. Vous pouvez toujours arrêter la simulation plus tôt en cliquant sur **Arrêter la simulation.**

Si vous choisissez d’exécuter votre simulation indéfiniment, elle s’exécute jusqu'à ce que vous cliquiez sur **Arrêter la simulation**. Vous pouvez fermer votre navigateur et revenir à la page Simulation d’appareil pour arrêter votre simulation à tout moment.

![Arrêter la simulation](./media/iot-accelerators-device-simulation-explore/stop.png)

> [!NOTE]
> Vous ne pouvez exécuter qu’une seule simulation à la fois. Vous devez arrêter la simulation en cours d’exécution avant d’en démarrer une autre.
