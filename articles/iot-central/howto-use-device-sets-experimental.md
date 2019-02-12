---
title: Utiliser des ensembles d’appareils dans votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, comment utiliser des ensembles d’appareils dans votre application Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: dd8fa36acaf3f4871d63200a4863778180e9be1a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771906"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>Utiliser des ensembles d’appareils dans votre application Azure IoT Central

Cet article décrit comment, en tant qu’opérateur, utiliser des ensembles d’appareils dans votre application Azure IoT Central.

Un ensemble d’appareils est une liste d’appareils qui sont regroupés, car ils correspondent à certains critères spécifiés. Les ensembles d’appareils vous permettent de gérer, de visualiser et d’analyser des appareils à grande échelle en regroupant les appareils dans des groupes logiques plus petits. Par exemple, vous créez une liste de tous les appareils de climatisation à Seattle pour permettre à la technicienne de Seattle de rechercher tous les appareils dont elle est responsable. Cet article vous explique comment créer et configurer des ensembles d’appareils.

## <a name="create-a-device-set"></a>Créer un ensemble d’appareils

Pour créer un ensemble d’appareils :

1. Choisissez **Ensembles d’appareils** dans le menu de navigation gauche.

1. Cliquez sur **+ Nouveau**.

    ![Nouvel ensemble d’appareils](media/howto-use-device-sets-experimental/image1.png)

1. Donnez un nom à votre ensemble d’appareils qui soit unique dans toute l’application. Vous pouvez également ajouter une description. Un ensemble d’appareils peut contenir seulement des appareils d’un même modèle d’appareil. Choisissez le modèle à utiliser pour cet ensemble.

1. Créez la requête permettant d’identifier les appareils pour l’ensemble d’appareils en sélectionnant une propriété, un opérateur de comparaison et une valeur. Vous pouvez ajouter plusieurs requêtes, et les appareils qui répondent à **tous** les critères sont placés dans l’ensemble d’appareils. L’ensemble d’appareils que vous créez est accessible à toute personne ayant accès à l’application : toutes ces personnes peuvent donc voir, modifier ou supprimer l’ensemble d’appareils.

    ![Requête d’ensemble d’appareils](media/howto-use-device-sets-experimental/image2.png)

    > [!NOTE]
    > L’ensemble d’appareils est une requête dynamique. Chaque fois que vous visualisez la liste des appareils, des appareils différents peuvent figurer dans la liste. La liste varie selon les appareils qui répondent actuellement aux critères de la requête.

1. Choisissez **Enregistrer**.

## <a name="configure-the-dashboard-for-your-device-set"></a>Configurer le tableau de bord pour votre ensemble d’appareils

Après avoir créé votre ensemble d’appareils, vous pouvez configurer ses **tableaux de bord**. Le **tableau de bord** est la page d’accueil où vous pouvez placer des images et des liens. Vous pouvez également ajouter des grilles qui répertorient les appareils de l’ensemble d’appareils.

1. Choisissez **Ensembles d’appareils** dans le menu de navigation gauche.

1. Sélectionnez votre ensemble d'appareils.

1. Sélectionnez l'onglet **Tableau de bord** .

1. Cliquez sur **Modifier**.

    ![Mode Conception activé](media/howto-use-device-sets-experimental/image3.png)

1. Pour plus d’informations sur l’ajout d’une image, consultez [Préparer et charger des images dans votre application Azure IoT Central](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Ajoutez une vignette de lien :
    1. Choisissez **Lien** dans le volet droit.
    1. Donnez un **Titre** à votre lien.
    1. Choisissez une URL à ouvrir quand l’utilisateur clique sur le lien.
    1. Donnez à votre lien une description qui s’affiche sous le **Titre**.
    1. Choisissez **Enregistrer**.

        ![Enregistrer le lien](media/howto-use-device-sets-experimental/image7.png)

    1. Vous pouvez déplacer et redimensionner la vignette de lien sur le **tableau de bord**.

1. Ajoutez une grille. Une grille est un tableau des appareils de l’ensemble d’appareils, avec les colonnes que vous choisissez.
    1. Choisissez **Grille** dans le volet droit.
    1. Donnez un **Titre** à votre grille.
    1. Sélectionnez les colonnes à afficher en cliquant sur **Ajouter/Supprimer**. Dans le panneau qui s’affiche, choisissez la colonne à afficher et cliquez sur la flèche droite pour la sélectionner.
    1. Choisissez **OK**.
    1. Choisissez **Enregistrer**.

        ![Enregistrer la grille](media/howto-use-device-sets-experimental/image9.png)

    1. Faites glisser et déposez la grille pour la placer sur le **tableau de bord**.

        > [!NOTE]
        > Vous pouvez ajouter plusieurs images, liens et grilles.
  
    1. Cliquez sur **Done**.

### <a name="configuring-location-map-in-your-device-sets-dashboard"></a>Configuration de la carte des emplacements dans votre tableau de bord d’ensembles d’appareils

Vous pouvez ajouter une carte des emplacements pour visualiser l’emplacement de vos ensembles d’appareils sur une carte.

Pour ajouter une carte des emplacements à votre tableau de bord d’ensembles d’appareils, vous devez avoir configuré une propriété d’emplacement dans votre modèle d’appareil. Consultez [Créer une propriété d’emplacement optimisée par Azure Maps](howto-set-up-template-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

1. Dans votre tableau de bord des ensembles d’appareils, sélectionnez le mappage dans la bibliothèque.
2. Donnez un titre et choisissez la propriété d’emplacement que vous avez déjà configurée dans votre propriété d’appareil.
3. Enregistrez pour afficher la mosaïque de la carte indiquant l’emplacement de vos appareils dans l’ensemble d’appareils.
4. À présent, lorsqu’un opérateur affiche le tableau de bord des ensembles d’appareils, il peut voir toutes les mosaïques que vous avez configurées, y compris la carte des emplacements, et visualiser ainsi en un clin d’œil l’emplacement de tous les appareils. 
    
> [!NOTE] 
> Vous pouvez redimensionner la carte à la taille désirée. Un clic sur une épingle sur la carte affiche les informations sur l’appareil, ainsi que son nom et son emplacement. Vous pouvez cliquer sur le menu contextuel pour accéder à la page de propriétés de l’appareil.  

## <a name="configure-the-list-for-your-device-set"></a>Configurer la liste pour votre ensemble d’appareils

Après avoir créé votre ensemble d’appareils, vous pouvez configurer la **Liste**. La **Liste** montre tous les appareils de l’ensemble d’appareils, avec les colonnes que vous choisissez.

1. Choisissez **Ensembles d’appareils** dans le menu de navigation gauche.

1. Choisissez l’onglet **Liste**.

1. Choisissez **Options de colonne**.

    ![Options de colonne](media/howto-use-device-sets-experimental/image11.png)

1. Pour choisir les colonnes à afficher : sélectionnez une colonne, puis cliquez sur la flèche droite.

    ![Choisir une colonne](media/howto-use-device-sets-experimental/image12.png)

1. Choisissez **OK**.

## <a name="analytics"></a>Analytics

L’analytique des ensembles d’appareils est identique à celle de l’onglet d’analytique principal dans le menu de navigation gauche. Pour plus d’informations sur l’analytique, consultez [Comment créer une analytique](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à utiliser des ensembles d’appareils dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Guide pratique pour créer des règles de télémétrie](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
