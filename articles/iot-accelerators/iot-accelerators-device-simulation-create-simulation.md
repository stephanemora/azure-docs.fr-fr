---
title: Créer une nouvelle simulation IoT - Azure | Microsoft Docs
description: Dans ce didacticiel, vous allez utiliser la simulation d’appareil pour créer et exécuter une nouvelle simulation.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 09a6920e0d3a50da1bdacbf2bc7a80396c885897
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "61448556"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Didacticiel : Créer et exécuter une simulation d’appareil IoT

Dans ce didacticiel, vous allez utiliser la simulation d’appareil pour créer et exécuter une nouvelle simulation IoT à avec un ou plusieurs appareils simulés.

Dans ce tutoriel, vous allez :

>[!div class="checklist"]
> * Afficher toutes les simulations actives et l’historique des simulations
> * Créer et exécuter une nouvelle simulation
> * Afficher les métriques d’une simulation
> * Arrêter une simulation

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel, vous avez besoin d’une instance déployée de la simulation d’appareil dans votre abonnement Azure.

Si vous n’avez pas encore déployé la Simulation d’appareil, vous devez terminer le guide de démarrage rapide [Déployer et exécuter une simulation d’appareil IoT dans Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Ouvrir la Simulation d’appareil

Pour exécuter la Simulation d’appareil dans votre navigateur, accédez d’abord aux [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com). 

Vous pouvez être invité à vous connecter les informations d’identification de votre abonnement Azure.

Cliquez ensuite sur l’option **Launch** (Lancer) de la vignette de la simulation d’appareil que vous avez déployée à l’aide du [guide de démarrage rapide](quickstart-device-simulation-deploy.md).

## <a name="view-simulations"></a>Afficher les simulations

Sélectionnez **Simulations** dans la barre de menus. La page **Simulations** affiche des informations sur toutes les simulations disponibles. Dans cette page, vous pouvez voir les simulations en cours d’exécution et celles exécutées précédemment. Pour réexécuter une simulation antérieure, cliquez sur la vignette de la simulation pour en afficher les détails :

![Simulations](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Créer une simulation

Pour créer une simulation, cliquez sur **+ Nouvelle simulation** dans l’angle supérieur droit.

Une simulation se compose d’un ou plusieurs modèles d’appareil. Le modèle d’appareil définit le comportement, la télémétrie et le format du message de l’appareil à simuler.

Pour ajouter un modèle d’appareil, cliquez sur **+ Ajouter un type d’appareil** et sélectionnez le modèle d’appareil dans la liste. Votre simulation peut contenir plusieurs modèles d’appareil. Chaque modèle d’appareil peut compter un nombre différent d’appareils et utiliser diverses fréquences de messages.

Une fois le formulaire de la nouvelle simulation complété, cliquez sur **Démarrer la simulation** pour commencer la simulation.

En fonction du nombre d’appareils simulés, la configuration et le démarrage de votre simulation peuvent prendre quelques minutes :

![Nouvelle simulation](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Arrêter une simulation

Lorsque vous cliquez sur **Démarrer la simulation**, la page des détails de la simulation s’affiche. Cette page de détails contient les statistiques de simulation en direct et les métriques d’IoT Hub. Vous pouvez également accéder à cette page de détails en cliquant sur la vignette de la simulation dans la page **Simulations**.

Pour arrêter une simulation, cliquez sur l’option **Arrêter la simulation** de la barre d’actions dans le coin supérieur droit.

![Arrêter la simulation](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à créer, exécuter et arrêter une simulation. Vous avez également appris comment afficher les détails de la simulation. Pour en savoir plus sur l’exécution des simulations, passez au didacticiel suivant :

> [!div class="nextstepaction"]
> [Créer un appareil simulé personnalisé](iot-accelerators-device-simulation-create-custom-device.md)
