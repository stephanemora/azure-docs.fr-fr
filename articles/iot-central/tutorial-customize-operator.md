---
title: Personnaliser les vues de l’opérateur dans Azure IoT Central | Microsoft Docs
description: En tant que générateur, personnalisez les vues de l’opérateur dans votre application Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: dc3543af0a10c83d8ca212957a6345c8a70cd58b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279939"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view-new-ui-design"></a>Didacticiel : Personnaliser la vue de l’opérateur d’Azure IoT Central (nouvelle conception d’interface utilisateur)

Ce didacticiel vous montre, en tant que générateur, comment personnaliser la vue de l’opérateur de votre application. Lorsque vous apportez une modification à l’application en tant que générateur, vous pouvez afficher un aperçu de vue de l’opérateur dans l’application Microsoft Azure IoT Central.

Dans ce didacticiel, vous personnalisez l’application pour afficher des informations pertinentes sur le climatiseur raccordé pour un opérateur. Vos personnalisations permettent à l’opérateur de gérer les climatiseurs raccordés à l’application.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le tableau de bord de votre appareil
> * Configurer la disposition des paramètres de votre appareil
> * Configurer la disposition des propriétés de votre appareil
> * Afficher un aperçu de l’appareil en tant qu’opérateur
> * Configurer votre tableau de bord d’application par défaut
> * Afficher un aperçu du tableau de bord d’application par défaut en tant qu’opérateur

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer les deux didacticiels précédents :

* [Définir un nouveau type d’appareil dans votre application Azure IoT Central](tutorial-define-device-type.md).
* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurer le tableau de bord de votre appareil

En tant que générateur, vous pouvez définir les informations qui s’affichent sur le tableau de bord d’un appareil. Dans le tutoriel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md), vous avez ajouté un graphique linéaire et d’autres informations au tableau de bord **Climatiseur connecté**.

1. Pour modifier le modèle d’appareil **Climatiseur connecté**, choisissez **Modèles d’appareil** dans le menu de navigation de gauche :

    ![Page Modèles d’appareil](media/tutorial-customize-operator/devicetemplates.png)

2. Pour personnaliser le tableau de bord votre appareil, sélectionnez le modèle d’appareil **Climatiseur connecté (1.0.0)** que vous avez créé dans le tutoriel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md).

3. Pour modifier le tableau de bord, sélectionnez l’onglet **Tableau de bord**.

4. Pour ajouter une vignette d’indicateur de performance clé (KPI) au tableau de bord, choisissez **KPI** :

    Pour définir le KPI, utilisez les informations du tableau suivant :

    | Paramètre     | Valeur |
    | ----------- | ----- |
    | Nom        | Température maximale |
    | Période  | 1 semaine précédente |
    | Type de mesure | Télémétrie |
    | Mesure | température |
    | Agrégation | Maximale |
    | Visibilité  | activé |

    ![Ajouter un KPI](media/tutorial-customize-operator/addkpi.png)

