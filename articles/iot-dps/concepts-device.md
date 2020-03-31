---
title: Concepts d’appareil dans Azure Device Provisioning | Microsoft Docs
description: Décrit les concepts de provisionnement des appareils spécifiques aux appareils dotés du service Device Provisioning (DPS) et IoT Hub
author: nberdy
ms.author: nberdy
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f5f931622f793a1146c04403e8c5e1a5ef7a7d62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79237525"
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>Concepts d’appareil du service IoT Hub Device Provisioning

Le service IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui vous permet de configurer l’approvisionnement sans contact des appareils dans un hub IoT spécifié. Avec le service Device Provisioning, vous pouvez approvisionner des millions d’appareils de manière sécurisée et scalable.

Cet article donne une vue d’ensemble des concepts d’*appareil* impliqués dans l’approvisionnement d’appareils. Cet article concerne particulièrement les personnes impliquées dans l’[étape de fabrication](about-iot-dps.md#manufacturing-step) qui permet de préparer un appareil pour le déploiement.

## <a name="attestation-mechanism"></a>Mécanisme d’attestation

Le mécanisme d’attestation est la méthode utilisée pour confirmer l’identité d’un appareil. Le mécanisme d’attestation concerne également la liste d’inscriptions, qui indique au service d’approvisionnement la méthode d’attestation à utiliser avec un appareil donné.

> [!NOTE]
> IoT Hub utilise un « schéma d’authentification » pour un concept semblable dans ce service.

Le service Device Provisioning prend en charge les formes d’attestation suivantes :
* **Certificats X.509** basés sur le flux d’authentification de certificat X.509 standard.
* **Module de plateforme sécurisée (TPM)** basé sur un défi nonce, utilisant la norme de module de plateforme sécurisée (TPM) pour les clés afin de présenter un jeton de signature d’accès partagé (SAS) signé. Il n’est pas nécessaire d’avoir un TPM physique sur l’appareil, mais le service utilise pour l’attestation la paire de clés de type EK conformément à la [spécification TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).
* **Clé symétrique** basée sur des [jetons de sécurité](../iot-hub/iot-hub-devguide-security.md#security-tokens) avec signature d’accès partagé (SAP), qui incluent une signature hachée et un délai d’expiration incorporé. Pour plus d’informations, consultez [Attestation de clé symétrique](concepts-symmetric-key-attestation.md).

## <a name="hardware-security-module"></a>Module de sécurité matériel

Le module de sécurité matériel, ou module HSM, est utilisé pour le stockage matériel sécurisé des secrets de l’appareil. C’est la forme de stockage de secrets la plus sécurisée. Les certificats X.509 et les jetons SAP peuvent être stockés dans le module HSM. Les modules HSM peuvent être utilisés avec les deux mécanismes d’attestation que le service d’approvisionnement prend en charge.

> [!TIP]
> Nous vous recommandons vivement d’utiliser un module HSM avec les appareils pour stocker en toute sécurité des secrets sur vos appareils.

Les secrets de l’appareil peuvent également être stockés sous forme logicielle (mémoire), mais cette forme de stockage est moins sécurisée que le module HSM.

## <a name="registration-id"></a>ID d’enregistrement

L’ID d’enregistrement est utilisé pour identifier de manière unique un appareil dans le service Device Provisioning. L’ID d’appareil doit être unique dans l’[étendue de l’ID](#id-scope) du service d’approvisionnement. Chaque appareil doit avoir un ID d’enregistrement. L’ID d’inscription est insensible à la casse ; il peut comporter des caractères alphanumériques et des caractères spéciaux (deux points, point, trait de soulignement et trait d’union).

* Dans le cas d’un module TPM, l’ID d’enregistrement est fourni par le TPM lui-même.
* Dans le cas d’une attestation X.509, l’ID d’enregistrement est le nom de sujet du certificat.

## <a name="device-id"></a>ID de périphérique

L’ID d’appareil est l’ID tel qu’il apparaît dans IoT Hub. L’ID d’appareil de votre choix peut être défini dans l’entrée d’inscription, mais ce n’est pas obligatoire. Il n’est possible de définir l’ID d’appareil souhaité que dans les inscriptions individuelles. Si aucun ID d’appareil souhaité n’est spécifié dans la liste d’inscriptions, l’ID d’inscription est utilisé comme ID d’appareil durant l’enregistrement de l’appareil. Découvrez plus d’informations sur les [ID d’appareil dans IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).

## <a name="id-scope"></a>Étendue de l’ID

L’étendue de l’ID est affectée à un service Device Provisioning quand celui-ci est créé par l’utilisateur et utilisé pour identifier de façon unique le service d’approvisionnement spécifique qui permet d’enregistrer l’appareil. L’étendue de l’ID est générée par le service et est immuable, ce qui garantit l’unicité.

> [!NOTE]
> L’unicité est importante pour les opérations de déploiement longues et les scénarios de fusion et acquisition.
