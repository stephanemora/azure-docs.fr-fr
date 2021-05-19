---
title: Contrôle d’accès et sécurité pour IoT Hub | Microsoft Docs
description: Vue d’ensemble de la façon de contrôler l’accès à IoT Hub, contient des liens vers des articles détaillés sur l’intégration d’AAD et les options SAS.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 617a68a36e0f4673ed89753d1e48f529725d1648
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109484178"
---
# <a name="control-access-to-iot-hub"></a>Contrôler l’accès à IoT Hub

Cet article décrit les options de sécurisation de votre hub IoT. IoT Hub utilise des *autorisations* pour accorder l’accès à chaque point de terminaison IoT Hub. Les autorisations limitent l’accès à un hub IoT selon la fonctionnalité.

Il existe trois façons différentes de contrôler l’accès à IoT Hub :

- **Intégration d’Azure Active Directory (Azure AD)** pour les API de service. Azure fournit une authentification basée sur les identités avec AAD et une autorisation fine avec le contrôle d’accès en fonction du rôle Azure (Azure RBAC). L’intégration d’Azure AD et RBAC est prise en charge uniquement pour les API de service IoT Hub. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub à l’aide d’Azure Active Directory](iot-hub-dev-guide-azure-ad-rbac.md).
- Les **signatures d’accès partagé** vous permettent de grouper des autorisations et de les accorder aux applications à l’aide de clés d’accès et de jetons de sécurité signés. Pour plus d’informations, consultez [Contrôler l’accès à IoT Hub à l’aide de la signature d’accès partagé](iot-hub-dev-guide-sas.md). 
- **Informations d’identification de sécurité par appareil**. Chaque IoT Hub contient un [registre des identités](iot-hub-devguide-identity-registry.md). Pour chaque appareil dans ce registre des identités, vous pouvez configurer des informations d’identification de sécurité qui accordent des autorisations DeviceConnect incluses dans l’étendue des points de terminaison de ces appareils. Pour plus d’informations, consultez [Authentification d’un appareil pour IoT Hub](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub).

## <a name="next-steps"></a>Étapes suivantes

- [Contrôler l’accès à IoT Hub à l’aide d’Azure Active Directory](iot-hub-dev-guide-azure-ad-rbac.md)
- [Contrôler l’accès à IoT Hub à l’aide de la signature d’accès partagé](iot-hub-dev-guide-sas.md)
- [Authentifier un appareil avec IoT Hub](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)
