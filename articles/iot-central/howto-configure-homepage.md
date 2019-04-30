---
title: Configurer le tableau de bord d’application Azure IoT Central | Microsoft Docs
description: Comme un générateur, découvrez comment configurer le tableau de bord par défaut Azure IoT Central application.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3168bbbf70c1ffeb3827482459febbcea256eda6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886954"
---
# <a name="configure-the-application-dashboard"></a>Configurer le tableau de bord d’application

Le **tableau de bord** est la page qui charge lorsque les utilisateurs qui ont accès à l’application accèdent à l’URL de l’application. Si vous avez sélectionné le **exemple Contoso** ou **exemple Devkits** modèle d’application pour créer votre application, votre application est un tableau de bord prédéfini. Si vous avez choisi le **Application personnalisée** modèle d’application, votre tableau de bord est vide.

> [!NOTE]
> Les utilisateurs peuvent également [créer leurs propres tableaux de bord personnels](howto-personalize-dashboard.md) à utiliser au lieu du tableau de bord application par défaut.

## <a name="add-tiles"></a>Ajouter des vignettes

La capture d’écran suivante montre le tableau de bord dans une application créée à partir de la **exemple Contoso** modèle. Pour personnaliser le tableau de bord par défaut pour votre application, sélectionnez **modifier** dans la partie supérieure droite de la page.

![Tableau de bord pour les applications basées sur le modèle « Exemple Contoso »](media/howto-configure-homepage/image1.png)

En sélectionnant **modifier**, ouvre le panneau de la bibliothèque du tableau de bord. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord.

![Bibliothèque de tableaux de bord](media/howto-configure-homepage/image2.png)

Par exemple, vous pouvez ajouter un **paramètres et propriétés** vignette pour afficher une sélection des valeurs de paramètres et les propriétés actuelles pour un appareil. Pour cela, sélectionnez d’abord un **modèle d’appareil**, puis sélectionnez une **instance d’appareil**. Après cela, indiquez un titre pour la vignette, puis sélectionnez un **paramètre** ou une **propriété** à afficher. La capture d’écran suivante montre les paramètres et les propriétés sélectionnées à ajouter à la vignette. Sélectionnez **fait** pour enregistrer la modification au tableau de bord.

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](media/howto-configure-homepage/image3.png)

Maintenant lorsqu’un opérateur affiche le tableau de bord application par défaut, ils voient la nouvelle vignette avec la **définir la température** définition pour l’appareil :

![Onglet « Tableau de bord » où figurent les paramètres et les propriétés de la vignette](media/howto-configure-homepage/image4.png)

Vous pouvez Explorer d’autres types de vignettes dans la bibliothèque pour découvrir comment personnaliser davantage le tableau de bord application par défaut.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer votre tableau de bord application par défaut Azure IoT Central, vous pouvez :

> [!div class="nextstepaction"]
> [Découvrir comment préparer et charger des images](howto-prepare-images.md)
