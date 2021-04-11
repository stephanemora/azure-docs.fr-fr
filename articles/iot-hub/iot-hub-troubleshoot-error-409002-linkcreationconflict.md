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
ms.openlocfilehash: c354eed6e7bf9f2147f8b81a82a06e80a9337c84
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061041"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Cet article décrit les causes et solutions des erreurs **409002 LinkCreationConflict**.

## <a name="symptoms"></a>Symptômes

L'erreur **409002 LinkCreationConflict** apparaît dans les journaux, avec la déconnexion de l'appareil ou l'échec de message cloud-à-appareil.

<!-- When using AMQP? -->

## <a name="cause"></a>Cause :

En règle générale, cette erreur se produit quand IoT Hub détecte qu’un client a plusieurs connexions. En fait, quand une nouvelle demande de connexion arrive pour un appareil avec une connexion existante, IoT Hub ferme la connexion existante en générant cette erreur.

### <a name="cause-1"></a>Cause 1

Le plus souvent, un problème distinct (par exemple, [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) entraîne la déconnexion de l’appareil. L’appareil tente de rétablir la connexion immédiatement, mais IoT Hub continue de considérer que l’appareil est connecté. IoT Hub ferme la connexion précédente et journalise cette erreur.

### <a name="cause-2"></a>Cause 2

Une logique erronée côté appareil force celui-ci à établir la connexion quand une connexion est déjà ouverte.

## <a name="solution"></a>Solution

Cette erreur étant généralement l’effet secondaire d’un autre problème temporaire, recherchez d’autres erreurs dans les journaux d’activité pour résoudre le problème. Sinon, veillez à émettre une nouvelle demande de connexion uniquement en cas d’abandon de la connexion précédente.
