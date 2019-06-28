---
title: Configurer le tableau de bord de l’application d’Azure IoT Central | Microsoft Docs
description: En tant que concepteur, découvrez comment configurer le tableau de bord de l’application d’Azure IoT Central par défaut.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b1edd4777839c947cf8f60bb6857f939553e2603
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467625"
---
# <a name="configure-the-application-dashboard"></a>Configurer le tableau de bord de l’application

Le **tableau de bord** est la page qui se charge quand les utilisateurs autorisés accèdent à l’URL de l’application. Si vous avez sélectionné les modèles d’application **Exemple Contoso** ou **Exemple Devkits** pour créer votre application, votre application comporte un tableau de bord prédéfini. Si vous avez choisi le modèle d’application **Application personnalisée**, votre tableau de bord est vide.

> [!NOTE]
> Les utilisateurs peuvent également [créer leurs propres tableaux de bord personnels](howto-personalize-dashboard.md) à utiliser au lieu du tableau de bord de l’application par défaut.

## <a name="add-tiles"></a>Ajouter des vignettes

La capture d’écran suivante montre le tableau de bord dans une application créée à partir du modèle **Exemple Contoso**. Pour personnaliser le tableau de bord par défaut pour votre application, sélectionnez **Modifier** en haut à droite de la page.

![Tableau de bord pour les applications basées sur le modèle « Exemple Contoso »](media/howto-configure-homepage/image1a.png)

La sélection de **Modifier** ouvre le panneau de bibliothèque de tableaux de bord. La bibliothèque contient les vignettes et les primitives de tableau de bord que vous pouvez utiliser pour personnaliser le tableau de bord.

![Bibliothèque de tableaux de bord](media/howto-configure-homepage/image2a.png)

Par exemple, vous pouvez ajouter une vignette **Paramètres et propriétés de l’appareil** pour montrer une sélection des valeurs actuelles de paramètres et de propriétés d’un appareil. Pour cela, sélectionnez d’abord un **modèle d’appareil**, puis sélectionnez une **instance d’appareil**. Après cela, indiquez un titre pour la vignette, puis sélectionnez un **paramètre** ou une **propriété** à afficher. La capture d’écran suivante montre les paramètres et propriétés sélectionnées à ajouter à la vignette. Sélectionnez **Terminé** pour enregistrer la modification apportée au tableau de bord.

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](media/howto-configure-homepage/image3a.png)

Maintenant, lorsqu’un opérateur affiche le tableau de bord de l’application par défaut, il voit la nouvelle vignette avec le paramètre **Set Temperature** (Définir la température) pour l’appareil :

![Onglet « Tableau de bord » où figurent les paramètres et les propriétés de la vignette](media/howto-configure-homepage/image4a.png)

Vous pouvez explorer d’autres types de vignettes dans la bibliothèque afin de découvrir comment personnaliser davantage le tableau de bord de l’application par défaut.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment configurer votre tableau de bord de l’application par défaut d’Azure IoT Central, vous pouvez :

> [!div class="nextstepaction"]
> [Découvrir comment préparer et charger des images](howto-prepare-images.md)
