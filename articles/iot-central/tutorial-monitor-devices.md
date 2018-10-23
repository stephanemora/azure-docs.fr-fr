---
title: Surveiller vos appareils dans Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, utilisez votre application Azure IoT Central pour surveiller vos appareils.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 91a4fceaf5a3e573964919d365a67519b26278ca
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310622"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Didacticiel : utiliser Azure IoT Central pour surveiller vos appareils

Ce didacticiel vous explique, en tant qu’opérateur, comment utiliser votre application Microsoft Azure IoT Central pour surveiller vos appareils et modifier les paramètres.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Recevoir une notification
> * Analyser un problème
> * Résoudre un problème

## <a name="prerequisites"></a>Prérequis

Avant de commencer, le générateur doit effectuer les trois didacticiels du générateur pour créer l’application Azure IoT Central :

* [Définir un nouveau type d’appareil](tutorial-define-device-type.md)
* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md)
* [Personnaliser les vues de l’opérateur](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Recevoir une notification

Azure IoT Central envoie des notifications sur les appareils sous forme de courriers électroniques. Le générateur a ajouté une règle pour envoyer une notification lorsque la température dans un climatiseur raccordé dépasse un seuil. Consultez les courriers électroniques envoyés au compte choisi par le générateur pour recevoir des notifications.

Ouvrez le courrier électronique que vous avez reçu à la fin du didacticiel [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md). Dans le courrier électronique, choisissez **Cliquez ici pour ouvrir votre appareil** :

![Règles du Générateur d’applications](media/tutorial-monitor-devices/email.png)

La page **Appareil** de l’appareil simulé **Climatiseur raccordé-1** que vous avez créé dans les didacticiels précédents s’ouvre dans votre navigateur :

![Appareil ayant déclenché le courrier électronique de notification](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Analyser un problème

En tant qu’opérateur, vous pouvez afficher des informations sur l’appareil sur les pages **Mesures**, **Paramètres**, **Propriétés**, **Règles**et  **Tableau de bord**. Le générateur a personnalisé le **tableau de bord** afin d’afficher des informations importantes sur un climatiseur raccordé.

Choisissez la vue **Tableau de bord** pour afficher des informations sur l’appareil.

![Page du tableau de bord d’un appareil](media/tutorial-monitor-devices/initial_screen.png)

Le graphique sur le tableau de bord affiche un tracé de la température de l’appareil. Vous pouvez également consulter la température cible actuelle de l’appareil dans la vignette **Set target temperature** (Température cible réglée). Vous jugez que la température cible est trop élevée.

## <a name="remediate-an-issue"></a>Résoudre un problème

Pour modifier la température cible de l’appareil, utilisez la page **Paramètres** :

1. Choisissez **Paramètres**. Définissez **Set Temperature** (Température réglée) sur 75. Choisissez **Mettre à jour** pour envoyer la nouvelle température cible à l’appareil. Lorsque l’appareil accuse réception d’une modification de paramètre, la valeur du d’état devient **synchronisé** :

    ![Mettre à jour les paramètres](media/tutorial-monitor-devices/change_settings.png)

2. Choisissez **Tableau de bord** et vérifiez la nouvelle valeur du paramètre :

    ![Tableau de bord de l’appareil mis à jour](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="nextstepaction"]
> * Recevoir une notification
> * Analyser un problème
> * Résoudre un problème

Maintenant que vous savez comment surveiller votre appareil, l’étape suivante suggérée consiste à [ajouter un appareil](tutorial-add-device.md).