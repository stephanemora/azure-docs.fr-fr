---
title: Définir un nouveau type d’appareil dans Azure IoT Central | Microsoft Docs
description: Ce tutoriel vous montre comment créer un nouveau type d’appareil dans votre application Azure IoT Central en votre qualité de générateur. Vous définissez la télémétrie, l’état, les propriétés et les commandes pour votre type d’appareil.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 660b2ead146695657ae13444cb7936eff8224f3a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099514"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>Didacticiel : Définir un nouveau type d’appareil dans votre application Azure IoT Central (fonctionnalités d’évaluation)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ce didacticiel vous indique comment utiliser un modèle d’appareil pour définir un nouveau type d’appareil dans votre application Microsoft Azure IoT Central en votre qualité de générateur. Un modèle d’appareil définit les capacités de votre appareil. Les capacités incluent la télémétrie que l’appareil envoie, les propriétés de l’appareil et les commandes auxquelles un appareil répond.

Dans ce tutoriel, vous allez créer un modèle d’appareil **Capteur environnemental**. Un appareil capteur environnemental :

* Envoie des données de télémétrie comme la température.
* Signale des propriétés propres à l’appareil, telles que le niveau de luminosité.
* Répond aux commandes telles que Activer et Désactiver.
* Signale des propriétés d’appareil génériques, telles que la version du microprogramme et le numéro de série.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un modèle d’appareil
> * Importer un modèle de capacité d’appareil
> * Créer des propriétés du cloud
> * Définir une visualisation pour la télémétrie de l’appareil
> * Publier votre modèle d’appareil
> * Créer un appareil simulé pour votre modèle d’appareil
> * Visualiser votre appareil simulé

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous devez disposer d’une application Azure IoT Central. Si vous avez suivi le guide de démarrage rapide [Créer votre application Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), vous pouvez réutiliser l’application que vous avez créée dans le cadre de ce guide. Dans le cas contraire, créez une application Azure IoT Central vide en procédant comme suit :

