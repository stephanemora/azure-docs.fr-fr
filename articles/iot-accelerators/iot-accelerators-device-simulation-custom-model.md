---
title: Configurer un modèle d’appareil personnalisé - Azure | Microsoft Docs
description: Cet article décrit la configuration d’un modèle d’appareil personnalisé dans l’accélérateur de solution de simulation d’appareil.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967476"
---
# <a name="device-models"></a>Modèles d’appareils

Lorsque vous configurez une simulation, vous pouvez choisir d’utiliser un modèle d’appareil existant avec un ensemble prédéfini de capteurs, ou de créer un modèle d’appareil personnalisé dont vous choisirez les capteurs simulés. Les capteurs personnalisés vous permettent de modéliser avec une plus grande fidélité vos appareils réels.

## <a name="pre-configured-device-models"></a>Modèles d’appareils préconfigurés

L’accélérateur de solution de simulation d’appareil propose trois modèles d’appareils préconfigurés : des refroidisseurs, des ascenseurs et des camions.

Les modèles d’appareils préconfigurés ont plusieurs capteurs avec des comportements avancés définis dans un fichier JavaScript. Vous ne pouvez pas configurer ces comportements dans l’interface utilisateur web.

Le tableau suivant présente les configurations de chaque modèle d’appareil préconfiguré :

| Modèle de l'appareil  | Capteur           | Unité  |
| ------------- | ---------------- | ----- |
| Chiller (Refroidisseur)       | humidity         | %     |
|               | pressure         | psig  |
|               | temperature      | F     |
| Elevator (Élévateur)      | Floor            |       |
|               | Vibration        | MM    |
|               | Température      | F     |
| Truck (Camion)         | Latitude         |       |
|               | Longitude        |       |
|               | speed            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>Modèles d’appareils personnalisés

Les modèles d’appareils personnalisés vous permettent de modéliser des capteurs qui représentent plus fidèlement vos propres appareils. Un appareil personnalisé peut avoir jusqu'à 10 capteurs personnalisés.

Lorsque vous sélectionnez le type de modèle d’appareil personnalisé, vous ajoutez de nouveaux capteurs en cliquant sur **+Add sensor** (+Ajouter capteur).

[![Configurer les capteurs](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Les capteurs personnalisés possèdent les propriétés suivantes :

| Champ     | Description |
| --------- | ----------- |
| Nom du capteur | Nom convivial pour le capteur, par exemple **température** ou **vitesse**.  |
| Comportement  | Les comportements permettent de faire varier les données de télémétrie d’un message à l’autre, afin de simuler des données réelles. **Incrément** augmente la valeur d’une unité à chaque message envoyé, en commençant par la valeur minimale. Une fois que la valeur maximale est atteinte, la propriété repart de la valeur minimale. **Décrément** se comporte de la même façon que **Incrément** mais dans le sens inverse. Le comportement **Aléatoire** génère une valeur aléatoire comprise entre la valeur minimale et la valeur maximale. |
| Valeur minimale | Le plus petit nombre de la plage acceptable. |
| Valeur maximale | Le plus grand nombre de la plage acceptable. |
| Unité      | L’unité de mesure pour le capteur, par exemple °F ou MPH. |

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de procédure, vous avez appris à configurer un modèle d’appareil personnalisé pour une simulation. Vous souhaiterez ensuite peut-être explorer d’autres [accélérateurs de solutions IoT](about-iot-accelerators.md).