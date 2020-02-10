---
title: Résolution de l’erreur Azure IoT Hub 504101 GatewayTimeout
description: Comprendre comment corriger l’erreur 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960254"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Cet article décrit les causes et solutions des erreurs **504101 GatewayTimeout**.

## <a name="symptoms"></a>Symptômes

Lorsque vous tentez d’appeler une méthode directe à partir de l’IoT Hub sur un appareil, la demande échoue en générant l’erreur **504101 GatewayTimeout**.

## <a name="cause"></a>Cause :

### <a name="cause-1"></a>Cause 1

L’IoT Hub a rencontré une erreur et n’a pas pu vérifier que l’exécution de la méthode directe s’était terminée avant l’expiration du délai d’attente.

### <a name="cause-2"></a>Cause 2

Lors de l’utilisation d’une version antérieure du Kit de développement logiciel (SDK) C# Azure IoT (< 1.19.0), le lien AMQP entre l’appareil et IoT Hub peut être abandonné en mode silencieux en raison d’un bogue.

## <a name="solution"></a>Solution

### <a name="solution-1"></a>Solution 1

Faites une nouvelle tentative.

### <a name="solution-2"></a>Solution 2

Opérez une mise à niveau vers la dernière version du Kit de développement logiciel (SDK) C# Azure IoT.