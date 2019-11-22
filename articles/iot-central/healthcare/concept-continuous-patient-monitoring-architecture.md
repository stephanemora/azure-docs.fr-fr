---
title: Architecture de surveillance continue des patients dans Azure IoT Central | Microsoft Docs
description: Découvrez plus d’informations sur une architecture de surveillance continue des patients.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027474"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architecture du suivi continu des patients

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Vous pouvez créer des solutions de surveillance continue des patients en tirant parti du modèle d’application fourni et en utilisant l’architecture décrite ci-dessous comme guide.

>[!div class="mx-imgBorder"] 
>![Architecture de surveillance continue des patients](media/cpm-architecture.png)

1. Appareils médicaux communiquant via Bluetooth Low Energy (BLE)
1. Passerelle de téléphone portable recevant des données BLE et les envoyant à IoT Central
1. Exportation en continu des données médicales des patients vers l’API Azure pour FHIR (Fast Healthcare Interoperability Resources)&reg;
1. Machine learning basé sur des données interopérables
1. Tableau de bord de l’équipe des soins basé sur les données FHIR

## <a name="details"></a>Détails
Cette section décrit plus en détails chaque partie du diagramme de l’architecture.

### <a name="ble-medical-devices"></a>Appareils médicaux BLE
De nombreux appareils médicaux de type wearable utilisés dans l’espace IoT médical sont des appareils BLE (Bluetooth Low Energy), ce qui signifie qu’ils ne peuvent pas communiquer directement avec le cloud et qu’ils doivent passer par une passerelle. Cette architecture suggère d’utiliser une application de téléphone portable pour cette passerelle.

### <a name="mobile-phone-gateway"></a>Passerelle de téléphone portable
La fonction principale de l’application de téléphone portable est d’ingérer les données BLE des appareils médicaux et de les communiquer à Azure IoT Central. En outre, l’application peut guider les patients dans un flux de configuration et de provisionnement des appareils, et les aider à créer une vision de leurs données médicales personnelles. D’autres solutions peuvent utiliser une passerelle de tablette ou une passerelle statique si elles sont mises en place dans une chambre d’hôpital pour implémenter le même circuit de communication.

### <a name="export-to-azure-api-for-fhirreg"></a>Exporter vers l’API Azure pour FHIR&reg;
Azure IoT Central est certifié conforme à HIPAA et à HITRUST&reg;, mais vous pouvez également envoyer des données relatives à la santé des patients à l’API Azure pour FHIR. L’[API Azure pour FHIR](../../healthcare-apis/overview.md) est une API conforme, entièrement managée et basée sur des standards pour les données médicales, qui vous permet de créer des systèmes participatifs avec vos données médicales. Elle permet d’échanger rapidement des données via des API FHIR en s’appuyant sur une offre PaaS dans le cloud. Avec la fonctionnalité d’exportation continue des données d’IoT Central, vous pouvez envoyer des données à l’API Azure pour FHIR.

### <a name="machine-learning"></a>Apprentissage automatique
Une fois que vous avez agrégé vos données et que vous les avez traduites au format FHIR, vous pouvez créer des modèles Machine Learning qui peuvent enrichir des insights et permettre une prise de décision plus intelligente pour votre équipe de soins. Il existe un large éventail de services qui peuvent être utilisés pour créer, entraîner et déployer des modèles Machine Learning. Pour plus d’informations sur l’utilisation des offres de Machine Learning d’Azure, consultez notre [documentation Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Tableau de bord de fournisseur
Les données qui se trouvent dans l’API Azure pour FHIR peuvent être utilisées pour créer un tableau de bord d’insights sur les patients ou être intégrées directement dans EMR pour aider les équipes de soins à visualiser l’état des patients. Les équipes de soins peuvent utiliser ce tableau de bord afin d’entreprendre des actions pour les patients ayant besoin d’assistance et pour repérer les premiers signes d’avertissement d’une détérioration. Pour découvrir comment créer un tableau de bord de fournisseur en temps réel Power BI, suivez notre [guide de procédure](howto-health-data-triage.md).

## <a name="next-steps"></a>Étapes suivantes
* [Découvrir comment déployer un modèle d’application de surveillance continue des patients](tutorial-continuous-patient-monitoring.md)