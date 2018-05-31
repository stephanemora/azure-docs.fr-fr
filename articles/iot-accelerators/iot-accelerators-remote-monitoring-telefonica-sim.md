---
title: Intégrer des données SIM à la solution de surveillance à distance - Azure| Microsoft Docs
description: Cet article décrit comment intégrer des données de carte SIM Telefónica à la solution de surveillance à distance.
services: iot-suite
suite: iot-suite
author: hegate
manager: timlt
ms.author: hegate
ms.service: iot-suite
ms.date: 05/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 590a24113d66edacc6edcfe988330f643f1aa57a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367551"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Intégrer des données SIM à la solution de surveillance à distance

Les appareils IoT se connectent souvent au cloud à l’aide d’une carte SIM qui leur permet d’envoyer des flux de données à partir de n’importe quel endroit. La solution de surveillance à distance d’Azure IoT permet l’intégration des données de connectivité managée d’IoT : les opérateurs peuvent ainsi également suivre l’intégrité de l’appareil via les données fournies par la carte SIM IoT.

La surveillance à distance propose une intégration prête à l’emploi à la connectivité Telefónica IoT, permettant aux clients qui utilisent sa plateforme de connectivité IoT de synchroniser les données de connectivité des cartes SIM de leur appareil avec leurs solutions. Cette solution peut être étendue pour prendre en charge d’autres fournisseurs de connectivité IoT via un [référentiel](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) GitHub.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

* Intégrer des données de carte SIM Telefónica IoT à la solution de surveillance à distance
* Affichage des données de télémétrie en temps réel
* Voir les données SIM

## <a name="telefnica-iot-integration-setup"></a>Configuration de l’intégration Telefónica IoT

### <a name="prerequisites"></a>Prérequis


Cette fonctionnalité supplémentaire de surveillance à distance est actuellement en préversion. Pour synchroniser vos données de connectivité dans la solution de surveillance à distance Azure, effectuez les étapes suivantes :

1. Remplissez une requête sur le [site de Telefónica](https://iot.Telefónica.com/contact), sélectionnez l’option **Surveillance à distance Azure**, avec vos données de contact.
2. Telefónica active votre compte.
3. Si vous n’êtes pas encore un client Telefónica et que vous souhaitez profiter de ce service ou d’autres services prêts pour le cloud de connectivité IoT, visitez le [site de Telefónica](https://iot.Telefónica.com/contact) et sélectionnez l’option **Connectivité**.

### <a name="telefnica-sim-setup"></a>Configuration de la carte SIM Telefónica
L’association de l’ID d’appareil jumeau Azure et de la carte SIM Telefónica est basée sur la propriété « alias » de la carte SIM Telefónica IoT. 

Accédez à [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) (Portail de la plateforme de connectivité Telefónica IoT) > SIM Inventory (Inventaire SIM) > sélectionnez votre carte SIM et mettez à jour chaque « alias » de carte SIM avec l’ID d’appareil jumeau voulu. Cette tâche peut également être effectuée en mode bloc (consultez les manuels de l’utilisateur de la plateforme de connectivité Telefónica IoT).

Cette tâche peut également être effectuée en mode bloc (consultez les manuels de l’utilisateur de la plateforme de connectivité Telefónica IoT)

![Mise à jour de Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Pour connecter votre appareil à la surveillance à distance, vous pouvez suivre ces tutoriels en utilisant [C](iot-accelerators-connecting-devices-linux.md) ou [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Voir les données de télémétrie d’appareil et les propriétés de la carte SIM

Une fois que votre compte Telefónica est configuré correctement et que votre appareil est connecté, vous pouvez voir les détails de l’appareil et les données de la carte SIM.

Les paramètres de connectivité suivants sont publiés :

* ICCID
* IP
* Présence de réseau
* État de la carte SIM
* Emplacement sur le réseau
* Trafic de données consommées

![tableau de bord](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble de l’intégration des données SIM à la surveillance à distance Azure IoT, voici les étapes suivantes suggérées pour les accélérateurs de solution :

* [Exploiter la solution de surveillance à distance Azure IoT](iot-accelerators-remote-monitoring-explore.md)
* [Effectuer la surveillance avancée](iot-accelerators-remote-monitoring-monitor.md)
* [Gestion de vos appareils](iot-accelerators-remote-monitoring-manage.md)
* [Résoudre les problèmes de l’appareil](iot-accelerators-remote-monitoring-maintain.md)

