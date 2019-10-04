---
title: Créer des tableaux de bord personnels Azure IoT Central | Microsoft Docs
description: En tant qu’utilisateur, découvrez comment créer et gérer vos tableaux de bord personnels.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c048ae8c0daba0e467a9243f4dd83f8d95921e10
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502649"
---
# <a name="create-and-manage-personal-dashboards"></a>Créer et gérer des tableaux de bord personnels

Lorsque vous accédez à votre application pour la première fois, la page **Tableau de bord** se charge. Un **générateur** dans votre application définit le tableau de bord par défaut pour tous les utilisateurs. Vous pouvez le remplacer par un tableau de bord personnalisé. Vous pouvez disposer de plusieurs tableaux de bord, qui affichent différentes informations, et basculer de l’un à l’autre.

## <a name="create-dashboard"></a>Créer un tableau de bord

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Exemple Contoso**. Vous pouvez remplacer le tableau de bord par défaut de l’application par un tableau de bord personnalisé. Pour ce faire, sélectionnez l’option **+ Nouveau** figurant dans la partie supérieure droite de la page.

![Tableau de bord pour les applications basées sur le modèle « Exemple Contoso »](media/howto-personalize-dashboard/defaultdashboard.png)

En sélectionnant **+ Nouveau**, vous ouvrez l’éditeur de tableau de bord. Celui-ci vous permet de nommer votre tableau de bord et de choisir des éléments dans la bibliothèque. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord.

![Bibliothèque de tableaux de bord](media/howto-personalize-dashboard/dashboardeditor.png)

Par exemple, vous pouvez ajouter une mosaïque **Paramètres et propriétés de l’appareil** pour afficher les valeurs de paramètres et de propriétés pour l’un des appareils que vous gérez. Pour cela, sélectionnez d’abord un **modèle d’appareil**, puis sélectionnez une **instance d’appareil**. Ensuite, indiquez un titre pour la mosaïque, puis sélectionnez un **paramètre** ou une **propriété** à afficher. La capture d’écran suivante montre le paramètre **Vitesse du ventilateur**, sélectionné de façon à être ajouté à la mosaïque. Sélectionnez **Terminé** pour enregistrer la modification apportée à votre tableau de bord.

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](media/howto-personalize-dashboard/dashboardsetting.png)

Désormais, lorsque vous affichez votre tableau de bord personnel, vous voyez la nouvelle mosaïque, qui indique le paramètre **Vitesse du ventilateur** associé à l’appareil :

![Onglet « Tableau de bord » où figurent les paramètres et les propriétés de la vignette](media/howto-personalize-dashboard/personaldashboard.png)

Vous pouvez explorer d’autres types de mosaïque dans la bibliothèque, afin de découvrir comment personnaliser davantage vos tableaux de bord personnels.

Pour en savoir plus sur l’utilisation des mosaïque dans Azure IoT Central, voir [Utiliser des vignettes de tableau de bord](howto-use-tiles.md).

## <a name="manage-dashboards"></a>Gérer les tableaux de bord

Vous pouvez disposer de plusieurs tableaux de bord personnels et basculer de l’un à l’autre, ou sélectionner le tableau de bord par défaut :

![Changer de tableau de bord](media/howto-personalize-dashboard/switchdashboards.png)

Vous pouvez modifier vos tableaux de bord personnels et supprimer ceux dont vous n’avez plus besoin :

![Supprimer un tableau de bord](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez désormais comment créer et gérer des tableaux de bord personnels, vous pouvez découvrir comment effectuer l’opération suivante :

> [!div class="nextstepaction"]
> [Gérer les préférences des applications](howto-manage-preferences.md)
