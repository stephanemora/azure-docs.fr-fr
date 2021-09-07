---
title: Chiffrement de données Azure IoT Hub au repos à l’aide de clés gérées par le client | Microsoft Docs
description: Chiffrement de données Azure IoT Hub au repos à l’aide de clés gérées par le client
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: asrastog
ms.openlocfilehash: 4dd3ee01504c2777ad72e32e11932b3b63d07448
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515549"
---
# <a name="encryption-of-azure-iot-hub-data-at-rest-using-customer-managed-keys"></a>Chiffrement de données Azure IoT Hub au repos à l’aide de clés gérées par le client

IoT Hub prend en charge le chiffrement des données au repos avec des clés gérées par le client (CMK), fonctionnalité également appelée BYOK (Apporter votre propre clé). Azure IoT Hub assure le chiffrement des données au repos et en transit à mesure qu’elles sont écrites dans nos centres de données. Les données sont chiffrées lors de leur lecture et déchiffrées lors de leur écriture. 

Par défaut, IoT Hub utilise des clés gérées par Microsoft pour chiffrer les données. Avec CMK, vous pouvez obtenir une autre couche de chiffrement en plus du chiffrement par défaut, ainsi que choisir de chiffrer les données au repos avec une clé de chiffrement à clé gérée via votre [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Cela vous donne la flexibilité nécessaire pour la création, la rotation, la désactivation et la révocation des contrôles d’accès. Si le service BYOK est configuré pour votre hub IoT, nous assurons également un double chiffrement, ce qui ajoute une deuxième couche de protection, tout en vous permettant encore de contrôler la clé de chiffrement par le biais de votre coffre de clés Azure.

Cette fonctionnalité requiert la création d’un nouveau IoT Hub (niveau de base ou standard). Pour essayer cette fonctionnalité, contactez-nous par le biais du [support technique Microsoft](https://azure.microsoft.com/support/create-ticket/). Partagez le nom de votre société et votre ID d’abonnement pour contacter le support Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’IoT Hub](./about-iot-hub.md)

* [En savoir plus sur Azure Key Vault](../key-vault/general/overview.md)
