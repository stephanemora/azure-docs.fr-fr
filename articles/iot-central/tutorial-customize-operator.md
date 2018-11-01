---
title: Personnaliser les vues de l’opérateur dans Azure IoT Central | Microsoft Docs
description: En tant que générateur, personnalisez les vues de l’opérateur dans votre application Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 070a6ee4bc712b2dbec598cdb9be5c324895c033
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154979"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Didacticiel : Personnaliser la vue de l’opérateur d’Azure IoT Central

Ce didacticiel vous montre, en tant que générateur, comment personnaliser la vue de l’opérateur de votre application. Lorsque vous apportez une modification à l’application en tant que générateur, vous pouvez afficher un aperçu de vue de l’opérateur dans l’application Microsoft Azure IoT Central.

Dans ce didacticiel, vous personnalisez l’application pour afficher des informations pertinentes sur le climatiseur raccordé pour un opérateur. Vos personnalisations permettent à l’opérateur de gérer les climatiseurs raccordés à l’application.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer le tableau de bord de votre appareil
> * Configurer la disposition des paramètres de votre appareil
> * Configurer la disposition des propriétés de votre appareil
> * Afficher un aperçu de l’appareil en tant qu’opérateur
> * Configurer votre page d’accueil par défaut
> * Afficher un aperçu de la page d’accueil par défaut en tant qu’opérateur

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez effectuer les deux didacticiels précédents :

* [Définir un nouveau type d’appareil dans votre application Azure IoT Central](tutorial-define-device-type.md).
* [Configurer des règles et des actions pour votre appareil](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Configurer le tableau de bord de votre appareil

En tant que générateur, vous pouvez définir les informations qui s’affichent sur le tableau de bord d’un appareil. Dans le didacticiel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md), vous avez ajouté à un graphique linéaire et d’autres informations sur le tableau de bord **Climatiseur raccordé-1**.

1. Pour modifier le modèle d’appareil **Climatiseur raccordé**, choisissez **Explorer** dans le menu de navigation gauche :

    ![Page Explorateur](media/tutorial-customize-operator/explorer.png)

2. Pour commencer à personnaliser le tableau de bord de votre climatiseur raccordé, sélectionnez le modèle d’appareil **Climatiseur raccordé (1.0.0)**. Choisissez l’appareil **Climatiseur raccordé-1** que vous avez créé dans le didacticiel [Définir un nouveau type d’appareil dans votre application](tutorial-define-device-type.md) :

    ![Sélectionner le climatiseur raccordé](media/tutorial-customize-operator/selectdevice.png)

    Lorsque vous accédez à un appareil, tel que **Climatiseur raccordé-1**, vous pouvez sélectionner **Modifier un modèle** pour apporter une modification au modèle sous-jacent. Pour plus d’informations, consultez [Créer une nouvelle version de modèle d’appareil](howto-version-devicetemplate.md).

3. Pour modifier le tableau de bord, choisissez **Tableau de bord**, puis sélectionnez **Modifier un modèle** :

    ![Page du tableau de bord de modèle d’appareil](media/tutorial-customize-operator/dashboard.png)

4. Pour ajouter une vignette d’indicateur de performance clé (KPI) au tableau de bord, choisissez **KPI** :

    ![Ajouter un KPI](media/tutorial-customize-operator/addkpi.png)

    Pour définir le KPI, utilisez les informations du tableau suivant :

    | Paramètre     | Valeur |
    | ----------- | ----- |
    | NOM        | Température maximale |
    | Mesure | température |
    | Agrégation | Maximale |
    | Période  | 1 semaine précédente |

5. Choisissez **Enregistrer**. Vous pouvez maintenant voir la vignette du KPI sur le tableau de bord :

    ![Vignette de KPI](media/tutorial-customize-operator/temperaturekpi.png)

6. Pour déplacer ou redimensionner une vignette sur le tableau de bord, déplacez le pointeur de souris sur la vignette. Vous pouvez faire glisser la vignette vers un nouvel emplacement ou la redimensionner :

    ![Modifier la disposition du tableau de bord](media/tutorial-customize-operator/dashboardlayout.png)

7. Cliquez sur **Terminé** lorsque vous avez terminé vos modifications.

## <a name="configure-your-settings-layout"></a>Configurer la disposition de vos paramètres

En tant que générateur, vous pouvez également configurer la vue de l’opérateur des paramètres d’appareil. Un opérateur utilise la page des paramètres de l’appareil pour configurer un appareil. Par exemple, un opérateur utilise la page des paramètres pour définir la température cible d’un climatiseur connecté.

1. Pour modifier la disposition des paramètres de votre climatiseur raccordé, choisissez **Paramètres**, puis sélectionnez **Modifier un modèle** :

    ![Page Paramètres](media/tutorial-customize-operator/settings.png)

2. Vous pouvez déplacer et redimensionner les vignettes de paramètres :

    ![Modifier la disposition des paramètres](media/tutorial-customize-operator/settingslayout.png)

3. Cliquez sur **Terminé** lorsque vous avez terminé vos modifications.

