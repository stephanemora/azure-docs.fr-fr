---
title: Surveiller vos appareils dans Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, utilisez votre application Azure IoT Central pour surveiller vos appareils.
author: dominicbetts
ms.author: dobett
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9480565643887b5a9a4d644ba3173b365eaea29c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767771"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Tutoriel : Utiliser Azure IoT Central pour surveiller vos appareils

Ce didacticiel vous explique, en tant qu’opérateur, comment utiliser votre application Microsoft Azure IoT Central pour surveiller vos appareils et modifier les paramètres.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Recevoir une notification
> * Analyser un problème
> * Résoudre un problème

## <a name="prerequisites"></a>Prérequis

Avant de commencer, le générateur doit effectuer les trois didacticiels du générateur pour créer l’application Azure IoT Central :

* [Définir un nouveau type d’appareil](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Personnaliser les vues de l’opérateur](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Recevoir une notification

Azure IoT Central envoie des notifications sur les appareils sous forme de courriers électroniques. Le générateur a ajouté une règle pour envoyer une notification lorsque la température dans un climatiseur raccordé dépasse un seuil. Consultez les courriers électroniques envoyés au compte choisi par le générateur pour recevoir des notifications.

Ouvrez le courrier électronique que vous avez reçu à la fin du didacticiel [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Dans le courrier électronique, choisissez **Cliquez ici pour ouvrir votre appareil** :

![E-mail de notification d’alerte](media/tutorial-monitor-devices-experimental/email.png)

La page **Appareil** de l’appareil simulé **Climatiseur raccordé-1** que vous avez créé dans les didacticiels précédents s’ouvre dans votre navigateur :

![Appareil ayant déclenché le courrier électronique de notification](media/tutorial-monitor-devices-experimental/sourcedevice.png)

## <a name="investigate-an-issue"></a>Analyser un problème

En tant qu’opérateur, vous pouvez afficher des informations sur l’appareil sur les pages **Mesures**, **Paramètres**, **Propriétés**, **Règles**et  **Tableau de bord**. Le générateur a personnalisé le **tableau de bord** afin d’afficher des informations importantes sur un climatiseur raccordé.

Choisissez la vue **Tableau de bord** pour afficher des informations sur l’appareil.

![Page du tableau de bord d’un appareil](media/tutorial-monitor-devices-experimental/initial_screen.png)

Le graphique sur le tableau de bord affiche un tracé de la température de l’appareil. Vous pouvez également consulter la température cible actuelle de l’appareil dans la vignette **Set target temperature** (Température cible réglée). Vous jugez que la température cible est trop élevée.

## <a name="remediate-an-issue"></a>Résoudre un problème

Pour modifier la température cible de l’appareil, utilisez la page **Paramètres** :

1. Choisissez **Paramètres**. Définissez **Set Temperature** (Température réglée) sur 75. Choisissez **Mettre à jour** pour envoyer la nouvelle température cible à l’appareil. Quand l’appareil confirme la modification du paramètre, l’état du paramètre passe à **synchronisé** :

    ![Mettre à jour les paramètres](media/tutorial-monitor-devices-experimental/change_settings.png)

2. Choisissez **Tableau de bord** et vérifiez la nouvelle valeur du paramètre :

    ![Tableau de bord de l’appareil mis à jour](media/tutorial-monitor-devices-experimental/new_settings.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="nextstepaction"]
> * Recevoir une notification
> * Analyser un problème
> * Résoudre un problème

Maintenant que vous savez comment surveiller votre appareil, l’étape suivante suggérée consiste à [ajouter un appareil](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).