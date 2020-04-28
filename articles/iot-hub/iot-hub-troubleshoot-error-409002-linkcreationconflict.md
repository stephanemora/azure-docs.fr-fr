---
title: Résolution de l’erreur Azure IoT Hub 409002 LinkCreationConflict
description: Comprendre comment corriger l’erreur 409002 LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758742"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Cet article décrit les causes et solutions des erreurs **409002 LinkCreationConflict**.

## <a name="symptoms"></a>Symptômes

L’erreur **409002 LinkCreationConflict** est consignée dans les journaux de diagnostic, ainsi que la déconnexion d’appareil ou l’échec de message cloud-à-appareil. 

<!-- When using AMQP? -->

## <a name="cause"></a>Cause :

En règle générale, cette erreur se produit quand IoT Hub détecte qu’un client a plusieurs connexions. En fait, quand une nouvelle demande de connexion arrive pour un appareil avec une connexion existante, IoT Hub ferme la connexion existante en générant cette erreur.

### <a name="cause-1"></a>Cause 1

Le plus souvent, un problème distinct (par exemple, [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) entraîne la déconnexion de l’appareil. L’appareil tente de rétablir la connexion immédiatement, mais IoT Hub continue de considérer que l’appareil est connecté. IoT Hub ferme la connexion précédente et journalise cette erreur.

### <a name="cause-2"></a>Cause 2

Une logique erronée côté appareil force celui-ci à établir la connexion quand une connexion est déjà ouverte.

## <a name="solution"></a>Solution

Cette erreur étant généralement l’effet secondaire d’un autre problème temporaire, recherchez d’autres erreurs dans les journaux d’activité pour résoudre le problème. Sinon, veillez à émettre une nouvelle demande de connexion uniquement en cas d’abandon de la connexion précédente.