> [!NOTE]
> En mode **Modifier un modèle**, vous ne pouvez pas modifier les valeurs des paramètres.

## <a name="configure-your-properties-layout"></a>Configurer la disposition de vos propriétés

En plus du tableau de bord et des paramètres, vous pouvez également configurer la vue de l’opérateur des propriétés de l’appareil. Un opérateur utilise la page des propriétés de l’appareil pour gérer les métadonnées de l’appareil. Par exemple, un opérateur utilise la page des propriétés pour afficher le numéro de série d’un appareil ou mettre à jour les informations de contact du fabricant.

1. Pour modifier la disposition des propriétés de votre climatiseur raccordé, choisissez **Propriétés**, puis sélectionnez **Modifier un modèle** :

    ![Page Propriétés](media/tutorial-customize-operator/properties.png)

2. Vous pouvez déplacer et redimensionner les champs de propriétés :

    ![Modifier la mise en page des propriétés](media/tutorial-customize-operator/propertieslayout.png)

3. Cliquez sur **Terminé** lorsque vous avez terminé vos modifications.

> [!NOTE]
> En mode **Modifier un modèle**, vous ne pouvez pas modifier les valeurs des propriétés.

## <a name="preview-the-connected-air-conditioner-device-as-an-operator"></a>Afficher un aperçu du climatiseur raccordé en tant qu’opérateur

En mode **Modifier un modèle**, vous pouvez personnaliser les pages du tableau de bord, des paramètres et des propriétés pour un opérateur. Si vous n’êtes pas en mode **Modifier un modèle**, vous pouvez afficher l’application en tant qu’opérateur.

1. Pour afficher votre climatiseur connecté en tant qu’opérateur, cliquez sur **Terminé** pour arrêter la modification du modèle, puis revenez à l’affichage de l’opérateur pour cet appareil.

2. Pour mettre à jour l’emplacement de cet appareil, modifiez la valeur dans la vignette Emplacement et choisissez **Enregistrer** :

    ![Modifier une valeur de propriété](media/tutorial-customize-operator/editproperty.png)

3. Pour envoyer un paramètre à votre climatiseur raccordé, choisissez **Paramètres**, modifiez une valeur de paramètre dans une vignette, puis choisissez **Mettre à jour** :

    ![Envoyer un paramètre à l’appareil](media/tutorial-customize-operator/sendsetting.png)

    Lorsque l’appareil accuse réception de la nouvelle valeur de paramètre, le paramètre s’affiche comme étant **synchronisés** sur la vignette.

4. En tant qu’opérateur, vous pouvez afficher le tableau de bord de l’appareil tel qu’il est configuré par le générateur :

    ![Vue de l’opérateur du tableau de bord de l’appareil](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-home-page"></a>Configurer la page d’accueil par défaut

Lorsqu’un générateur ou un opérateur se connecte à une application Azure IoT Central, il voit une page d’accueil. En tant que générateur, vous pouvez configurer le contenu de cette page d’accueil afin d’inclure le contenu le plus utile et pertinent pour un opérateur.

1. Pour personnaliser la page d’accueil par défaut, accédez à la page **Accueil**, puis sélectionnez **Modifier**, en haut à droite de la page. Lorsque vous sélectionnez **Modifier**, un panneau glisse depuis la droite et présente une liste d’objets que vous pouvez ajouter à votre page d’accueil.

    ![Page Générateur d’applications](media/tutorial-customize-operator/builderhome.png)

2. Pour personnaliser la page d’accueil, ajoutez des vignettes à partir de la **bibliothèque**. Choisissez **Link** (Lier) et ajoutez les détails sur le site web de votre organisation. Puis choisissez **Enregistrer** :

    ![Ajouter un lien vers la page d'accueil](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Vous pouvez également ajouter des liens vers des pages de votre application Azure IoT Central. Par exemple, vous pouvez ajouter un lien vers le tableau de bord d’un appareil ou une page de paramètres.

3. Si vous le souhaitez, choisissez **Image** et charger une image à afficher sur votre page d’accueil. Une image peut inclure une URL à laquelle vous pouvez accéder lorsque vous cliquez dessus :

    ![Ajouter une image à la page d’accueil](media/tutorial-customize-operator/addimage.png)

    Pour en savoir plus, consultez [Préparer et charger des images dans votre application Azure IoT Central](howto-prepare-images.md).

## <a name="preview-the-default-home-page-as-an-operator"></a>Afficher un aperçu de la page d’accueil par défaut en tant qu’opérateur

Pour afficher un aperçu de la page d’accueil en tant qu’opérateur, sélectionnez **Terminé** en haut à droite de la page

![Activer/désactiver le mode Création](media/tutorial-customize-operator/operatorviewhome.png)

Vous pouvez cliquer sur le lien et des vignettes d’image pour accéder aux URL que vous avez définies en tant que générateur.

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

* [Surveiller vos appareils (en tant qu’opérateur)](tutorial-monitor-devices.md)
* [Ajouter un appareil à votre application (en tant qu’opérateur et développeur d’appareil)](tutorial-add-device.md)
