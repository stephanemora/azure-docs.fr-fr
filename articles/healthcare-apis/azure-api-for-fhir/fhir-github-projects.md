---
title: projets de GitHub associés pour l’API Azure pour FHIR
description: répertorie tous les référentiels Open Source (GitHub) pour l’API Azure pour FHIR.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122434349"
---
# <a name="related-github-projects"></a>projets de GitHub associés

nous disposons de nombreux projets open source sur GitHub qui vous fournissent le code source et des instructions pour déployer des services pour différentes utilisations. vous êtes toujours invité à visiter nos référentiels GitHub pour apprendre et expérimenter nos produits et fonctionnalités. 

## <a name="fhir-server"></a>Serveur FHIR
* [Microsoft/fhir-Server](https://github.com/microsoft/fhir-server/): serveur fhir Open source, qui est la base de l’API Azure pour fhir
* Pour afficher les dernières versions, reportez-vous aux [notes de publication](https://github.com/microsoft/fhir-server/releases)
* [Microsoft/fhir-Server-Samples](https://github.com/microsoft/fhir-server-samples): exemple d’environnement

## <a name="data-conversion--anonymization"></a>Conversion de données & anonymisation

#### <a name="fhir-converter"></a>Convertisseur FHIR
* [Microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): utilitaire de conversion pour convertir des formats de données hérités en FHIR
* Intégration avec l’API Azure pour FHIR et FHIR Server pour Azure sous la forme d’une opération de $convert de données
* Améliorations continues dans les OSS et intégration continue aux serveurs FHIR
 
#### <a name="fhir-converter---vs-code-extension"></a>convertisseur FHIR-Extension VS Code
* [Microsoft/FHIR-Tools-for-anonymisation](https://github.com/microsoft/FHIR-Tools-for-Anonymization): un ensemble d’outils permettant d’aider à l’anonymisation de données (au format FHIR)
* Intégration avec l’API Azure pour FHIR et FHIR Server pour Azure sous la forme d' « exportation identifiée »

#### <a name="fhir-tools-for-anonymization"></a>Outils FHIR pour l’anonymisation
* [microsoft/vscode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): extension VS Code qui contient une collection d’outils à utiliser avec les api de santé Azure
* publié sur Visual Studio Marketplace
* Utilisé pour la création de modèles de liquide à utiliser dans le convertisseur FHIR

## <a name="iot-connector"></a>Connecteur IoT

#### <a name="integration-with-iot-hub-and-iot-central"></a>Intégration à IoT Hub et IoT Central
* [Microsoft/IoMT-fhir](https://github.com/microsoft/iomt-fhir): intégration avec IoT Hub ou IOT central à fhir avec la normalisation des données et la conversion fhir des données normalisées
* Normalisation : les informations de données de l’appareil sont extraites dans un format commun pour un traitement ultérieur
* Conversion FHIR : les données normalisées et regroupées sont mappées à FHIR. Les observations sont créées ou mises à jour en fonction des modèles configurés et liés à l’appareil et au patient.
* [Outils pour vous aider à créer la carte de conversation](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): visualiser la configuration de mappage pour normaliser les données d’entrée de l’appareil et les transformer en ressources FHIR. les développeurs peuvent utiliser cet outil pour modifier et tester les mappages, le mappage des appareils et le mappage FHIR, et les exporter pour le téléchargement vers le IoT Connector dans le Portail Azure.

#### <a name="healthkit-and-fhir-integration"></a>Intégration de HealthKit et de FHIR
* [Microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): bibliothèque SWIFT qui automatise l’exportation des données Apple healthkit vers un serveur fhir

 