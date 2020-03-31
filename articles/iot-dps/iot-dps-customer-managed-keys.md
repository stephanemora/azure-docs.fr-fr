---
title: Chiffrement de données du Service Azure Device Provisioning au repos à l’aide de clés gérées par le client | Microsoft Docs
description: Chiffrement de données au repos avec des clés gérées par le client pour le Service Device Provisioning
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77674783"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Chiffrement de données au repos avec des clés gérées par le client pour le Service Device Provisioning

## <a name="overview"></a>Vue d’ensemble

Le Service Device Provisioning (DPS) prend en charge le chiffrement des données au repos avec des clés gérées par le client (CMK). Cette méthode est également appelée BYOK (Bring Your Own Key, ou apportez votre propre clé). Le service DPS assure le chiffrement des données au repos et en transit. Par défaut, il utilise des clés gérées par Microsoft pour chiffrer les données. Avec la prise en charge de CMK, les clients peuvent désormais chiffrer les données au repos avec une clé de chiffrement, gérée par les clients, à l’aide d'[Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Cette fonctionnalité requiert la création d’un service DPS dans l’une des régions suivantes : USA Est, USA Ouest 2 ou USA Centre Sud. Pour essayer cette fonctionnalité, contactez-nous par le biais du [support technique Microsoft](https://azure.microsoft.com/support/create-ticket/). Partagez le nom de votre société et votre ID d’abonnement pour contacter le support Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur le service Device Provisioning](https://docs.microsoft.com/azure/iot-dps/)

* [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)