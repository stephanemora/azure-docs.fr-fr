---
title: Connecter un capteur RuuviTag dans Azure IoT Central | Microsoft Docs
description: Apprenez à connecter un capteur environnemental RuuviTag à votre application IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: f1d152c921d38931f8c67396fc5769cfd2dfcf58
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468223"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Connecter un capteur RuuviTag à votre application Azure IoT Central

Cet article vous explique comment, en tant que concepteur de solutions, vous pouvez connecter un capteur RuuviTag à votre application Microsoft Azure IoT Central.

Qu'est-ce qu'une balise Ruuvi ?

RuuviTag est une plateforme open source avancée pour balises dotées de capteurs conçue pour répondre aux besoins des clients professionnels, des développeurs, des fabricants et des étudiants, mais vous pouvez également l'utiliser chez vous ou dans le cadre de vos activités personnelles. L'appareil est configuré pour fonctionner dès que vous le sortez de son emballage et il est prêt à être déployé là où vous en avez besoin. Il s'agit d'une balise Bluetooth Low Energy dotée d'un capteur environnemental et d'un accéléromètre. 

RuuviTag communique via BLE (Bluetooth Low Energy) et nécessite donc un appareil de passerelle pour s'adresser à Azure IoT Central. Assurez-vous que vous disposez d'un appareil de passerelle, comme le Rigado Cascade 500, configuré pour pouvoir connecter un RuuviTag à IoT Central. 

Suivez les [instructions disponibles ici](./howto-connect-rigado-cascade-500-pnp.md) si vous souhaitez configurer un appareil de passerelle Rigado Cascade 500.

## <a name="prerequisites"></a>Prérequis
Pour connecter des capteurs RuuviTag, vous devez disposer des ressources suivantes :

* Un capteur RuuviTag. Pour plus d'informations, consultez le site de [RuuviTag](https://ruuvi.com/). 
* Un appareil Rigado Cascade 500 ou une autre passerelle BLE. Pour plus d'informations, consultez le site de [Rigado](https://www.rigado.com/).
* Une application Azure IoT Central créée à partir d'un des modèles d'application disponibles en préversion. Pour plus d'informations, consultez [Créer une application](https://docs.microsoft.com/azure/iot-central/core/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="add-a-ruuvitag-device-template"></a>Ajouter un modèle d'appareil RuuviTag

Pour intégrer un capteur RuuviTag à votre instance d'application Azure IoT Central, vous devez configurer un modèle d'appareil correspondant dans votre application.

Pour ajouter un modèle d'appareil RuuviTag : 

1. Accédez à l'onglet ***Modèles d'appareil*** dans le volet gauche et sélectionnez **+ Nouveau** : ![Créer un modèle d'appareil](./media/howto-connect-ruuvi/devicetemplate-new.png). La page vous donne la possibilité de ***Créer un modèle personnalisé*** ou d'***Utiliser un modèle d'appareil préconfiguré***.
1. Sélectionnez le modèle d'appareil RuuviTag dans la liste des modèles d'appareil préconfigurés, comme indiqué ci-dessous : ![Sélectionner le modèle d'appareil RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Sélectionnez ***Suivant : Personnaliser*** pour passer à l'étape suivante. 
1. Dans l'écran suivant, sélectionnez ***Créer*** pour intégrer le modèle d'appareil C500 à votre application IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Connecter un capteur RuuviTag

Comme indiqué ci-dessus, pour connecter le RuuviTag à votre application IoT Central, vous devez configurer un appareil de passerelle. Les étapes ci-dessous partent du principe que vous avez configuré un appareil de passerelle Rigado Cascade 500.  

1. Allumez votre appareil Rigado Cascade 500 et reliez-le à votre connexion réseau (via Ethernet ou sans fil).
1. Retirez le couvercle du RuuviTag et tirez sur la languette en plastique pour sécuriser la connexion avec la batterie. 
1. Placez le RuuviTag à proximité immédiate de la passerelle Rigado Cascade 500 précédemment configurée avec votre application IoT Central. 
1. En quelques secondes, votre RuuviTag doit apparaître dans la liste des appareils d'IoT Central.  
![Liste des appareils RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png) Vous pouvez maintenant utiliser ce RuuviTag dans votre application IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Créer un RuuviTag simulé
Si vous n'avez pas de RuuviTag physique sous la main, vous pouvez créer un capteur RuuviTag simulé à utiliser à des fins de test dans votre application Azure IoT Central.

Pour créer un RuuviTag simulé :

1. Sélectionnez **Appareils > RuuviTag**. 
1. Sélectionnez **+Nouveau**. 
1. Spécifiez un **ID d'appareil** unique et un **nom d'appareil** convivial.  
1. Activez le paramètre **Simulé**.
1. Sélectionnez **Create** (Créer).  

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à connecter un RuuviTag à votre application Azure IoT Central, nous vous conseillons d'apprendre à [personnaliser votre application IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) pour créer une solution de bout en bout. 