---
title: Intégrer des données SIM à la solution de surveillance à distance - Azure| Microsoft Docs
description: Cet article décrit comment intégrer des données SIM Telefónica à la solution de surveillance à distance.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Intégrer des données SIM à la solution de surveillance à distance

## <a name="overview"></a>Vue d'ensemble
Les appareils IoT se connectent souvent au cloud à l’aide d’une carte SIM qui leur permet d’envoyer des flux de données à partir de n’importe quel endroit. La solution de surveillance à distance Azure IoT permet l’intégration de données de gestion SIM afin que les opérateurs puissent également suivre l’intégrité de l’appareil via les données fournies par la carte SIM. La surveillance à distance propose une intégration prête à l’emploi à Telefónica IoT, autorisant les clients qui utilisent sa plateforme de connectivité IoT à synchroniser les données de connectivité de cartes SIM de leur appareil avec les solutions. Cette solution peut être étendue pour prendre en charge d’autres fournisseurs de compagnies de téléphone via le dépôt GitHub.
Ce tutoriel vous montre comment effectuer les opérations suivantes :
* Intégrer des données SIM à la solution de surveillance à distance
* Voir les données de télémétrie en temps réel
* Voir les données SIM 

## <a name="telefonica-iot-integration-setup"></a>Configuration de l’intégration à Telefónica IoT

### <a name="prerequisites"></a>Prérequis

Pour synchroniser vos données de connectivité dans la solution de surveillance à distance Azure, effectuez les étapes suivantes :

1.  Remplissez une demande sur le [site de Telefónica](https://iot.telefonica.com/contact), sélectionnez l’option de **surveillance à distance Azure**, avec vos données de contact.
2.  Telefónica va activer votre compte. 
3.  Si vous n’êtes pas encore un client Telefónica et que vous souhaitez profiter de ce service ou d’autres services prêts pour le cloud de connectivité IoT, visitez le [site de Telefónica](https://iot.telefonica.com/contact) et sélectionnez l’option **Connectivité**.

### <a name="telefonica-sim-setup"></a>Configuration de la carte SIM Telefónica
L’association de l’ID d’appareil jumeau Azure et de la carte SIM Telefónica va reposer sur la propriété « alias » de la carte SIM Telefónica IoT. 

Accédez à [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) (Portail de la plateforme de connectivité Telefónica IoT) > SIM Inventory (Inventaire SIM) > sélectionnez votre carte SIM et mettez à jour chaque « alias » SIM avec l’ID d’appareil jumeau voulu. 

Cette tâche peut également être effectuée en mode bloc (consultez les manuels de l’utilisateur de la plateforme de connectivité Telefónica IoT)

![Mise à jour de Telefónica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Pour connecter votre appareil à la surveillance à distance, vous pouvez suivre ces tutoriels en utilisant [C](iot-suite-connecting-devices-linux.md) ou [Node](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Voir les données de télémétrie d’appareil et les propriétés de la carte SIM
Une fois que votre compte Telefónica est configuré correctement et que votre appareil est connecté, vous pouvez voir les détails de l’appareil et les données SIM.
Les paramètres de connectivité suivants peuvent être publiés :
* ICCID
* IP
* Présence de réseau
* État de la carte SIM
* Emplacement sur le réseau
* Trafic de données consommées

![Tableau de bord](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble de l’intégration des données SIM à la surveillance à distance Azure IoT, voici les étapes suivantes suggérées pour les accélérateurs de solution :

* [Exploiter la solution de surveillance à distance Azure IoT](iot-suite-remote-monitoring-explore.md)
* [Effectuer la surveillance avancée](iot-suite-remote-monitoring-monitor.md)
* [Gestion de vos appareils](iot-suite-remote-monitoring-manage.md)
* [Résoudre les problèmes de l’appareil](iot-suite-remote-monitoring-maintain.md)

