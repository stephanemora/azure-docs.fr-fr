---
title: Dépanner des appareils dans la solution de surveillance à distance - Azure | Microsoft Docs
description: Ce didacticiel vous montre comment dépanner et corriger les problèmes d’appareils dans la solution de surveillance à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 9a620d91238393ba0bde89f521f790b58ab35baf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628070"
---
# <a name="troubleshoot-and-remediate-device-issues"></a>Dépanner et corriger les problèmes d’un appareil

Ce didacticiel vous montre comment utiliser la page **Maintenance** dans la solution pour dépanner et corriger les problèmes d’un appareil. Pour présenter ces fonctionnalités, le didacticiel utilise un scénario dans l’application Contoso IoT.

Contoso fait le test d’un nouvel appareil **Prototype** sur le terrain. En tant qu’opérateur de Contoso, vous remarquerez pendant le test que l’appareil **Prototype** déclenche de façon inattendue une alerte de température dans le tableau de bord. Vous devez commencer à étudier le comportement de cet appareil **Prototype** défectueux.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

>[!div class="checklist"]
> * Utiliser la page **Maintenance** pour examiner l’alerte
> * Appeler une méthode d’appareil pour résoudre le problème

## <a name="prerequisites"></a>Prérequis

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de la solution de surveillance à distance dans votre abonnement Azure.

Si vous n’avez pas encore déployé la solution de surveillance à distance, vous devez suivre le tutoriel [Déployer l’accélérateur de solution de surveillance à distance](iot-accelerators-remote-monitoring-deploy.md).

## <a name="use-the-maintenance-dashboard"></a>Utiliser le tableau de bord de maintenance

Dans la page **Tableau de bord**, vous remarquez des alertes de température inattendue déclenchées par la règle associée aux appareils **Prototype** :

![Affichage des alertes dans le tableau de bord](./media/iot-accelerators-remote-monitoring-maintain/dashboardalarm.png)

Pour examiner le problème, sous Explorer, cliquez sur **(...)** à côté de l’alerte en question :

![Explorer l’alerte dans le tableau de bord](./media/iot-accelerators-remote-monitoring-maintain/dashboardexplorealarm.png)

Des informations détaillées sur l’alerte s’affichent :

* À quel moment l’alerte a été déclenchée
* Les informations d’état sur les appareils associés à l’alerte
* Les données de télémétrie des appareils associés à l’alerte

![Détails de l’alerte](./media/iot-accelerators-remote-monitoring-maintain/maintenancealarmdetail.png)

Pour accuser réception de l’alerte, sélectionnez les **occurrences de l’alerte**, puis choisissez **Acknowledge** (Accuser réception). Cette action permet de montrer aux autres opérateurs que vous avez vu l’alerte et que vous travaillez à sa résolution.

![Accuser réception des alertes](./media/iot-accelerators-remote-monitoring-maintain/maintenanceacknowledge.png)

Lorsque vous accusez réception de l’alerte, l’état de l’occurrence devient **Acknowledged** (Réception confirmée).

Dans la liste, vous pouvez voir l’appareil **Prototype** chargé de déclencher l’alerte de température :

![Liste des appareils à l’origine de l’alerte](./media/iot-accelerators-remote-monitoring-maintain/maintenanceresponsibledevice.png)

## <a name="remediate-the-issue"></a>Résoudre le problème

Pour résoudre le problème avec l’appareil **Prototype**, vous devez appeler la méthode **DecreaseTemperature** sur l’appareil.

Pour agir sur un appareil, sélectionnez-le dans la liste des appareils, puis choisissez **Travaux**. Le modèle d’appareil **Prototype** définit six méthodes que doivent prendre en charge les appareils :

![Afficher les méthodes prises en charge par l’appareil](./media/iot-accelerators-remote-monitoring-maintain/maintenancemethods.png)

Choisissez **DecreaseTemperature** et choisissez **DecreaseTemperature** comme le nom du travail. Choisissez ensuite **Appliquer** :

![Créer le travail pour réduire la température](./media/iot-accelerators-remote-monitoring-maintain/maintenancecreatejob.png)

Pour suivre l’état du travail sur la page **Maintenance**, sélectionnez **Travaux**. Utilisez la vue **Travaux** pour effectuer le suivi de tous les travaux et appels de méthodes de la solution :

![Surveiller le travail pour réduire la température](./media/iot-accelerators-remote-monitoring-maintain/maintenancerunningjob.png)

Pour afficher les détails d’un travail ou d’un appel de méthode en particulier, sélectionnez-le dans la liste au sein de la vue **Travaux** :

![Affichage des détails d’une tâche](./media/iot-accelerators-remote-monitoring-maintain/maintenancejobdetail.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Utiliser la page **Maintenance** pour examiner l’alerte
> * Appeler une méthode d’appareil pour résoudre le problème

Vous avez maintenant appris à gérer les problèmes d’appareil, l’étape suivante suggérée consiste à apprendre comment [Tester votre solution avec des appareils simulés](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->