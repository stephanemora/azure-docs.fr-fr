---
title: Créer des tableaux de bord personnels Azure IoT Central | Microsoft Docs
description: En tant qu’utilisateur, découvrez comment créer et gérer vos tableaux de bord personnels.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: fb74669dcaa802ad06a9c4dff3ffdf25726f518c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518516"
---
# <a name="create-and-manage-personal-dashboards"></a>Créer et gérer des tableaux de bord personnels

Le **tableau de bord** est la page se charge lorsque vous accédez tout d’abord à votre application. Un **Générateur** dans votre application définit le tableau de bord application par défaut pour tous les utilisateurs. Vous pouvez remplacer ce tableau de bord par défaut par votre propre, tableau de bord des applications personnalisées. Vous pouvez avoir plusieurs tableaux de bord qui affichent des données différentes et basculer entre elles.

## <a name="create-dashboard"></a>Créer un tableau de bord

La capture d’écran suivante montre le tableau de bord dans une application créée à partir de la **exemple Contoso** modèle. Vous pouvez remplacer le tableau de bord application par défaut par un tableau de bord personnel. Pour ce faire, sélectionnez **+ nouveau** dans la partie supérieure droite de la page.

![Tableau de bord pour les applications basées sur le modèle « Exemple Contoso »](media/howto-personalize-dashboard/defaultdashboard.png)

En sélectionnant **+ nouveau**, ouvre l’éditeur de tableau de bord. Dans l’éditeur, vous pouvez nommer votre tableau de bord et a décidé d’éléments dans la bibliothèque. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord.

![Bibliothèque de tableaux de bord](media/howto-personalize-dashboard/dashboardeditor.png)

Par exemple, vous pouvez ajouter un **paramètres et propriétés** vignette pour afficher les valeurs de propriétés et les paramètres pour un des appareils que vous gérez. Pour cela, sélectionnez d’abord un **modèle d’appareil**, puis sélectionnez une **instance d’appareil**. Nommez ensuite la vignette et sélectionnez un **paramètre** ou un **propriété** à afficher. La capture d’écran suivante montre le **vitesse de ventilateur** paramètre sélectionné à ajouter à la vignette. Sélectionnez **fait** pour enregistrer la modification de votre tableau de bord.

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](media/howto-personalize-dashboard/dashboardsetting.png)

Maintenant lorsque vous affichez votre tableau de bord personnel, vous voyez la nouvelle vignette avec la **vitesse de ventilateur** définition pour l’appareil :

![Onglet « Tableau de bord » où figurent les paramètres et les propriétés de la vignette](media/howto-personalize-dashboard/personaldashboard.png)

Vous pouvez Explorer d’autres types de vignettes dans la bibliothèque pour découvrir comment personnaliser davantage vos tableaux de bord personnels.

## <a name="manage-dashboards"></a>Gérer les tableaux de bord

Vous pouvez avoir plusieurs tableaux de bord personnels et passer à l’autre, ou cliquez sur le tableau de bord application par défaut :

![Tableau de bord de commutateur](media/howto-personalize-dashboard/switchdashboards.png)

Vous pouvez modifier vos tableaux de bord personnels et supprimez celles que vous n’avez plus besoin :

![Supprimer le tableau de bord](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer et gérer des tableaux de bord personnels, vous pouvez :

> [!div class="nextstepaction"]
> [Découvrez comment gérer vos préférences de l’application](howto-manage-preferences.md)
