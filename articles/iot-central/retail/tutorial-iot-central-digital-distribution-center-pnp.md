---
title: Didacticiel sur le centre de distribution numérique Internet des objets | Microsoft Docs
description: Un didacticiel sur le modèle d’application du centre de distribution numérique pour IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 879e1911c715688c2fed4044e23f437b3bbef7b2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890673"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Didacticiel : Déployer et parcourir un modèle d’application du centre de distribution numérique

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce didacticiel vous montre comment prendre en main le déploiement d’un modèle d’application du **centre de distribution numérique** IoT Central. Vous allez apprendre à déployer le modèle, ce qui est inclus prêt à l’emploi et ce que vous pourriez vouloir faire ensuite.

Dans ce didacticiel, vous apprendrez comment, 
* créer une application pour un centre de distribution numérique 
* examiner l’application 

## <a name="prerequisites"></a>Prérequis
* Aucune configuration requise spécifique n’est requise pour déployer cette application
* Il est recommandé d’avoir un abonnement Azure, mais vous pouvez essayer sans

## <a name="create-digital-distribution-center-application-template"></a>Créer un modèle d’application pour un centre de distribution numérique

Vous pouvez créer une application en suivant les étapes ci-dessous

1. Accédez au site web du gestionnaire d’applications Azure IoT Central. Sélectionnez **Générer** dans la barre de navigation de gauche, puis cliquez sur l’onglet **Distribution**.

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. Sélectionnez l’onglet **Distribution** et sélectionnez **Créer une application** sous ** application du centre de distribution numérique **

3. **Créer une application** ouvre le nouveau formulaire d’application et remplit les détails demandés comme indiqué ci-dessous.
   **Nom de l’application** : vous pouvez utiliser le nom suggéré par défaut ou entrer le nom convivial de votre application.
   **URL** : vous pouvez utiliser l’URL par défaut suggérée ou entrer votre URL conviviale unique et facile à mémoriser. Ensuite, le paramètre par défaut est recommandé si vous disposez déjà d’un abonnement Azure. Sinon, vous pouvez démarrer avec une évaluation gratuite de sept jours et choisir de convertir le paiement à l’utilisation à tout moment avant l’expiration de l’évaluation gratuite.
   **Informations de facturation** : Les informations sur le répertoire, l’abonnement Azure et la région sont requises pour configurer les ressources.
   **Créer** : Sélectionnez Créer en bas de la page pour déployer votre application.

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-create.png)

## <a name="walk-through-the-application-dashboard"></a>Examiner le tableau de bord de l’application 

Après le déploiement réussi du modèle d’application, votre tableau de bord par défaut est un portail dédié à l’opérateur du centre de distribution. Northwind Trader est un fournisseur de solutions de centres de distribution fictifs qui gère les systèmes de transport. 

Dans ce tableau de bord, vous verrez une passerelle et une caméra faisant office d’appareil IoT. La passerelle fournit des données de télémétrie sur les packages tels que valide, non valide, non identifié et taille, ainsi que les propriétés de jumeaux d’appareil associées. Toutes les commandes en aval sont exécutées sur des appareils IoT, tels qu’une caméra. Ce tableau de bord est préconfiguré pour présenter l’activité des opérations de l’appareil du centre de distribution critique.

Le tableau de bord est structuré de manière logique pour afficher les fonctionnalités de gestion des périphériques de la passerelle Azure IoT et de l’appareil IoT.  
   * Vous pouvez exécuter des tâches de commande et de contrôle de la passerelle
   * Gérez toutes les caméras qui font partie de la solution. 

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Modèle d’appareil

Cliquez sur l’onglet Modèles d’appareil pour voir le modèle de fonctionnalités de la passerelle. Un modèle de fonctionnalités est structuré autour de deux interfaces différentes **Caméra** et **Passerelle de distribution numérique**

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Caméra** : cette interface organise toutes les fonctionnalités de commande propres à la caméra 

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Passerelle de distribution numérique** : cette interface représente toutes les données de télémétrie provenant de la caméra, les propriétés du jumeau d'appareil définies dans le cloud et les informations sur la passerelle.

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Commandes de la passerelle
Cette interface organise toutes les fonctionnalités de commande de la passerelle

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Règles
Sélectionnez l’onglet Règles pour afficher deux règles différentes qui existent dans ce modèle d’application. Ces règles sont configurées pour envoyer des notifications par e-mail aux opérateurs pour des investigations supplémentaires.

 **Un trop grand nombre d’alertes de packages non valides** : cette règle est déclenchée lorsque la caméra détecte un nombre élevé de packages non valides circulant dans le système de transport.
 
**Packages volumineux** : cette règle est déclenchée si la caméra détecte un paquet volumineux qui ne peut pas être inspecté pour la qualité. 

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Tâches
Sélectionnez l’onglet Tâches pour afficher cinq types de tâches disponibles dans le cadre de ce modèle d’application : Vous pouvez tirer parti de la fonctionnalité Travaux pour effectuer des opérations sur l’ensemble de la solution. Ici, les travaux du centre de distribution numérique utilisent les commandes de l’appareil et la fonctionnalité de jumeau pour effectuer des tâches telles que,
   * l’étalonnage de la caméra avant le lancement de la détection du package 
   * la mise à jour périodique du microprogramme de la caméra
   * la modification de l’intervalle de télémétrie pour gérer le chargement de données

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’envisagez pas de continuer à utiliser cette application, supprimez le modèle d’application en visitant **Administration** > **Paramètres de l’application**, puis cliquez sur **supprimer**.

> [!div class="mx-imgBorder"]
> ![Centre de distribution numérique](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’architecture de la solution du centre de distribution numérique [concept du centre de distribution numérique](./architecture-digital-distribution-center-pnp.md)
* En savoir plus sur d’autres [modèles de distribution IoT Central](./overview-iot-central-retail-pnp.md)
* En savoir plus sur IoT Central en lisant [Vue d’ensemble d’IoT Central](../preview/overview-iot-central.md)
