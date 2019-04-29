---
title: Schéma d’appareil dans la solution de supervision à distance - Azure | Microsoft Docs
description: Cet article décrit le schéma JSON qui définit un appareil simulé dans la solution de supervision à distance.
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 3c2bca45a7927689992cf44fb70199e246641760
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446101"
---
# <a name="understand-the-device-model-schema"></a>Comprendre le schéma de modèle d’appareil

Vous pouvez utiliser des appareils simulés dans la solution de supervision à distance pour en tester le comportement. La solution de supervision à distance inclut un service de simulation d’appareil pour exécuter des appareils simulés. Quand vous déployez la solution de supervision à distance, une collection d’appareils simulés est provisionnée automatiquement. Vous pouvez personnaliser les appareils simulés existants ou créer les vôtres.

Cet article décrit le schéma de modèle d’appareil qui spécifie les fonctionnalités et le comportement d’un appareil simulé. Le modèle d’appareil est stocké dans un fichier JSON.

> [!NOTE]
> Ce schéma de modèle d’appareil est destiné uniquement aux appareils simulés hébergés dans le service de simulation d’appareil. Si vous voulez créer un appareil réel, consultez [Connecter votre appareil à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).

Les articles suivants sont associées à l’article en cours :

* [Implémenter le comportement de modèle d’appareil](iot-accelerators-remote-monitoring-device-behavior.md) décrit les fichiers JavaScript que vous utilisez pour implémenter le comportement d’un appareil simulé.
* [Créer un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md) réunit toutes les instructions et vous montre comment déployer un nouveau type d’appareil simulé dans votre solution.

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

Maintenant que vous avez découvert le schéma JSON, nous vous recommandons d’apprendre à [implémenter le comportement de votre appareil simulé](iot-accelerators-remote-monitoring-device-behavior.md).

Pour plus d’informations sur le développement de la solution de supervision à distance, consultez :

* [Guide d’informations de référence pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guide de résolution des problèmes pour les développeurs](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
