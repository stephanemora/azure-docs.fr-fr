---
title: Charger des images dans votre application Azure IoT Central | Microsoft Docs
description: En tant que concepteur, découvrez comment préparer et charger des images dans votre application Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b975d7dccc85973a42408d87e3c03a91aaf1c450
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812750"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Préparer et charger des images dans votre application Azure IoT Central

Cet article décrit comment, en tant que concepteur, vous pouvez personnaliser votre application Azure IoT Central en téléchargeant des images personnalisées. Par exemple, vous pouvez personnaliser le tableau de bord d’un appareil avec une image de l’appareil.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
1. Un outil pour la mise à l’échelle et le redimensionnement des fichiers d’images.

## <a name="choose-where-to-use-custom-images"></a>Choisir où utiliser des images personnalisées

Vous pouvez ajouter des images personnalisées aux emplacements suivants dans une application Azure IoT Central :

* La page **Gestionnaire d’application**

    ![Image sur la page Gestionnaire d’application](media/howto-prepare-images-experimental/applicationmanager.png)

* La page d’accueil

    ![Image sur la page d’accueil](media/howto-prepare-images-experimental/homepage.png)

* Un modèle d’appareil

    ![Image sur un modèle d’appareil](media/howto-prepare-images-experimental/devicetemplate.png)

* Une vignette sur un tableau de bord d’appareil

    ![Image sur une vignette d’appareil](media/howto-prepare-images-experimental/devicetile.png)

* Une vignette sur le tableau de bord d’un ensemble d’appareils

    ![Image sur une vignette d’ensemble d’appareils](media/howto-prepare-images-experimental/devicesettile.png)

## <a name="prepare-the-images"></a>Préparer les images

Dans les quatre emplacements, vous pouvez utiliser des images PNG, GIF ou JPEG.

Le tableau suivant récapitule les tailles d’image que vous pouvez utiliser :

| Lieu | Tailles |
| -------- | ------ |
| Gestionnaire d’application | 268 x 160 px |
| Modèle d’appareil | 64 x 64 px |
| Page d’accueil et vignettes de tableau de bord | La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px. |

Pour obtenir le meilleur affichage dans l’application, vous devez créer des images qui correspondent aux dimensions indiquées dans le tableau précédent.

## <a name="upload-the-images"></a>Charger les images

Les sections suivantes décrivent comment charger les images dans les différents emplacements :

### <a name="application-manager"></a>Gestionnaire d’application

Pour charger une image sur le **Gestionnaire d’application**, accédez à la page **Paramètres de l’application** dans la section **Administration**. Vous devez être administrateur pour effectuer cette tâche :

![Charger une image d’application](media/howto-prepare-images-experimental/uploadapplicationmanager.png)

Cliquez sur la vignette de l’image d’application pour charger votre image préparée (268 x 160 px) à partir de votre ordinateur local.

### <a name="home-page"></a>page d'accueil

Pour charger une image dans la page d’accueil, accédez à la **page d’accueil** de votre application, puis cliquez sur **Modifier**. Vous devez être concepteur pour effectuer cette tâche :

![Charger une image de page d’accueil](media/howto-prepare-images-experimental/uploadhomepage.png)

Sous Configurer une Image, cliquez sur la vignette de l’image pour charger votre image préparée à partir de votre ordinateur local. La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px.

**Enregistrez** l’image chargée. Vous pouvez la redimensionner en mode édition. Lorsque vous avez terminé, cliquez sur **Terminé**. 

### <a name="device-template"></a>Modèle d’appareil

Pour charger une image sur un modèle d’appareil, accédez à **Device Templates** et choisissez le modèle d’appareil. Vous devez être concepteur pour effectuer cette tâche :

![Charger une image de modèle d’appareil](media/howto-prepare-images-experimental/uploaddevicetemplate.png)

Cliquez sur la vignette de l’image pour charger votre image préparée (64 x 64 px) à partir de votre ordinateur local. 

### <a name="device-dashboard"></a>Page du tableau de bord d’un appareil

Pour charger une image sur un tableau de bord d’appareil, accédez à **Device Explorer** et choisissez le modèle d’appareil, puis un appareil. Choisissez alors l’onglet **Tableau de bord**. Vous devez être concepteur pour effectuer cette tâche :

![Charger une image de tableau de bord d’appareil](media/howto-prepare-images-experimental/uploaddevicedashboard.png)

Sous Configurer une Image, cliquez sur la vignette de l’image, puis choisissez le fichier à charger à partir de votre ordinateur local. La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px.

**Enregistrez** l’image chargée. Vous pouvez la redimensionner et la repositionner en mode édition. Lorsque vous avez terminé, cliquez sur **Terminé**.

### <a name="device-set-dashboard"></a>Tableau de bord d’un ensemble d’appareils

Pour charger une image sur un tableau de bord d’ensemble d’appareils, accédez à **Explorateur d’appareils** et choisissez l’ensemble d’appareils, puis un appareil. Choisissez ensuite la page **Tableau de bord** et cliquez sur **Modifier** :

![Charger une image de tableau de bord d’ensemble d’appareils](media/howto-prepare-images-experimental/uploaddevicesetdashboard.png)

Sous Configurer une Image, cliquez sur la vignette de l’image pour charger votre image préparée à partir de votre ordinateur local. La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px.

**Enregistrez** l’image chargée. Vous pouvez la redimensionner et la repositionner en mode édition. Lorsque vous avez terminé, cliquez sur **Terminé**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à préparer et à charger des images dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Gérer les appareils dans votre application Azure IoT Central](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)