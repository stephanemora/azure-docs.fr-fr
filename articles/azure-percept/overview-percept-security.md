---
title: Vue d’ensemble de la sécurité Azure Percept
description: Découvrez la sécurité Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: b5a345139114842c83cb1f11792076efb1461870
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678344"
---
# <a name="azure-percept-security-overview"></a>Vue d’ensemble de la sécurité Azure Percept

Les appareils du DK Azure Percept sont conçus avec une racine matérielle de confiance, c’est-à-dire, la sécurité intégrée sur chaque appareil. Cela permet de protéger les données d’inférence, ainsi que les capteurs qui peuvent collecter des données sensibles à caractère privé comme les caméras et les micros. En outre, cela active l’authentification et l’autorisation des appareils dans les services Azure Percept Studio.

> [!NOTE]
> Le DK Azure Percept est destiné à être utilisé dans les environnements de développement et de test, ainsi que pour les scénarios de preuve de concept.

## <a name="devices"></a>Appareils

### <a name="azure-percept-dk"></a>DK Azure Percept

Le DK Azure Percept comprend un module de plateforme sécurisée (TPM) version 2.0 qui peut être utilisé pour connecter l’appareil de façon sécurisée à Azure Device Provisioning Services. TPM est une norme ISO sectorielle créée par le Trusted Computing Group. Pour en savoir plus sur cette norme, consultez la [spécification TPM 2.0 complète](https://trustedcomputinggroup.org/resource/tpm-library-specification/) ou la spécification ISO/IEC 11889. Pour savoir comment le service DPS peut provisionner des services de façon sécurisée, consultez [Service Azure IoT Hub Device Provisioning - Attestation TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>Système sur module (SoM) Azure Percept

Le système sur module (SoM) du DK Azure Percept avec vision activée, et le SOM d’accessoire Azure Percept Audio comprennent un microcontrôleur permettant de protéger l’accès aux capteurs d’IA incorporés. À chaque démarrage, le microprogramme du microcontrôleur authentifie et autorise l’accélérateur d’IA auprès des services Azure Percept Studio à l’aide de l’architecture DICE. L’architecture DICE divise les démarrages en couches et crée des secrets pour chacune des couches, ainsi qu’une configuration basée sur l’UDS. Si un autre code ou une autre configuration sont démarrés, à quelque endroit de la chaîne que ce soit, les secrets ne seront pas les mêmes. Pour plus d’informations sur l’architecture DICE, consultez les [spécifications du groupe de travail DICE](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Pour configurer l’accès à Azure Percept Studio, ainsi qu’aux services nécessaires, consultez **Configuration des pare-feu pour le DK Azure Percept** ci-dessous.

Les appareils Azure Percept utilisent la racine matérielle de confiance pour sécuriser le microprogramme. La ROM de démarrage garantit l’intégrité des microprogrammes entre la ROM et le chargeur du système d’exploitation, ce qui garantit par là-même l’intégrité des autres composants logiciels, créant ainsi une chaîne de confiance.

## <a name="services"></a>Services

### <a name="iot-edge"></a>IoT Edge

Le DK Azure Percept se connecte de façon sécurisée à Azure Percept Studio, ainsi qu’à d’autres services Azure, à l’aide du protocole TLS. Le DK Azure Percept est un appareil compatible avec Azure IoT Edge. Le runtime IoT Edge est une collection de programmes qui transforme un appareil en appareil IoT Edge. Collectivement, les composants du runtime IoT Edge permettent aux appareils IoT Edge de recevoir du code à exécuter dans l’arête et de communiquer les résultats. Le DK Azure Percept utilise des conteneurs Docker pour isoler les charges de travail IoT Edge du système d’exploitation hôte et des applications compatibles Edge. Pour plus d’informations sur le framework de sécurité Azure IoT Edge, consultez [Gestionnaire de sécurité IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager?view=iotedge-2018-06).

### <a name="device-update-for-iot-hub"></a>Device Update pour IoT Hub

Device Update pour IoT Hub permet une mise à jour OTA sécurisée, scalable et fiable, offrant ainsi une sécurité renouvelable pour les appareils Azure Percept. Elle fournit des contrôles de gestion enrichis, ainsi que la conformité des mises à jour par le biais d’insights. Le DK Azure Percept comprend une solution de mise à jour d’appareil préintégrée qui fournit une mise à jour résiliente (A/B) entre le microprogramme et les couches du système d’exploitation.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configuration des pare-feu pour le DK Azure Percept

Si votre configuration réseau exige l’autorisation explicite des connexions établies à partir d’appareils du DK Azure Percept, consultez la liste suivante de composants IoT Edge.

Cette liste de vérification est un point de départ pour les règles de pare-feu :

|URL (* = caractère générique) |Ports TCP sortants|    Usage|
|-------------------|------------------|---------|
|*.auth.azureperceptdk.azure.net|   443|    Authentification et autorisation Azure AD du SoM Azure DK|
|*.auth.projectsantacruz.azure.net| 443|    Authentification et autorisation Azure AD du SoM Azure DK|

Ensuite, passez en revue la liste des [connexions utilisées par Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist?view=iotedge-2018-06#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Recommandations supplémentaires concernant le déploiement en production

Le DK Azure Percept offre une grande variété de fonctionnalités de sécurité prêtes à l’emploi. Outre les puissantes fonctionnalités de sécurité qui sont incluses dans la version actuelle, Microsoft suggère également de suivre les instructions ci-dessous si vous prévoyez des déploiements de production :

- Protection physique renforcée de l’appareil
- Activation du chiffrement des données au repos
- Supervision continue de la posture des appareils et réponse rapide aux alertes
- Limitation du nombre d’administrateurs ayant accès à l’appareil

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [modèles d’IA Azure Percept](./overview-ai-models.md) disponibles.