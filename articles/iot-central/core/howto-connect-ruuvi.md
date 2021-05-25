---
title: Connecter un capteur RuuviTag dans Azure IoT Central | Microsoft Docs
description: Apprenez à connecter un capteur environnemental RuuviTag à votre application IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 13b1e22f1e1e5f51d524e80d0bf102b744fbec3d
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109684592"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Connecter un capteur RuuviTag à votre application Azure IoT Central

Cet article explique comment connecter un capteur RuuviTag à une application Microsoft Azure IoT Central.

Qu'est-ce qu'une balise Ruuvi ?

RuuviTag est une plateforme open source avancée pour balises dotées de capteurs conçue pour répondre aux besoins des clients professionnels, des développeurs, des fabricants, des étudiants et des amateurs. L’appareil est configuré pour fonctionner dès que vous le sortez de son emballage et que vous le déployez là où vous en avez besoin. Il s’agit d’une balise Bluetooth Low Energy intégrant un capteur environnemental et un accéléromètre.

RuuviTag communique via BLE (Bluetooth Low Energy) et nécessite un appareil de passerelle pour s’adresser à Azure IoT Central. Veillez à disposer d’un appareil de passerelle, comme le Rigado Cascade 500, configuré pour permettre à un capteur RuuviTag de se connecter à IoT Central.

Suivez les [instructions disponibles ici](./howto-connect-rigado-cascade-500.md) si vous souhaitez configurer un appareil de passerelle Rigado Cascade 500.

## <a name="prerequisites"></a>Prérequis

Pour connecter des capteurs RuuviTag, vous devez disposer des ressources suivantes :

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- Un capteur RuuviTag. Pour plus d'informations, consultez le site de [RuuviTag](https://ruuvi.com/).

- Un appareil Rigado Cascade 500 ou une autre passerelle BLE. Pour plus d’informations, veuillez visiter [Rigado](https://www.rigado.com/).


## <a name="add-a-ruuvitag-device-template"></a>Ajouter un modèle d'appareil RuuviTag

Pour intégrer un capteur RuuviTag dans votre instance d’application Azure IoT Central, vous devez configurer un modèle d’appareil correspondant dans votre application.

Pour ajouter un modèle d'appareil RuuviTag :

1. Accédez à l’onglet ***Modèles d’appareil** _ dans le volet gauche, sélectionnez _*+ Nouveau** :  ![Créer un modèle d’appareil](./media/howto-connect-ruuvi/devicetemplate-new.png). La page vous donne la possibilité de ***Créer un modèle personnalisé**_ ou d’_ *_Utiliser un modèle d’appareil préconfiguré_**
1. Sélectionnez le modèle d'appareil RuuviTag dans la liste des modèles d'appareil préconfigurés, comme indiqué ci-dessous :  ![Sélectionner le modèle d'appareil RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Sélectionnez ***Suivant : Personnaliser*** pour passer à l'étape suivante.
1. Dans l'écran suivant, sélectionnez ***Créer*** pour intégrer le modèle d'appareil C500 à votre application IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Connecter un capteur RuuviTag

Comme indiqué précédemment, pour connecter le capteur RuuviTag à votre application IoT Central, vous devez configurer un appareil de passerelle. Les étapes ci-dessous partent du principe que vous avez configuré un appareil de passerelle Rigado Cascade 500.  

1. Allumez votre appareil Rigado Cascade 500 et reliez-le à votre connexion réseau (via Ethernet ou sans fil).
1. Retirez le couvercle du RuuviTag et tirez sur la languette en plastique pour sécuriser la connexion avec la batterie.
1. Placez le capteur RuuviTag à proximité d’une passerelle Rigado Cascade 500 déjà configurée dans votre application IoT Central.
1. En quelques secondes, votre RuuviTag doit apparaître dans la liste des appareils d'IoT Central.  
    ![Liste des appareils RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

Vous pouvez maintenant utiliser ce capteur RuuviTag dans votre application IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Créer un RuuviTag simulé

Si vous n’avez pas de capteur RuuviTag physique, vous pouvez créer un capteur RuuviTag simulé à utiliser à des fins de test dans votre application Azure IoT Central.

Pour créer un RuuviTag simulé :

1. Sélectionnez **Appareils > RuuviTag**.
1. Sélectionnez **+Nouveau**.
1. Spécifiez un **ID d'appareil** unique et un **nom d'appareil** convivial.  
1. Activez le paramètre **Simulé**.
1. Sélectionnez **Create** (Créer).  

## <a name="next-steps"></a>Étapes suivantes

Voici quelques suggestions pour continuer :

- En savoir plus sur la [connectivité des appareils dans Azure IoT Central](./concepts-get-connected.md)
- Découvrez comment [superviser la connectivité des appareils à l’aide d’Azure CLI](./howto-monitor-devices-azure-cli.md)
