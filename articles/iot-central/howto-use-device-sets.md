---
title: Utiliser des ensembles d’appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, comment utiliser des ensembles d’appareils dans votre application Azure IoT Central.
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932c8315b5af22c3adf18de50cf03deaf6b2a53e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201147"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Utiliser des ensembles d’appareils dans votre application Azure IoT Central

Cet article décrit comment, en tant qu’opérateur, utiliser des ensembles d’appareils dans votre application Microsoft Azure IoT Central.

Un ensemble d’appareils est une liste d’appareils qui sont regroupés, car ils correspondent à certains critères spécifiés. Les ensembles d’appareils vous permettent de gérer, de visualiser et d’analyser des appareils à grande échelle en regroupant les appareils dans des groupes logiques plus petits. Par exemple, vous créez une liste de tous les appareils de climatisation à Seattle pour permettre à la technicienne de Seattle de rechercher tous les appareils dont elle est responsable. Cet article vous explique comment créer et configurer des ensembles d’appareils.

## <a name="create-a-device-set"></a>Créer un ensemble d’appareils

Pour créer un ensemble d’appareils :

1. Choisissez **Ensembles d’appareils** dans le menu de navigation gauche.

1. Cliquez sur **+ Nouveau**.

    ![Nouvel ensemble d’appareils](media/howto-use-device-sets/image1.png)

1. Donnez un nom à votre ensemble d’appareils qui soit unique dans toute l’application. Vous pouvez également ajouter une description. Un ensemble d’appareils peut contenir seulement des appareils d’un même modèle d’appareil. Choisissez le modèle à utiliser pour cet ensemble.

1. Créez la requête permettant d’identifier les appareils pour l’ensemble d’appareils en sélectionnant une propriété, un opérateur de comparaison et une valeur. Vous pouvez ajouter plusieurs requêtes, et les appareils qui répondent à **tous** les critères sont placés dans l’ensemble d’appareils. L’ensemble d’appareils que vous créez est accessible à toute personne ayant accès à l’application : toutes ces personnes peuvent donc voir, modifier ou supprimer l’ensemble d’appareils.

    ![Requête d’ensemble d’appareils](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > L’ensemble d’appareils est une requête dynamique. Chaque fois que vous visualisez la liste des appareils, des appareils différents peuvent figurer dans la liste. La liste varie selon les appareils qui répondent actuellement aux critères de la requête.

1. Choisissez **Enregistrer**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurer le tableau de bord pour votre ensemble d’appareils

Après avoir créé votre ensemble d’appareils, vous pouvez configurer ses **tableaux de bord**. Le **tableau de bord** est la page d’accueil où vous pouvez placer des images et des liens. Vous pouvez également ajouter des grilles qui répertorient les appareils de l’ensemble d’appareils.

1. Choisissez **Ensembles d’appareils** dans le menu de navigation gauche.

1. Choisissez l’onglet **Tableau de bord**.

1. Activez le **mode Conception**.

    ![Mode Conception activé](media/howto-use-device-sets/image3.png)

1. Pour plus d’informations sur l’ajout d’une image, consultez [Préparer et charger des images dans votre application Azure IoT Central](howto-prepare-images.md).

1. Ajoutez une vignette de lien :
    1. Choisissez **Lien** dans le volet droit.

        ![Choisir un lien](media/howto-use-device-sets/image6.png)

    1. Donnez un **Titre** à votre lien.
    1. Choisissez une URL à ouvrir quand l’utilisateur clique sur le lien.
    1. Donnez à votre lien une description qui s’affiche sous le **Titre**.
    1. Choisissez **Enregistrer**.

        ![Enregistrer le lien](media/howto-use-device-sets/image7.png)

    1. Vous pouvez déplacer et redimensionner la vignette de lien sur le **tableau de bord**.

1. Ajoutez une grille. Une grille est un tableau des appareils de l’ensemble d’appareils, avec les colonnes que vous choisissez.
    1. Choisissez **Grille** dans le volet droit.

        ![Choisir une grille](media/howto-use-device-sets/image8.png)

    1. Donnez un **Titre** à votre grille.
    1. Sélectionnez les colonnes à afficher en cliquant sur le bouton Paramètres. Dans le panneau qui s’affiche, choisissez la colonne à afficher et cliquez sur la flèche droite pour la sélectionner.
    1. Choisissez **OK**.
    1. Choisissez **Enregistrer**.

        ![Enregistrer la grille](media/howto-use-device-sets/image9.png)

    1. Faites glisser et déposez la grille pour la placer sur le **tableau de bord**.

    > [!NOTE]
    > Vous pouvez ajouter plusieurs images, liens et grilles.

1. Désactivez le **mode Conception**.

    ![Mode Conception désactivé](media/howto-use-device-sets/image10.png)

## <a name="configure-the-list-for-your-device-set"></a>Configurer la liste pour votre ensemble d’appareils

Après avoir créé votre ensemble d’appareils, vous pouvez configurer la **Liste**. La **Liste** montre tous les appareils de l’ensemble d’appareils, avec les colonnes que vous choisissez.

1. Choisissez **Ensembles d’appareils** dans le menu de navigation gauche.

1. Choisissez l’onglet **Liste**.

1. Choisissez **Options de colonne**.

    ![Options de colonne](media/howto-use-device-sets/image11.png)

1. Pour choisir les colonnes à afficher : sélectionnez une colonne, puis cliquez sur la flèche droite.

    ![Choisir une colonne](media/howto-use-device-sets/image12.png)

1. Choisissez **OK**.

## <a name="analytics"></a>Analytique

L’analytique des ensembles d’appareils est identique à celle de l’onglet d’analytique principal dans le menu de navigation gauche. Pour plus d’informations sur l’analytique, consultez [Comment créer une analytique](howto-create-analytics.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des ensembles d’appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](howto-create-telemetry-rules.md)
