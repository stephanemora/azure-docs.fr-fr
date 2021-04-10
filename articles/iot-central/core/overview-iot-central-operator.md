---
title: Guide de l’opérateur Azure IoT Central
description: Azure IoT Central est une plateforme d’applications IoT qui simplifie la création de solutions IoT. Cet article propose une vue d’ensemble du rôle d’opérateur dans IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669936"
---
# <a name="iot-central-operator-guide"></a>Guide de l’opérateur IoT Central

*Cet article est destiné aux opérateurs.*

Une application IoT Central vous permet de surveiller et de gérer des millions d’appareils tout au long de leur cycle de vie. Ce guide est conçu pour les opérateurs qui utilisent une application IoT Central pour gérer les appareils IoT.

Un opérateur :

- Surveille et gère les appareils connectés à l’application.
- Résout et corrige les problèmes liés aux appareils.
- Approvisionne de nouveaux appareils.

## <a name="monitor-and-manage-devices"></a>Surveiller et gérer les appareils

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Capture d’écran montrant la vue d’un appareil":::

Pour surveiller les appareils, un opérateur peut utiliser les vues d’appareils définies par le générateur de solutions dans le cadre du modèle d’appareil. Ces vues peuvent afficher les données de télémétrie et les valeurs de propriétés des appareils. La vue **Vue d’ensemble** présentée dans la capture d’écran précédente en est un exemple.

Pour obtenir des informations plus détaillées, un opérateur peut utiliser des groupes d’appareils et les fonctionnalités d’analytique intégrées. Pour en savoir plus, consultez [Comment utiliser l’analytique pour analyser des données d’appareils](howto-create-analytics.md).

Pour gérer des appareils individuels, un opérateur peut utiliser des vues d’appareils pour définir les propriétés de l’appareil et du cloud, et appeler des commandes d’appareil. Des exemples incluent les vues **Gérer l’appareil** et **Commandes** dans la capture d’écran précédente.

Pour gérer des appareils en bloc, un opérateur peut créer et planifier des travaux. Les travaux peuvent mettre à jour des propriétés et exécuter des commandes sur plusieurs appareils. Pour en savoir plus, consultez [Créer et exécuter un travail dans votre application Azure IoT Central](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Résoudre et corriger des problèmes

L’opérateur est responsable de l’intégrité de l’application et de ses appareils. Le [Guide de résolution des problèmes](troubleshoot-connection.md) permet aux opérateurs de diagnostiquer et de résoudre des problèmes courants. Un opérateur peut utiliser la page **Appareils** pour bloquer les appareils qui semblent ne pas fonctionner correctement jusqu’à ce que le problème soit résolu.

## <a name="add-and-remove-devices"></a>Ajouter et supprimer des appareils

L’opérateur peut ajouter et supprimer des appareils dans votre application IoT Central, individuellement ou en bloc. Pour plus d’informations, consultez [Gérer les appareils dans votre application Azure IoT Central](howto-manage-devices.md).

## <a name="personalize"></a>Personnaliser

Les opérateurs peuvent créer des tableaux de bord personnalisés dans une application IoT Central qui contiennent des liens vers les ressources qu’ils utilisent le plus souvent. Pour en savoir plus, consultez [Gérer les tableaux de bord](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez en savoir plus sur l’utilisation d’IoT Central, nous vous suggérons de tester les démarrages rapides, en commençant par [Créer une application Azure IoT Central](./quick-deploy-iot-central.md).
