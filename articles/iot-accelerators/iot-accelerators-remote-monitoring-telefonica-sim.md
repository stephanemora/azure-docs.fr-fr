---
title: Intégrer des données SIM à la solution de supervision à distance - Azure| Microsoft Docs
description: Cet article décrit comment intégrer des données SIM Telefónica à la solution de supervision à distance.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 21d84bea808ba5832b81d415001a3fc25a7e1630
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006761"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Intégrer des données SIM à la solution de supervision à distance

Les appareils IoT se connectent souvent au cloud à l’aide d’une carte SIM qui leur permet d’envoyer des flux de données à partir de n’importe quel endroit. La solution de supervision à distance d’Azure IoT permet l’intégration des données de connectivité managée d’IoT : les opérateurs peuvent ainsi également suivre l’intégrité de l’appareil via les données fournies par la carte SIM IoT.

La surveillance à distance fournit une intégration avec la connectivité IoT Telefónica, ce qui permet aux clients utilisant sa plateforme de connectivité IoT de synchroniser les données de connectivité des cartes SIM de leurs appareils à leurs solutions. Cette solution peut être étendue pour prendre en charge d’autres fournisseurs de connectivité IoT via un [référentiel](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) GitHub.

Dans ce tutoriel, vous allez apprendre à :

* Intégrer des données SIM IoT Telefónica à la solution de supervision à distance
* Afficher les données de télémétrie en temps réel
* Voir les données SIM

## <a name="telefnica-iot-integration-setup"></a>Configuration de l’intégration d’IoT Telefónica

### <a name="prerequisites"></a>Conditions préalables requises

Cette fonctionnalité supplémentaire de surveillance à distance est actuellement en préversion. Pour synchroniser vos données de connectivité dans la solution de supervision à distance Azure, effectuez les étapes suivantes :

1. Remplissez un formulaire de demande sur le [site de Telefónica](https://iot.telefonica.com/contact) et sélectionnez l’option **Azure Remote Monitoring**, en incluant vos données de contact.
2. Telefónica active votre compte.
3. Si vous n’êtes pas encore client de Telefónica et que vous souhaitez profiter de ce service ou d’autres services cloud de connectivité IoT, visitez le [site de Telefónica](https://iot.telefonica.com/) et sélectionnez l’option **Connectivity**.

### <a name="telefnica-sim-setup"></a>Configuration de carte SIM Telefónica
L’association d’ID de jumeau d’appareil SIM Telefónica et Azure est basée sur la propriété « alias » de carte SIM IoT Telefónica. 

Accédez à [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM Inventory > sélectionnez votre carte SIM et mettez à jour chaque « alias » de carte SIM avec votre ID de jumeau d’appareil souhaité. Vous pouvez aussi effectuer cette tâche en bloc (consultez les manuels utilisateur de la plateforme de connectivité IoT Telefónica).

Vous pouvez aussi effectuer cette tâche en bloc (consultez les manuels utilisateur de la plateforme de connectivité IoT Telefónica).

![Mise à jour de Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Pour connecter votre appareil à la surveillance à distance, vous pouvez suivre ces tutoriels en utilisant [C](iot-accelerators-connecting-devices-linux.md) ou [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Voir les données de télémétrie d’appareil et les propriétés de la carte SIM

Une fois votre compte Telefónica configuré correctement et votre appareil connecté, vous pouvez afficher les détails de l’appareil et les données SIM.

Les paramètres de connectivité suivants sont publiés :

* ICCID
* IP
* Présence de réseau
* État de la carte SIM
* Emplacement sur le réseau
* Trafic de données consommées

![Capture d’écran de la fenêtre d’Explorateur d’appareils dans le tableau de bord de supervision à distance Azure IoT. Une ligne présentant les détails de l’appareil WeatherStation est mise en surbrillance.](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble de l’intégration des données SIM à la surveillance à distance Azure IoT, voici les étapes suivantes suggérées pour les accélérateurs de solution :

* [Exploiter la solution de supervision à distance Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Effectuer la surveillance avancée](iot-accelerators-remote-monitoring-monitor.md)
* [Gestion de vos appareils](iot-accelerators-remote-monitoring-manage.md)
* [Résoudre les problèmes de l’appareil](iot-accelerators-remote-monitoring-maintain.md)

