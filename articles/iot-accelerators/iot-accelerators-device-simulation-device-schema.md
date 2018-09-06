---
title: Déployer un schéma dans la solution de simulation d’appareil – Azure | Microsoft Docs
description: Cet article décrit le schéma JSON qui définit un appareil simulé dans la solution de simulation d’appareil.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338153"
---
# <a name="understand-the-device-model-schema"></a>Comprendre le schéma de modèle d’appareil

Vous pouvez utiliser l’accélérateur de solution de simulation d’appareil afin de générer des charges de test pour les solutions qui utilisent IoT Hub. Une collection d’appareils simulés est automatiquement approvisionnée lors du déploiement de la solution de simulation d’appareil. Vous pouvez personnaliser les appareils simulés existants ou créer les vôtres.

Cet article décrit le schéma de modèle d’appareil qui spécifie les fonctionnalités et le comportement d’un appareil simulé. Le modèle d’appareil est stocké dans un fichier JSON.

Les articles suivants sont associées à l’article en cours :

* [Implémenter le comportement de modèle d’appareil](iot-accelerators-device-simulation-device-behavior.md) décrit les fichiers JavaScript que vous utilisez pour implémenter le comportement d’un appareil simulé.
* [Créer un appareil simulé](iot-accelerators-device-simulation-create-simulated-device.md) réunit toutes les instructions et vous montre comment déployer un nouveau type d’appareil simulé dans votre solution.

Dans cet article, vous apprendrez comment :

>[!div class="checklist"]
> * Utiliser un fichier JSON pour définir un modèle d’appareil simulé
> * Spécifier les propriétés de l’appareil simulé
> * Spécifier les données de télémétrie que l’appareil simulé envoie
> * Spécifier les méthodes cloud-à-appareil auxquelles l’appareil répond

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a décrit comment créer votre propre modèle personnalisé d’appareil simulé. Cet article vous a montré comment :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Utiliser un fichier JSON pour définir un modèle d’appareil simulé
> * Spécifier les propriétés de l’appareil simulé
> * Spécifier les données de télémétrie que l’appareil simulé envoie
> * Spécifier les méthodes cloud-à-appareil auxquelles l’appareil répond

Maintenant que vous avez découvert le schéma JSON, nous vous recommandons d’apprendre à [implémenter le comportement de votre appareil simulé](iot-accelerators-device-simulation-device-behavior.md).

Pour plus d’informations à l’intention des développeurs sur la solution de simulation d’appareil, voir le [Guide de référence des développeurs](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
