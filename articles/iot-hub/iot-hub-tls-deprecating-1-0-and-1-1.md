---
title: Dépréciation de TLS 1.0 et 1.1 dans IoT Hub et le service de provisionnement des appareils (Microsoft System Center Data Protection Manager) | Microsoft Docs
description: Instructions relatives à la dépréciation de TLS 1.0 et 1.1 et aux chiffrements pris en charge dans IoT Hub et DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402793"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Dépréciation de TLS 1.0 et 1.1 dans IoT Hub et le service de provisionnement des appareils

Pour offrir un chiffrement optimal, IoT Hub et le Service Device Provisioning (DPS) passent au mécanisme de chiffrement privilégié TLS (Transport Layer Security) 1.2 pour les appareils et services IoT. 

## <a name="supported-ciphers"></a>Chiffrements pris en charge

La chronologie de la disponibilité des différents chiffrements utilisés dans la négociation TLS est la suivante :

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (actuellement pris en charge)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (sera pris en charge dans la seconde moitié de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (sera pris en charge dans la seconde moitié de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (sera pris en charge dans la seconde moitié de 2020)


## <a name="customer-feedback"></a>Feedback des clients

Même si la mise en œuvre de TLS 1.2 est un choix de chiffrement optimal à l’échelle industrielle et sera effectuée comme prévu, nous aimerions entendre l’avis des clients concernant leurs déploiements spécifiques et les difficultés d’adoption de TLS 1.2. Vous pouvez pour cela envoyer vos commentaires à l’adresse [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
