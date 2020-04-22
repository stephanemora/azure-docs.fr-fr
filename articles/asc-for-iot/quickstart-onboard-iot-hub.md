---
title: 'Démarrage rapide : Activer le service'
description: Découvrez comment intégrer et activer le service de sécurité Azure Security Center pour IoT dans votre hub Azure IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310751"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Démarrage rapide : Intégrer le service Azure Security Center pour IoT dans IoT Hub

Cet article explique comment activer le service Azure Security Center pour IoT sur votre IoT Hub. Si vous n’avez pas de hub IoT, pour commencer, voir [Créer un hub IoT à l’aide du portail Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal).

> [!NOTE]
> Actuellement, Azure Security Center pour IoT prend uniquement en charge les hubs IoT de niveau standard.

## <a name="prerequisites-for-enabling-the-service"></a>Prérequis pour l’activation du service

- Espace de travail Log Analytics
  - Par défaut, Azure Security Center pour IoT stocke deux types d’informations dans votre espace de travail Log Analytics : des **alertes de sécurité** et des **recommandations**.
  - Vous pouvez choisir d’ajouter le stockage d’un type d’information supplémentaire : les **événements bruts**. Notez que le stockage d’**événements bruts** dans Log Analytics engendre des frais de stockage supplémentaires.
- IoT Hub (niveau standard)
- Satisfaire à tous les [prérequis du service](service-prerequisites.md)

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Activer Azure Security Center pour IoT sur votre IoT Hub

Pour activer la sécurité sur votre hub IoT :

1. Ouvrez votre **hub IoT** dans le portail Azure.
1. Dans le menu **Sécurité**, cliquez sur **Sécuriser votre solution IoT**.

Félicitations ! Vous avez activé Azure Security Center pour IoT sur votre IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Gestion de la géolocalisation et des adresses IP

Pour sécuriser votre solution IoT, les adresses IP des connexions entrantes et sortantes vers et depuis vos appareils IoT, IoT Edge ainsi que les hubs IoT sont collectées et stockées par défaut. Ces informations sont essentielles pour détecter une connectivité anormale à partir de sources IP suspectes, par exemple lorsque des tentatives sont effectuées pour établir des connexions à partir d’une source IP d’un botnet connu ou d’une source IP en dehors de votre géolocalisation. Le service Azure Security Center pour IoT offre la flexibilité nécessaire pour activer et désactiver la collecte des données d’adresses IP à tout moment.

Pour activer ou désactiver la collecte des données d’adresses IP :

1. Ouvrez votre hub IoT, puis sélectionnez **Vue d’ensemble** dans le menu **Sécurité**.
1. Choisissez l’écran **Paramètres** et modifiez les paramètres de géolocalisation et/ou de gestion IP comme vous le souhaitez.

### <a name="log-analytics-creation"></a>Création de Log Analytics

Quand Azure Security Center pour IoT est activé, un espace de travail Azure Log Analytics par défaut est créé pour stocker les événements de sécurité bruts, les alertes et les recommandations pour vos appareils IoT, IoT Edge et IoT Hub. Chaque mois, les cinq (5) premiers Go de données ingérées par client sur le service Azure Log Analytics sont gratuits. Chaque Go de données ingérées dans votre espace de travail Azure Log Analytics est conservé gratuitement pendant les 31 premiers jours. Découvrez plus en détail les tarifs de [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Pour modifier la configuration de l’espace de travail de Log Analytics :

1. Ouvrez votre hub IoT, puis sélectionnez **Vue d’ensemble** dans le menu **Sécurité**.
1. Choisissez l’écran **Paramètres** et modifiez les paramètres de configuration de l’espace de travail de Log Analytics comme vous le souhaitez.

### <a name="customize-your-iot-security-solution"></a>Personnaliser votre solution de sécurité IoT

Par défaut, l’activation de la solution Azure Security Center pour IoT sécurise automatiquement tous les hubs IoT dans votre abonnement Azure.

Pour activer ou désactiver le service Azure Security Center pour IoT sur un hub IoT spécifique :

1. Ouvrez votre hub IoT, puis sélectionnez **Vue d’ensemble** dans le menu **Sécurité**.
1. Choisissez l’écran **Paramètres** et modifiez les paramètres de sécurité de n’importe quel hub IoT dans votre abonnement Azure comme vous le souhaitez.

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour configurer votre solution...

> [!div class="nextstepaction"]
> [Configurer votre solution](quickstart-configure-your-solution.md)
