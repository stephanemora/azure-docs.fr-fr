---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402666"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

Dans cet article, vous créez un service principal qui planifie une tâche pour appeler une méthode directe sur un appareil, planifie une tâche pour mettre à jour le jumeau d'appareil, et surveille l'avancement de chaque tâche. Pour effectuer ces opérations, votre service requiert les autorisations de **lecture du registre** et d’**écriture du registre**. Par défaut, chaque IoT Hub est créé avec une stratégie d’accès partagé nommée **registryReadWrite** qui accorde ces autorisations.
