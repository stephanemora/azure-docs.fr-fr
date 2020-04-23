---
title: Dépréciation de TLS 1.0 et 1.1 dans IoT Hub | Microsoft Docs
description: Instructions relatives à la dépréciation de TLS 1.0 et 1.1 et aux chiffrements pris en charge dans IoT Hub.
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381294"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>Dépréciation de TLS 1.0 et 1.1 dans IoT Hub

Pour offrir un chiffrement optimal, IoT Hub passe au mécanisme de chiffrement privilégié TLS (Transport Layer Security) 1.2 pour les appareils et services IoT. 

## <a name="timeline"></a>Chronologie

IoT Hub continuera à prendre en charge TLS 1.0/1.1 jusqu’à nouvel ordre. Toutefois, nous recommandons à tous les clients de migrer vers TLS 1.2 dès que possible.

## <a name="supported-ciphers"></a>Chiffrements pris en charge

La chronologie de la disponibilité des différents chiffrements utilisés dans la négociation TLS est la suivante :

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (actuellement pris en charge)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (sera pris en charge dans la seconde moitié de 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (sera pris en charge dans la seconde moitié de 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (sera pris en charge dans la seconde moitié de 2020)

## <a name="customer-feedback"></a>Feedback des clients

Même si la mise en œuvre de TLS 1.2 est un choix de chiffrement optimal à l’échelle industrielle et sera effectuée comme prévu, nous aimerions entendre l’avis des clients concernant leurs déploiements spécifiques et les difficultés d’adoption de TLS 1.2. Vous pouvez pour cela envoyer vos commentaires à l’adresse [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
