---
title: Résolution de l’erreur 400027 ConnectionForcefullyClosedOnNewConnection dans Azure IoT Hub
description: Comprendre comment corriger l’erreur 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960210"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Cet article décrit les causes et solutions des erreurs **400027 ConnectionForcefullyClosedOnNewConnection**.

## <a name="symptoms"></a>Symptômes

Votre opération sur le jumeau appareil-à-cloud (par exemple, lecture ou correction de propriétés signalées) ou votre appel de méthode directe échouent avec le code d’erreur **400027**.

## <a name="cause"></a>Cause :

Un autre client a créé une connexion à IoT Hub à l’aide des mêmes informations d’identification. L’IoT Hub a donc fermé la connexion précédente. L’IoT Hub ne permet pas à plusieurs clients de se connecter à l’aide du même ensemble d’informations d’identification.

## <a name="solution"></a>Solution

Assurez-vous que chaque client se connecte à l’IoT Hub à l’aide de sa propre identité.