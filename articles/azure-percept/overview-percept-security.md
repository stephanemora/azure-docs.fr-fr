---
title: Vue d’ensemble de la sécurité Azure Percept
description: Découvrez la sécurité Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567642"
---
# <a name="azure-percept-security-overview"></a>Vue d’ensemble de la sécurité Azure Percept

Les appareils Azure Percept sont conçus avec une racine matérielle de confiance. Cette sécurité intégrée permet de protéger les données d’inférence et les capteurs qui peuvent collecter des données sensibles à caractère privé comme les caméras et les micros. En outre, elle active l’authentification et l’autorisation des appareils pour les services Azure Percept Studio.

> [!NOTE]
> La licence Azure Percept DK couvre l’utilisation dans les environnements de développement et de test uniquement.

## <a name="devices"></a>Appareils

### <a name="azure-percept-dk"></a>DK Azure Percept

Azure Percept DK comprend un Module de plateforme sécurisée (TPM) version 2.0 qui peut être utilisé pour connecter l’appareil à Azure Device Provisioning Services (DPS) de manière plus sécurisée. Le TPM est une norme ISO du Trusted Computing Group, applicable à l’ensemble du secteur. Consultez le [site web du Trusted Computing Group](https://trustedcomputinggroup.org/resource/tpm-library-specification/) pour plus d’informations sur la spécification TPM 2.0 complète ou la spécification ISO/CEI 11889. Pour plus d’informations sur la façon dont DPS peut approvisionner les appareils de manière sécurisée, consultez [Service Azure IoT Hub Device Provisioning – Attestation TPM](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Systèmes sur modules (SoM) Azure Percept

Le système sur module (SoM) Azure Percept Vision et le SoM Azure Percept Audio comprennent tous deux un microcontrôleur (MCU) permettant de protéger l’accès aux capteurs d’IA incorporés. À chaque démarrage, le microprogramme du microcontrôleur authentifie et autorise l’accélérateur d’IA auprès des services Azure Percept Studio à l’aide de l’architecture DICE. L’architecture DICE divise les démarrages en couches et crée des secrets d’appareil uniques (UDS) pour chaque couche et chaque configuration. Si un autre code ou une autre configuration sont démarrés, à quelque endroit de la chaîne que ce soit, les secrets ne seront pas les mêmes. Pour plus d’informations sur l’architecture DICE, consultez les [spécifications du groupe de travail DICE](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Pour configurer l’accès à Azure Percept Studio, ainsi qu’aux services nécessaires, consultez l’article sur la [configuration des pare-feu pour Azure Percept DK](concept-security-configuration.md).

Les appareils Azure Percept utilisent la racine matérielle de confiance pour sécuriser le microprogramme. La ROM de démarrage garantit l’intégrité du microprogramme entre la ROM et le chargeur du système d’exploitation, ce qui garantit par là-même l’intégrité des autres composants logiciels, créant ainsi une chaîne de confiance.

## <a name="services"></a>Services

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK se connecte à Azure Percept Studio de manière plus sécurisée, ainsi qu’à d’autres services Azure qui exploitent le protocole TLS (Transport Layer Security). Azure Percept DK est un appareil compatible avec Azure IoT Edge. Le runtime IoT Edge est une collection de programmes qui transforme un appareil en appareil IoT Edge. Collectivement, les composants du runtime IoT Edge permettent aux appareils IoT Edge de recevoir du code à exécuter dans l’arête et de communiquer les résultats. Azure Percept DK utilise des conteneurs Docker pour isoler les charges de travail IoT Edge du système d’exploitation hôte et des applications compatibles Edge. Pour plus d’informations sur le framework de sécurité Azure IoT Edge, consultez [Gestionnaire de sécurité IoT Edge](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Device Update pour IoT Hub

Device Update pour IoT Hub permet une mise à jour OTA plus sécurisée, scalable et fiable, offrant ainsi une sécurité renouvelable pour les appareils Azure Percept. Elle fournit des contrôles de gestion enrichis, ainsi que la conformité des mises à jour par le biais d’insights. Le DK Azure Percept comprend une solution de mise à jour d’appareil préintégrée qui fournit une mise à jour résiliente (A/B) entre le microprogramme et les couches du système d’exploitation.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les recommandations relatives à la configuration et à la sécurité du pare-feu](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Acheter un kit Azure Percept DK sur la boutique en ligne de Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
