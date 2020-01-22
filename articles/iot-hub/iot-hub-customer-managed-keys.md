---
title: Chiffrement de données Azure IoT Hub au repos par le biais de clés gérées par le client | Microsoft Docs
description: Chiffrement de données au repos avec des clés gérées par le client pour IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 1bb55d593878026bb3e57014a317b4fc0158d734
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913180"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Chiffrement de données au repos avec des clés gérées par le client pour IoT Hub

IoT Hub prend en charge le chiffrement des données au repos avec des clés gérées par le client (CMK), également appelé Apporter votre propre clé (BYOK), pour Azure IoT Hub. Azure IoT Hub assure le chiffrement des données au repos et en transit. Par défaut, IoT Hub utilise des clés gérées par Microsoft pour chiffrer les données. Avec la prise en charge de CMK, les clients peuvent désormais chiffrer les données au repos avec une clé de chiffrement, gérée par les clients, à l’aide d'[Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Cette fonctionnalité requiert la création d’un nouveau IoT Hub (niveau de base ou standard) dans l’une des régions suivantes : USA Est, USA Ouest 2 ou USA Centre Sud. Pour essayer cette fonctionnalité, contactez-nous par le biais du [support technique Microsoft](https://azure.microsoft.com/support/create-ticket/). Partagez le nom de votre société et votre ID d’abonnement pour contacter le support Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)