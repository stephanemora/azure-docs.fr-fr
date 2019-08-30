---
author: ChrisGMsft
ms.service: iot-pnp
ms.topic: include
ms.date: 06/28/2019
ms.author: chrisgre
ms.openlocfilehash: 7e8174855800bc6beea8750c32a6dd32588ccd9e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879201"
---
## <a name="iot-plug-and-play-device"></a>Appareil IoT Plug-and-Play

Un appareil IoT Plug-and-Play est généralement un petit appareil informatique autonome qui peut collecter des données ou contrôler d’autres appareils. Il exécute un logiciel ou un microprogramme qui implémente les capacités déclarées dans un [modèle de capacité d’appareil](#device-capability-model).  Par exemple, un appareil IoT Plug-and-Play peut être un appareil de supervision de l’environnement ou un contrôleur de système d’irrigation agricole intelligent. Vous pouvez écrire des solutions IoT hébergées dans le cloud pour commander, contrôler et recevoir les données provenant d’appareils IoT Plug-and-Play. Vous trouverez des appareils IoT Plug-and-Play dans le [catalogue d’appareils Azure Certified pour IoT](https://catalog.azureiotsolutions.com/). Chaque appareil IoT Plug-and-Play du catalogue a été validé et dispose d’un [modèle de capacité d’appareil](#device-capability-model).

## <a name="digital-twin"></a>Jumeau numérique

Les jumeaux numériques sont des modèles d’appareils IoT Plug-and-Play.  Les jumeaux numériques sont modélisés à l’aide du [langage DTDL (Digital Twin Definition Language)](https://aka.ms/DTDL).  Vous pouvez utiliser l’API Azure IoT pour interagir avec des jumeaux numériques. 

## <a name="digital-twin-definition-language"></a>Langage DTDL (Digital Twin Definition Language)

Langage de description des modèles et des interfaces pour les [appareils IoT Plug-and-Play](#iot-plug-and-play-device).  Utilisez le [langage DTDL](https://aka.ms/DTDL) pour décrire les capacités d’un [jumeau numérique](#digital-twin). Vous permettez ainsi à la plateforme IoT et aux solutions IoT de tirer parti de la sémantique de l’entité.

## <a name="device-capability-model"></a>Modèle de capacité d’appareil

Un modèle de capacité d’appareil décrit un appareil et définit l’ensemble des interfaces qu’il implémente. Créez un modèle de capacité d’appareil correspondant à un appareil physique, un produit ou une référence SKU.

## <a name="interface"></a>Interface

Une interface décrit les capacités associées qui sont implémentées par un appareil ou un jumeau numérique. Vous pouvez réutiliser les interfaces dans différents modèles de capacité.

## <a name="property"></a>Propriété

Les propriétés sont des champs de données définis dans une [interface](#interface). Ils représentent un état d’un jumeau numérique. Vous pouvez déclarer des propriétés comme étant accessibles en lecture seule ou en écriture. Les propriétés accessibles en lecture seule sont définies par du code s’exécutant dans le contexte de l’appareil IoT Plug-and-Play proprement dit, par exemple le numéro de série.  Les propriétés accessibles en écriture sont définies par des entités externes, par exemple les seuils d’alarme.

## <a name="telemetry"></a>Télémétrie

Les champs de télémétrie définis dans une [interface](#interface) représentent des mesures. Ces mesures sont généralement des valeurs telles que des relevés de capteur.

## <a name="command"></a>Commande

Les commandes définies dans une [interface](#interface) représentent les méthodes qui peuvent être exécutées sur le jumeau numérique. Par exemple, une commande de réinitialisation permet de réinitialiser un appareil.
