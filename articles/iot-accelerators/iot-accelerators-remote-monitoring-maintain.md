---
title: Utiliser des alertes dans la solution de surveillance à distance – Azure | Microsoft Docs
description: Ce tutoriel vous montre comment utiliser des alertes pour identifier et résoudre les problèmes avec les appareils connectés à l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 853fa2b80e04dd8d9225d023db8030fed044ed7f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890917"
---
# <a name="tutorial-troubleshoot-and-fix-device-issues"></a>Didacticiel : Résoudre et corriger les problèmes liés aux appareils

Dans ce tutoriel, vous utilisez l’accélérateur de solution de supervision à distance pour identifier et résoudre les problèmes avec vos appareils IoT connectés. Vous utilisez des alertes dans le tableau de bord de l’accélérateur de solution pour identifier les problèmes, puis vous exécutez des tâches à distance pour résoudre ces problèmes.

Contoso fait le test d’un nouvel appareil **Prototype** sur le terrain. En tant qu’opérateur de Contoso, vous remarquerez pendant le test que l’appareil **Prototype** déclenche de façon inattendue une alerte de température dans le tableau de bord. Vous devez maintenant examiner le comportement de cet appareil **Prototype** défectueux et résoudre le problème.

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Investiguer une alerte provenant d’un appareil
> * Résoudre le problème avec l’appareil

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="investigate-an-alert"></a>Investiguer une alerte

Dans la page **Tableau de bord**, vous remarquez des alertes de température inattendue déclenchées par la règle associée aux appareils **Prototype** :

[![Affichage des alertes dans le tableau de bord](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm-expanded.png#lightbox)

Pour investiguer le problème, sous Explorer, cliquez sur **(...)** à côté de l’alerte en question :

[![Explorer l’alerte dans le tableau de bord](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm-expanded.png#lightbox)

Des informations détaillées sur l’alerte s’affichent :

* À quel moment l’alerte a été déclenchée
* Les informations d’état sur les appareils associés à l’alerte
* Les données de télémétrie des appareils associés à l’alerte

[![Détails de l’alerte](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail-expanded.png#lightbox)

Pour confirmer la réception de l’alerte, sélectionnez toutes les **occurrences de l’alerte**, puis choisissez **Confirmer la réception**. Cette action permet de montrer aux autres opérateurs que vous avez vu l’alerte et que vous travaillez à sa résolution :

[![Confirmer réception des alertes](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge-expanded.png#lightbox)

Lorsque vous confirmez réception de l’alerte, l’état de l’occurrence devient **Réception confirmée**.

Dans la liste des appareils avec alerte, vous pouvez voir l’appareil **Prototype** chargé de déclencher l’alerte de température :

[![Lister les appareils à l’origine de l’alerte](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice-expanded.png#lightbox)

## <a name="resolve-the-issue"></a>Résoudre le problème

Pour résoudre le problème avec l’appareil **Prototype**, vous devez appeler la méthode **DecreaseTemperature** sur l’appareil.

Pour intervenir sur un appareil, sélectionnez-le dans la liste des appareils avec alerte, puis choisissez **Travaux**. Le modèle d’appareil **Prototype** prend en charge six méthodes :

[![Voir les méthodes prises en charge par l’appareil](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods-expanded.png#lightbox)

Choisissez **DecreaseTemperature** et choisissez **DecreaseTemperature** comme nom du travail. Cliquez alors sur **Appliquer** :

[![Créer le travail pour réduire la température](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob-expanded.png#lightbox)

Pour suivre l’état du travail, cliquez sur **Afficher l’état du travail**. Utilisez la vue **Travaux** pour effectuer le suivi de tous les travaux et appels de méthodes de la solution :

[![Surveiller le travail pour réduire la température](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob-expanded.png#lightbox)

Vous pouvez vérifier que la température de l’appareil a diminué en consultant les données de télémétrie sur la page **Tableau de bord** :

[![Visualiser la baisse de température](./media/iot-accelerators-remote-monitoring-maintain/jobresult-inline.png)](./media/iot-accelerators-remote-monitoring-maintain/jobresult-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a montré comment utiliser des alertes pour identifier des problèmes avec vos appareils et comment intervenir sur ces appareils pour résoudre les problèmes. Pour savoir comment connecter un appareil réel à votre accélérateur de solution, continuez avec les articles de guide pratique.

Maintenant que vous savez comment gérer les problèmes des appareils, l’étape suivante que nous vous suggérons est d’apprendre à [connecter votre appareil à l’accélérateur de solution de supervision à distance](iot-accelerators-connecting-devices.md).