5. Sélectionnez **Enregistrer**. Vous pouvez maintenant voir la vignette du KPI sur le tableau de bord :

    ![Vignette de KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Pour déplacer ou redimensionner une vignette sur le tableau de bord, déplacez le pointeur de souris sur la vignette. Vous pouvez faire glisser la vignette vers un nouvel emplacement ou la redimensionner.

## <a name="configure-your-settings-layout"></a>Configurer la disposition de vos paramètres

En tant que générateur, vous pouvez également configurer la vue de l’opérateur des paramètres d’appareil. Un opérateur utilise l’onglet des paramètres de l’appareil pour configurer un appareil. Par exemple, un opérateur utilise l’onglet des paramètres pour définir la température cible du climatiseur connecté.

1. Pour modifier la disposition des paramètres de votre climatiseur connecté, choisissez l’onglet **Paramètres**.

2. Vous pouvez déplacer et redimensionner les vignettes de paramètres :

    ![Modifier la disposition des paramètres](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>Configurer la disposition de vos propriétés

En plus du tableau de bord et des paramètres, vous pouvez également configurer la vue de l’opérateur des propriétés de l’appareil. Un opérateur utilise l’onglet des propriétés de l’appareil pour gérer les métadonnées de l’appareil. Par exemple, un opérateur utilise l’onglet des propriétés pour afficher le numéro de série d’un appareil ou mettre à jour les informations de contact du fabricant.

1. Pour modifier la disposition des propriétés de votre climatiseur connecté, choisissez l’onglet **Propriétés**.

2. Vous pouvez déplacer et redimensionner les champs de propriétés :

    ![Modifier la mise en page des propriétés](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Afficher un aperçu de l’appareil

Vous utilisez l’onglet **Modèles d’appareil** afin de personnaliser les onglets du tableau de bord, des paramètres et des propriétés pour un opérateur. Vous utilisez l’onglet **Device Explorer** pour voir et utiliser le modèle d’appareil.

1. Pour voir et utiliser le modèle du climatiseur connecté en tant qu’opérateur, accédez à la page **Device Explorer** et choisissez l’appareil simulé qu’IoT Central a généré à partir de votre modèle :

    ![Voir et utiliser le modèle d’appareil](media/tutorial-customize-operator/usetemplate.png)

2. Pour mettre à jour l’emplacement de cet appareil, choisissez **Propriétés** et modifiez la valeur dans la vignette Emplacement. Sélectionnez ensuite **Enregistrer** :

    ![Modifier une valeur de propriété](media/tutorial-customize-operator/editproperty.png)

3. Pour envoyer un paramètre à votre climatiseur raccordé, choisissez **Paramètres**, modifiez une valeur de paramètre dans une vignette, puis choisissez **Mettre à jour** :

    ![Envoyer un paramètre à l’appareil](media/tutorial-customize-operator/sendsetting.png)

    Lorsque l’appareil accuse réception de la nouvelle valeur de paramètre, le paramètre s’affiche comme étant **synchronisés** sur la vignette.

4. En tant qu’opérateur, vous pouvez afficher le tableau de bord de l’appareil tel qu’il est configuré par le générateur :

    ![Vue de l’opérateur du tableau de bord de l’appareil](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Configurer le tableau de bord par défaut

Lorsqu’un créateur ou un opérateur se connecte à une application Azure IoT Central, il voit le tableau de bord de l’application. En tant que créateur, vous pouvez configurer le contenu du tableau de bord par défaut afin d’y ajouter le contenu le plus utile et le plus pertinent pour les opérateurs.

> [!NOTE]
> Les utilisateurs peuvent également créer leurs propres tableaux de bord personnels et en choisir un comme tableau de bord par défaut.

1. Pour personnaliser le tableau de bord par défaut, accédez à la page **Tableau de bord**, puis sélectionnez **Modifier** en haut à droite de la page. Un panneau s’affiche avec une bibliothèque d’objets que vous pouvez ajouter au tableau de bord.

    ![Page du tableau de bord](media/tutorial-customize-operator/builderhome.png)

2. Pour personnaliser le tableau de bord, ajoutez des vignettes à partir de la **Bibliothèque**. Choisissez **Link** (Lier) et ajoutez les détails sur le site web de votre organisation. Puis choisissez **Enregistrer** :

    ![Ajouter un lien au tableau de bord](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Vous pouvez également ajouter des liens vers des pages de votre application Azure IoT Central. Par exemple, vous pouvez ajouter un lien vers le tableau de bord d’un appareil ou une page de paramètres.

3. Si vous le souhaitez, choisissez **Image** et chargez une image à afficher sur votre tableau de bord. Les images peuvent comprendre une URL à laquelle vous pouvez accéder lorsque vous cliquez dessus :

    ![Ajouter une image au tableau de bord](media/tutorial-customize-operator/addimage.png)

    Pour en savoir plus, consultez [Préparer et charger des images dans votre application Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-dashboard"></a>Aperçu du tableau de bord

Pour afficher un aperçu du tableau de bord en tant qu’opérateur, sélectionnez **Terminé** en haut à droite de la page.

![Activer/désactiver le mode Création](media/tutorial-customize-operator/operatorviewhome.png)

Vous pouvez sélectionner le lien et des vignettes d’image pour accéder aux URL que vous avez définies en tant que créateur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à personnaliser la vue de l’opérateur de l’application.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Configurer le tableau de bord de votre appareil
> * Configurer la disposition des paramètres de votre appareil
> * Configurer la disposition des propriétés de votre appareil
> * Afficher un aperçu de l’appareil en tant qu’opérateur
> * Configurer votre page d’accueil par défaut
> * Afficher un aperçu de la page d’accueil par défaut en tant qu’opérateur

Maintenant que vous avez appris à personnaliser la vue de l’opérateur de l’application, les étapes suivantes suggérées consistent à :

* [Superviser vos appareils (en tant qu’opérateur)](tutorial-monitor-devices.md)
* [Ajouter un nouvel appareil à votre application (en tant qu’opérateur et développeur d’appareil)](tutorial-add-device.md)