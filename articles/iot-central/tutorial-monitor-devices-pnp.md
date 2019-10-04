---
title: Surveiller vos appareils dans Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, utilisez votre application Azure IoT Central pour surveiller vos appareils.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878780"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Didacticiel : Utiliser Azure IoT Central pour superviser vos appareils (fonctionnalités de préversion)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ce didacticiel vous explique, en tant qu’opérateur, comment utiliser votre application Microsoft Azure IoT Central pour surveiller vos appareils et modifier les paramètres.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Recevoir une notification
> * Analyser un problème
> * Résoudre un problème

## <a name="prerequisites"></a>Prérequis

Avant de commencer, le générateur doit effectuer les trois didacticiels du générateur pour créer l’application Azure IoT Central :

* [Définir un nouveau type d’appareil](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Ajouter un appareil](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Recevoir une notification

Azure IoT Central envoie des notifications sur les appareils sous forme de courriers électroniques. Le générateur a ajouté une règle pour envoyer une notification quand la température dans un capteur environnemental connecté dépasse un seuil. Consultez les courriers électroniques envoyés au compte choisi par le générateur pour recevoir des notifications.

Ouvrez le courrier électronique que vous avez reçu à la fin du didacticiel [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Dans l’e-mail, sélectionnez le lien vers l’appareil :

![E-mail de notification d’alerte](media/tutorial-monitor-devices-pnp/email.png)

La vue **Tableau de bord** de l’appareil simulé capteur environnemental que vous avez créé dans les tutoriels précédents s’ouvre dans votre navigateur :

![Appareil ayant déclenché le courrier électronique de notification](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Analyser un problème

En tant qu’opérateur, vous pouvez voir les informations de l’appareil dans les pages **Tableau de bord**, **Propriétés de Capteur environnemental** et **Commandes**. Le générateur a personnalisé les pages **Tableau de bord** et **Propriétés de Capteur environnemental** pour voir des informations importantes sur un appareil capteur environnemental connecté.

Choisissez la vue **Tableau de bord** pour afficher des informations sur l’appareil.

Le graphique sur le tableau de bord affiche un tracé de la température de l’appareil. Vous pouvez également consulter la température cible actuelle de l’appareil dans la vignette **Propriétés de l’appareil**. Vous jugez que la température cible est trop élevée.

## <a name="remediate-an-issue"></a>Résoudre un problème

Pour apporter une modification à l’appareil, utilisez la page **Propriétés de Capteur environnemental** :

1. Choisissez **Propriétés de Capteur environnemental**. Affectez la valeur 10 au **Niveau de luminosité**. Choisissez **Enregistrer** pour mettre à jour l’appareil. Quand l’appareil confirme la modification du paramètre, l’état de la propriété passe à **synchronisé** :

    ![Mettre à jour les paramètres](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Choisissez **Tableau de bord** et vérifiez la nouvelle valeur de la propriété :

    ![Tableau de bord de l’appareil mis à jour](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Recevoir une notification
* Analyser un problème
* Résoudre un problème

Maintenant que vous savez comment superviser votre appareil, vous pouvez passer à l’étape suivante :

> [!div class="nextstepaction"]
> [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).