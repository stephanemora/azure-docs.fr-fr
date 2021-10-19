---
title: projets de GitHub associés pour le connecteur IoT-api de santé Azure
description: répertorier tous les référentiels open source (GitHub) pour le connecteur IoT
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: jasteppe
ms.openlocfilehash: 0092482af2f9f4f6f9a090a00cc387ae901e5b68
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187581"
---
# <a name="open-source-projects"></a>Projets open source

consultez nos projets open source sur GitHub qui fournissent du code source et des instructions pour déployer des services pour diverses utilisations avec IoT connector. 

## <a name="iot-connector-github-projects"></a>projets de GitHub du connecteur IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Intégration à IoT Hub et IoT Central

* [Microsoft/IoMT-fhir](https://github.com/microsoft/iomt-fhir): intégration avec IoT Hub ou IOT central aux ressources de l’interopérabilité de la santé rapide (fhir&#174;) avec la normalisation des données et la conversion fhir des données normalisées
* Normalisation : les informations de données de l’appareil sont extraites dans un format commun pour un traitement ultérieur
* Conversion FHIR : les données normalisées et regroupées sont mappées à FHIR. Les observations sont créées ou mises à jour en fonction des modèles configurés et liés à l’appareil et au patient.

#### <a name="device-and-fhir-destination-mappings-authoring-and-troubleshooting"></a>Création et résolution des mappages de destinations appareil et FHIR

* [Outils pour vous aider à générer des mappages de destination d’appareil et de FHIR](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualiser la configuration de mappage pour normaliser les données d’entrée de l’appareil et les transformer en ressources FHIR. Les développeurs peuvent utiliser cet outil pour modifier et tester les mappages des appareils et les mappages de destination FHIR. Exportez-les pour le chargement vers IoT Connector dans le Portail Azure.

#### <a name="healthkit-and-fhir-integration"></a>Intégration de HealthKit et de FHIR

* [Microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): bibliothèque SWIFT qui automatise l’exportation des données Apple healthkit vers un service fhir.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment déployer IoT Connector dans le Portail Azure

>[!div class="nextstepaction"]
>[Déployer le service géré de connecteur IoT](deploy-iot-connector-in-azure.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.