1. Accédez au site web du [gestionnaire d’applications d’Azure IoT Central](https://aka.ms/iotcentral).

1. Si vous disposez d’un abonnement Azure, connectez-vous avec les informations d’identification que vous utilisez pour y accéder. Sinon, inscrivez-vous à l’aide d’un compte Microsoft :

    ![Entrer votre compte d’entreprise](./media/tutorial-define-device-type-pnp/sign-in.png)

1. Pour commencer à créer une application Azure IoT Central, sélectionnez **Nouvelle application**.

Pour créer une application Azure IoT Central qui utilise des fonctionnalités d’évaluation, notamment IoT Plug-and-Play :

1. Choisissez **Version d’évaluation**. Vous n’avez pas besoin d’un abonnement Azure pour créer une application d’essai.

    Pour plus d’informations sur les répertoires et les abonnements, consultez le guide de démarrage rapide [Créer une application](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Choisissez **Aperçu de l’application**.

1. Vous pouvez éventuellement choisir un nom d’application convivial, tel que **Climatiseurs Contoso**. Azure IoT Central génère un préfixe d’URL unique pour vous. Vous pouvez remplacer ce préfixe d’URL par une chaîne plus facile à mémoriser.

1. Si vous créez une application d’essai, vous devez fournir vos coordonnées.

1. Sélectionnez **Create** (Créer).

    ![Page de création d’une application Azure IoT Central](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

Vous avez également besoin d’une copie locale du fichier **EnvironmentalSensorInline.capabilitymodel.json** qui contient le modèle de capacité d’appareil [IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md). Vous pouvez le télécharger [ici](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json). Cliquez avec le bouton droit sur la page et sélectionnez **Enregistrer sous**.

Après avoir téléchargé le fichier, ouvrez-le dans un éditeur de texte et remplacez les deux instances de `<YOUR_COMPANY_NAME_HERE>` par votre nom. Utilisez uniquement les caractères a-z, A-Z, 0-9 et le trait de soulignement.

## <a name="create-a-template"></a>Créer un modèle

En tant que générateur, vous pouvez créer et modifier les modèles d’appareil dans votre application. Une fois que vous avez publié un modèle d’appareil, vous pouvez générer un appareil simulé ou connecter des appareils réels qui implémentent le modèle d’appareil. Les appareils simulés vous permettent de tester le comportement de votre application avant de connecter un appareil physique.

Pour ajouter un nouveau modèle d’appareil à votre application, accédez à la page **Modèles d’appareil**. Pour cela, sélectionnez l’onglet **Modèles d’appareil** dans le menu de navigation de gauche.

![Page Modèles d’appareil](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Ajouter un modèle de capacité d’appareil

Il existe plusieurs options pour créer un modèle de capacité d’appareil dans IoT Central. Vous pouvez choisir de créer un modèle personnalisé à partir de zéro, d’importer à partir d’un fichier, d’effectuer une sélection dans le catalogue d’appareils, ou de connecter un appareil IoT Plug-and-Play par le biais d’une première connexion à l’appareil où le modèle de capacité d’appareil a été publié dans le référentiel public. Dans ce tutoriel, vous importez un modèle de capacité d’appareil à partir d’un fichier.

Les étapes suivantes montrent comment importer le modèle de capacité pour un appareil **Capteur environnemental**. Ces appareils envoient des données de télémétrie, telles que la température, à votre application :

1. Pour ajouter un nouveau modèle d’appareil, sélectionnez **+ Nouveau** dans la page **Modèles d’appareil**.

1. Choisissez **Personnalisé** dans la liste de modèles.

1. Entrez **Capteur environnemental** comme nom de votre modèle d’appareil.

1. Choisissez **Importer le modèle de capacité** pour créer un modèle de capacité d’appareil à partir d’un fichier JSON. Accédez au dossier dans lequel vous avez enregistré le fichier **EnvironmentalSensorInline.capabilitymodel.json** sur votre ordinateur local. Sélectionnez le fichier **EnvironmentalSensorInline.capabilitymodel.json**, puis sélectionnez **Ouvrir**. Le modèle de capacité de capteur environnemental comprend les interfaces **Capteur environnemental** et **Informations sur l’appareil** :

    ![Modèle de capacité d’appareil Capteur environnemental](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    Ces interfaces définissent les capacités d’un appareil **Capteur environnemental**. Les capacités incluent la télémétrie que l’appareil envoie, les propriétés signalées par un appareil et les commandes auxquelles un appareil répond.

### <a name="add-cloud-properties"></a>Ajouter des propriétés du cloud

Un modèle d’appareil peut inclure des propriétés du cloud. Les propriétés du cloud existent uniquement dans l’application IoT Central, et ne sont jamais envoyées à un appareil ni reçues à partir d’un appareil.

1. Sélectionnez **Propriétés du cloud**, puis **+ Ajouter une propriété cloud**. Utilisez les informations du tableau suivant pour ajouter une propriété du cloud à votre modèle d’appareil.

    | Nom d’affichage      | Type de sémantique | Schéma |
    | ----------------- | ------------- | ------ |
    | Dernière date de service | Aucun          | Date   |
    | Nom du client     | Aucun          | Chaîne |

1. Cliquez sur **Enregistrer** pour enregistrer vos modifications :

    ![Propriétés du cloud](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>Ajouter des personnalisations

Utilisez des personnalisations quand vous devez modifier une interface ou ajouter des fonctionnalités propres à IoT Central à une capacité qui ne vous oblige pas à créer une version de votre modèle de capacité d’appareil. Vous pouvez personnaliser les champs quand le modèle de capacité est à l’état Brouillon ou Publié. Vous pouvez uniquement personnaliser les champs qui n’interrompent pas la compatibilité de l’interface. Vous pouvez par exemple :

- Personnaliser le nom complet et les unités d’une capacité.
- Ajouter une couleur par défaut à utiliser quand la valeur apparaît sur un graphique.
- Spécifier les valeurs initiales, minimales et maximales d’une propriété.

Vous ne pouvez pas personnaliser le nom ou le type de la capacité.


## <a name="create-views"></a>Créer des vues

En tant que générateur, vous pouvez personnaliser l’application pour présenter des informations pertinentes sur l’appareil capteur environnemental à un opérateur. Vos personnalisations permettent à l’opérateur de gérer l’appareil capteur environnemental raccordé à l’application. Vous pouvez créer deux types de vues permettant à un opérateur d’interagir avec des appareils :

* Formulaires pour voir et modifier les propriétés de l’appareil et du cloud
* Tableaux de bord pour visualiser les appareils

### <a name="generate-default-views"></a>Générer des vues par défaut

La génération de vues par défaut permet de visualiser rapidement les informations importantes de votre appareil. Vous pouvez avoir jusqu’à trois vues par défaut générées pour votre modèle d’appareil :

* La vue **Commandes** permet à votre opérateur d’envoyer des commandes à votre appareil.
* La vue **Vue d’ensemble** utilise des graphiques et des métriques pour afficher les données de télémétrie des appareils.
* La vue **À propos** affiche les propriétés de l’appareil.

Une fois que vous avez sélectionné **Générer les vues par défaut**, elles sont ajoutées automatiquement sous la section **Vues** de votre modèle d’appareil.

### <a name="configure-a-view-to-visualize-devices"></a>Configurer une vue pour visualiser des appareils

Un tableau de bord d’appareil permet à un opérateur de visualiser un appareil à l’aide de graphiques et de métriques. En tant que générateur, vous pouvez définir les informations qui s’affichent sur le tableau de bord d’un appareil. Vous pouvez définir plusieurs tableaux de bord pour les appareils. Pour créer un tableau de bord afin de visualiser la télémétrie du capteur environnemental, sélectionnez **Vues**, puis **Visualisation de l’appareil** :

1. Toutes les propriétés de l’appareil, les propriétés du cloud, les données de télémétrie et les options statiques sont listées sous **Propriétés**. Vous pouvez glisser-déplacer l’un de ces éléments dans la vue. Faites glisser la propriété **Niveau de luminosité** sur la vue. Vous pouvez configurer la vignette à l’aide de l’icône d’engrenage.

1. Pour ajouter un graphique des données de télémétrie, sélectionnez **Humidité** et **Température**, puis **Combiner**. Pour voir ce graphique dans un autre format tel qu’un graphique à secteurs ou un histogramme, sélectionnez le bouton **Changer de visualisation** en haut de la vignette.

1. Cliquez sur **Enregistrer** pour enregistrer votre vue :

Vous pouvez ajouter d’autres vignettes qui affichent d’autres propriétés ou valeurs de télémétrie. Vous pouvez également ajouter du texte statique, des liens et des images. Pour déplacer ou redimensionner une vignette sur le tableau de bord, déplacez le pointeur de souris sur la vignette, puis faites-la glisser vers un nouvel emplacement ou redimensionnez-la.

### <a name="add-a-device-form"></a>Ajouter un formulaire d’appareil

Un formulaire d’appareil permet à un opérateur de modifier les propriétés de l’appareil et les propriétés du cloud accessibles en écriture. En tant que générateur, vous pouvez définir plusieurs formulaires et choisir les propriétés de l’appareil et du cloud à afficher sur chaque formulaire. Vous pouvez également afficher des propriétés d’appareil en lecture seule sur un formulaire.

Pour créer un formulaire afin de voir et de modifier les propriétés d’un capteur environnemental

1. Accédez à **Vues** dans le modèle **Capteur environnemental**. Sélectionnez la vignette **Modification des données de l’appareil et du cloud** pour ajouter une nouvelle vue.

1. Entrez le nom de formulaire **Propriétés du capteur environnemental**.

1. Faites glisser les propriétés du cloud **Nom du client** et **Dernière date de service** sur la section existante du formulaire.

1. Sélectionnez les propriétés de l’appareil **Niveau de luminosité** et **État de l’appareil**. Sélectionnez ensuite **Ajouter une section**. Remplacez le titre de la section par **Propriétés du capteur**. Sélectionnez **Appliquer**.

1. Sélectionnez les propriétés d’appareil **Modèle d’appareil**, **Version du logiciel**, **Fabricant** et **Fabricant de processeur**. Sélectionnez ensuite **Ajouter une section**. Remplacez le titre de la section par **Propriétés de l’appareil**. Sélectionnez **Appliquer**.

1. Cliquez sur **Enregistrer** pour enregistrer votre vue.

## <a name="publish-device-template"></a>Publier le modèle d’appareil

Avant de pouvoir créer un capteur environnemental simulé ou connecter un capteur environnemental réel, vous devez publier votre modèle d’appareil.

Pour publier un modèle d’appareil

1. Accédez à votre modèle d’appareil à partir de la page **Modèles d’appareil**.

1. Sélectionnez **Publier**.

1. Dans la boîte de dialogue **Publier un modèle d’appareil**, choisissez **Publier** :

    ![Modèle publié](media/tutorial-define-device-type-pnp/publishedmodel.png)

Une fois qu’un modèle d’appareil a été publié, il est visible dans la page **Appareils** et par l’opérateur. Dans un modèle d’appareil publié, vous ne pouvez pas modifier un modèle de capacité d’appareil sans créer de nouvelle version. En revanche, vous pouvez effectuer des mises à jour des propriétés du cloud, des personnalisations et des vues dans un modèle d’appareil publié sans gestion des versions. Après avoir apporté des modifications, sélectionnez **Publier** pour envoyer ces modifications à votre opérateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

* Créer un modèle d’appareil
* Importer un modèle de capacité d’appareil
* Créer des propriétés du cloud.
* Créer des personnalisations.
* Définir une visualisation pour la télémétrie de l’appareil.
* Publier votre modèle d’appareil.

Maintenant que vous avez créé un modèle d’appareil dans votre application Azure IoT Central, nous vous suggérons d’effectuer l’étape suivante :

> [!div class="nextstepaction"]
> [Ajouter un appareil](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
