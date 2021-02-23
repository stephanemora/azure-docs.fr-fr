---
title: Créer des tableaux de bord Azure IoT Central | Microsoft Docs
description: Découvrez comment créer et gérer vos tableaux de bord.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6fc99470fdc52a2dc6553056f305226f8348550c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366719"
---
# <a name="create-and-manage-multiple-dashboards"></a>Créer et gérer plusieurs tableaux de bord

Lorsque vous accédez à votre application pour la première fois, la page **Tableau de bord** se charge. Un **générateur** dans votre application définit le tableau de bord par défaut pour tous les utilisateurs. Vous pouvez également créer votre propre tableau de bord d’application personnalisé. Vous pouvez disposer de plusieurs tableaux de bord, qui affichent différentes informations, et basculer de l’un à l’autre.

Si vous êtes **administrateur** de l’application, vous pouvez également créer jusqu’à dix tableaux de bord de niveau application à partager avec d’autres utilisateurs de l’application. Seuls les **administrateurs** peuvent créer, modifier et supprimer des tableaux de bord de niveau application.  

## <a name="create-dashboard"></a>Créer un tableau de bord

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Application personnalisée**. Vous pouvez remplacer le tableau de bord d’application par défaut par un tableau de bord personnel ou, si vous êtes administrateur, un autre tableau de bord de niveau application. Pour cela, sélectionnez l’option **+ Nouveau** en haut à gauche de la page.

> [!div class="mx-imgBorder"]
> ![Tableau de bord des applications basées sur le modèle « Application personnalisée »](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Si vous sélectionnez **+ Nouveau**, l’éditeur de tableau de bord s’ouvre. Celui-ci vous permet de nommer votre tableau de bord et de choisir des éléments dans la bibliothèque. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord.

> [!div class="mx-imgBorder"]
> ![Bibliothèque de tableaux de bord](media/howto-create-personal-dashboards/dashboard-library.png)

Si vous êtes **administrateur** de l’application, vous pouvez choisir de créer soit un tableau de bord de niveau personnel, soit un tableau de bord de niveau application. Si vous créez un tableau de bord de niveau personnel, vous seul pourrez le voir. Si vous créez un tableau de bord de niveau application, tous les utilisateurs de l’application pourront le voir. Après avoir entré un titre et sélectionné le type de tableau de bord que vous souhaitez créer, vous pouvez enregistrer pour ajouter des vignettes ultérieurement. Si vous avez déjà ajouté un modèle d’appareil et une instance d’appareil, vous pouvez d’ores et déjà créer votre première vignette.  

> [!div class="mx-imgBorder"]
> ![Formulaire « Configurer les détails de l’appareil » contenant les détails de Température](media/howto-create-personal-dashboards/device-details.png)

Par exemple, vous pouvez ajouter une vignette **Télémétrie** pour la température actuelle de l’appareil. Pour ce faire :

1. Sélectionnez un **modèle d’appareil**.
1. Sélectionnez dans **Appareils** l’appareil que vous souhaitez afficher sur une vignette du tableau de bord. La liste des propriétés de l’appareil utilisables sur la vignette s’affiche alors.
1. Pour créer la vignette sur le tableau de bord, sélectionnez **Température** et glissez-déplacez-la dans la zone du tableau de bord. Vous pouvez également cocher la case à côté de **Température** et sélectionner **Ajouter une vignette**. La capture d’écran suivante montre comment sélectionner un modèle d’appareil et un appareil, puis créer une vignette Télémétrie de température sur le tableau de bord.
1. Sélectionnez **Enregistrer** en haut à gauche pour enregistrer les modifications apportées au tableau de bord.

> [!div class="mx-imgBorder"]
> ![Onglet « Tableau de bord » contenant les détails de la vignette Température](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Lorsque vous affichez votre tableau de bord personnel, vous voyez maintenant la nouvelle vignette, qui indique le paramètre **Température** de l’appareil :

> [!div class="mx-imgBorder"]
> ![Capture d’écran montrant la nouvelle vignette avec le paramètre Températures de l’appareil.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Vous pouvez explorer d’autres types de mosaïque dans la bibliothèque, afin de découvrir comment personnaliser davantage vos tableaux de bord personnels.

Pour savoir plus en détails comment utiliser les vignettes dans Azure IoT Central, voir [Ajouter des vignettes à un tableau de bord](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Gérer les tableaux de bord

Vous pouvez disposer de plusieurs tableaux de bord personnels et basculer de l’un à l’autre, ou choisir l’un des tableaux de bord d’application par défaut :

> [!div class="mx-imgBorder"]
> ![Basculer d’un tableau de bord à un autre](media/howto-create-personal-dashboards/switch-dashboards.png)

Vous pouvez modifier vos tableaux de bord personnels et supprimer ceux dont vous n’avez plus besoin. Si vous êtes **administrateur**, vous pouvez également modifier ou supprimer des tableaux de bord de niveau application.

> [!div class="mx-imgBorder"]
> ![Supprimer des tableaux de bord](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer et gérer des tableaux de bord personnels, vous pouvez [découvrir comment gérer vos préférences d’application](howto-manage-preferences.md).
