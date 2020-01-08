---
title: Démarrage rapide – Superviser vos appareils dans Azure IoT Central
description: En tant qu’opérateur, utilisez votre application Azure IoT Central pour superviser vos appareils dans ce guide de démarrage rapide.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e0f0e765ba6bb8b0969fbf639c8427e8b65214fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434771"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Démarrage rapide : Utiliser Azure IoT Central pour superviser vos appareils (fonctionnalités de préversion)

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce démarrage rapide vous explique comment, en tant qu'opérateur, vous pouvez utiliser votre application Microsoft Azure IoT Central pour surveiller vos appareils et modifier les paramètres.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer, vous devez suivre les trois guides de démarrage rapide précédents : [Créer une application Azure IoT Central](./quick-deploy-iot-central.md), [Ajouter un appareil simulé à votre application IoT Central](./quick-create-pnp-device.md) et [Configurer des règles et des actions pour votre appareil](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Recevoir une notification

Azure IoT Central envoie des notifications sur les appareils sous forme de courriers électroniques. Le générateur a ajouté une règle pour envoyer une notification quand la température dans un capteur environnemental connecté dépasse un seuil. Consultez les courriers électroniques envoyés au compte choisi par le générateur pour recevoir des notifications.

Ouvrez le courrier électronique que vous avez reçu à la fin du guide de démarrage rapide [Configurer des règles et des actions pour votre appareil](quick-configure-rules.md). Dans l’e-mail, sélectionnez le lien vers l’appareil :

![E-mail de notification d’alerte](media/quick-monitor-devices/email.png)

La vue **Tableau de bord** de l'appareil simulé Capteur environnemental que vous avez créé dans les guides de démarrage rapide précédents s'ouvre dans votre navigateur :

![Appareil ayant déclenché le courrier électronique de notification](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Analyser un problème

En tant qu'opérateur, vous pouvez consulter les informations relatives à l'appareil sur les pages **Vue d'ensemble**, **Propriétés du capteur environnemental** et **Commandes**. Le générateur a personnalisé les pages **Tableau de bord** et **Propriétés de Capteur environnemental** pour voir des informations importantes sur un appareil capteur environnemental connecté.

Choisissez **Vue d'ensemble** pour consulter les informations relatives à l'appareil.

Le graphique sur le tableau de bord affiche un tracé de la température de l’appareil. Vous jugez que la température de l'appareil est trop élevée.

## <a name="remediate-an-issue"></a>Résoudre un problème

Pour apporter une modification à l'appareil, utilisez la page **Propriétés du capteur environnemental**.

Choisissez **Propriétés de Capteur environnemental**. Affectez la valeur 10 au **Niveau de luminosité**. Choisissez **Enregistrer** pour mettre à jour l’appareil. Quand l’appareil confirme la modification du paramètre, l’état de la propriété passe à **synchronisé** :

![Mettre à jour les paramètres](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

* Recevoir une notification
* Analyser un problème
* Résoudre un problème

Maintenant que vous savez comment superviser votre appareil, vous pouvez passer à l’étape suivante :

> [!div class="nextstepaction"]
> [Créer et gérer un modèle d'appareil](howto-set-up-template.md).
