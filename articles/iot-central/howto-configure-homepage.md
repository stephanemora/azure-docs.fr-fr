---
title: Configurer la page d’accueil de votre application Azure IoT Central | Microsoft Docs
description: En tant que concepteur, découvrez comment configurer la page d’accueil de votre application Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/10/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 93f406a1d5e4a8c2ce5ad1db0c3936dd3ad2bfb9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992185"
---
## <a name="configuring-homepage"></a>Configuration de la page d’accueil

La page d’accueil est la page qui se charge quand les utilisateurs autorisés accèdent à l’URL de l’application. Si vous avez sélectionné les modèles d’application « Exemple Contoso » ou « Exemple Devkits » lors de la création de votre application, votre application a des pages d’accueil prédéfinies. Par contre, si vous avez sélectionné le modèle d’application « Application personnalisée », votre page d’accueil est vide.

Par exemple, voici la page d’accueil pour les applications basées sur le modèle « Exemple Contoso ». Pour personnaliser la page d’accueil de votre application, **activez** d’abord le **Mode Création** en haut à droite. 

![Page d’accueil pour les applications basées sur le modèle « Exemple Contoso »](media\howto-configure-homepage\image1.png)

Une fois le **Mode Création** **activé**, la bibliothèque de tableaux de bord s’ouvre dans un panneau à gauche. Il existe de nombreux types de vignettes et de primitives de tableau de bord que vous pouvez ajouter pour personnaliser votre page d’accueil.

![Bibliothèque de tableaux de bord](media\howto-configure-homepage\image2.png)

Par exemple, vous pouvez ajouter une vignette **Paramètres et propriétés** pour montrer une sélection des valeurs actuelles de paramètres et de propriétés. Pour cela, sélectionnez d’abord un **modèle d’appareil**, puis sélectionnez une **instance d’appareil**. Après cela, indiquez un titre pour la vignette, puis sélectionnez un **paramètre** ou une **propriété** à afficher. Dans ce cas, nous avons sélectionné **Fan Speed** (Vitesse du ventilateur). Cliquer sur **Enregistrer** fait que cette vignette apparaît sur la page d’accueil.

![Formulaire « Configurer les détails de l’appareil » contenant les détails des paramètres et des propriétés](media\howto-configure-homepage\image3.png)

Désormais, quand un opérateur visualise la page d’accueil, il peut voir cette vignette qui affiche les propriétés ou les paramètres de l’appareil :

![Onglet « Tableau de bord » où figurent les paramètres et les propriétés de la vignette](media\howto-configure-homepage\image4.png)

Familiarisez-vous avec les différents autres types de vignettes de la bibliothèque pour découvrir comment vous pouvez personnaliser encore plus la page d’accueil de votre application.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez découvert comment configurer votre page d’accueil Azure IoT Central, vous pouvez :

> [!div class="nextstepaction"]
> [Découvrir comment préparer et charger des images](howto-prepare-images.md)
