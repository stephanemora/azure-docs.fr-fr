---
title: Ajouter un appareil réel à une application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, ajoutez un appareil réel à votre application Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878860"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Didacticiel : Ajouter un appareil simulé à votre application Azure IoT Central (fonctionnalités d’évaluation)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ce tutoriel montre comment ajouter et configurer un appareil simulé dans votre application Microsoft Azure IoT Central.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Ajouter un nouvel appareil simulé
> * Utiliser un appareil simulé dans l’expérience de création

## <a name="prerequisites"></a>Prérequis

Avant de commencer, le générateur doit suivre le premier tutoriel pour générateur afin de créer l’application Azure IoT Central :

* [Définir un nouveau type d’appareil](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (obligatoire)

## <a name="add-a-simulated-device"></a>Utiliser un appareil simulé

Pour ajouter un appareil réel à votre application, vous utilisez le modèle d’appareil **Capteur environnemental** que vous avez créé dans le tutoriel [Définir un nouveau type d’appareil](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Pour ajouter un appareil en tant qu’opérateur, choisissez **Appareils** dans le menu de navigation gauche. L’onglet **Appareils** affiche **Tous les appareils** et le modèle d’appareil **Capteur environnemental**.

1. Pour ajouter un appareil capteur environnemental simulé, sélectionnez **+ Nouveau**. Utilisez l’**ID d’appareil** suggéré ou entrez votre propre **ID d’appareil** en minuscules. Vous pouvez également entrer un nom pour votre nouvel appareil. Basculez le bouton **simulé** sur **On**, puis sélectionnez **Créer**.

    ![Appareil simulé](./media/tutorial-add-device-pnp/simulated-device.png)

Vous pouvez désormais interagir avec les vues qui ont été créées par le générateur pour le modèle d’appareil à l’aide de données simulées.

## <a name="use-a-simulated-device-to-improve-views"></a>Utiliser un appareil simulé pour améliorer les vues

Une fois que vous avez créé un appareil simulé, le générateur peut utiliser cet appareil pour continuer à améliorer les vues du modèle d’appareil.

1. Dans la vue de votre appareil, copiez l’**ID d’appareil** simulé que vous avez créé.

1. Choisissez l’onglet **Modèles d’appareil** dans le menu de navigation gauche, puis sélectionnez le modèle **Capteur environnemental**.

1. Sélectionnez l’une des vues que vous souhaitez modifier, ou créez une nouvelle vue. Cliquez sur **Configurer l’aperçu d’appareil**. Vous pouvez choisir entre n’avoir aucun aperçu d’appareil, utiliser un appareil réel que vous pouvez configurer à des fins de test, ou choisir un appareil existant que vous avez ajouté dans IoT Central.

1. Choisissez **Sélectionner à partir d’un appareil en cours d’exécution** et entrez l’**ID d’appareil** de l’appareil simulé que vous avez copié.

1. Choisissez **Appliquer**. Vous pouvez maintenant voir le même appareil simulé dans votre expérience de génération de vues de modèle d’appareil. Cette vue est particulièrement utile pour les graphiques et autres visualisations.

    ![Configurer l’aperçu d’appareil](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Ajouter un nouvel appareil simulé
* Utiliser un appareil simulé dans l’expérience de création

Maintenant que vous avez connecté un appareil simulé à votre application Azure IoT Central, voici quelques prochaines étapes suggérées.

En tant qu’opérateur, vous pouvez apprendre à :

> [!div class="nextstepaction"]
> [Configurer des règles](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

En tant que développeur d’appareils, vous pouvez apprendre à :

> [!div class="nextstepaction"]
> [Connecter un appareil DevKit IoT MXChip à votre application Azure IoT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



