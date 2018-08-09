---
title: Comportement d’appareil simulé dans la solution de simulation d’appareil – Azure | Microsoft Docs
description: Cet article explique comment utiliser JavaScript pour définir le comportement d’un appareil simulé dans la solution de simulation d’appareil.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b68550bce1f4e3fbe3845c21598720083c8e384c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295205"
---
# <a name="implement-the-device-model-behavior"></a>Implémenter le comportement de modèle d’appareil

L’article [Comprendre le schéma de modèle d’appareil](iot-accelerators-device-simulation-device-schema.md) décrit le schéma qui définit un modèle d’appareil simulé. Cet article s’appuie sur deux types de fichiers JavaScript qui implémentent le comportement d’un appareil simulé :

- **État** : fichiers JavaScript qui s’exécutent à intervalles réguliers pour mettre à jour l’état interne de l’appareil.
- **Méthode** : fichiers JavaScript qui s’exécutent lorsque la solution appelle une méthode sur l’appareil.

Dans cet article, vous apprendrez comment :

>[!div class="checklist"]
> * Contrôler l’état d’un appareil simulé
> * Définir comment un appareil simulé doit répondre à un appel de méthode provenant du hub IoT auquel il est connecté
> * Déboguer vos scripts

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a décrit comment définir le comportement de votre propre modèle personnalisé d’appareil simulé. Cet article vous a montré comment :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Contrôler l’état d’un appareil simulé
> * Définir comment un appareil simulé doit répondre à un appel de méthode provenant du hub IoT auquel il est connecté
> * Déboguer vos scripts

Maintenant que vous avez découvert comment spécifier le comportement d’un appareil simulé, nous vous recommandons d’apprendre à [Créer un appareil simulé](iot-accelerators-remote-monitoring-test.md).

Pour plus d’informations à l’intention des développeurs sur la solution de simulation d’appareil, voir le [Guide de référence des développeurs](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
