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
ms.openlocfilehash: c8444fd57fe939dfb212d88c22a071c13dac691e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767326"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>Préparer et charger des images dans votre application Azure IoT Central

Cet article décrit comment, en tant que concepteur, vous pouvez personnaliser votre application Azure IoT Central en téléchargeant des images personnalisées. Par exemple, vous pouvez personnaliser le tableau de bord d’un appareil avec une image de l’appareil.

## <a name="before-you-begin"></a>Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

1. Une application Azure IoT Central. Pour plus d’informations, consultez [Créer une application](quick-deploy-iot-central.md).
1. Un outil pour la mise à l’échelle et le redimensionnement des fichiers d’images.

## <a name="choose-where-to-use-custom-images"></a>Choisir où utiliser des images personnalisées

Vous pouvez ajouter des images personnalisées aux emplacements suivants dans une application Azure IoT Central :

* Le **mes applications** page

    ![Image sur la page Gestionnaire d’application](media/howto-prepare-images/applicationmanager.png)

* Le tableau de bord d’application

    ![Image sur le tableau de bord applications](media/howto-prepare-images/homepage.png)

* Un modèle d’appareil

    ![Image sur un modèle d’appareil](media/howto-prepare-images/devicetemplate.png)

* Une vignette sur un tableau de bord d’appareil

    ![Image sur une vignette d’appareil](media/howto-prepare-images/devicetile.png)

* Une vignette sur le tableau de bord d’un ensemble d’appareils

    ![Image sur une vignette d’ensemble d’appareils](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>Préparer les images

Dans les quatre emplacements, vous pouvez utiliser des images PNG, GIF ou JPEG.

Le tableau suivant récapitule les tailles d’image que vous pouvez utiliser :

| Lieu | Tailles |
| -------- | ------ |
| Gestionnaire d’application | 268 x 160 px |
| Modèle d’appareil | 64 x 64 px |
| Vignettes de tableau de bord | La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px. |

Pour obtenir le meilleur affichage dans l’application, vous devez créer des images qui correspondent aux dimensions indiquées dans le tableau précédent.

## <a name="upload-the-images"></a>Charger les images

Les sections suivantes décrivent comment charger les images dans les différents emplacements :

### <a name="application-manager"></a>Gestionnaire d’application

Pour charger une image à utiliser sur le **mes applications** page, accédez à la **paramètres d’Application** page dans le **Administration** section. Vous devez être administrateur pour effectuer cette tâche :

![Charger une image d’application](media/howto-prepare-images/uploadapplicationmanager.png)

Sélectionnez la vignette de l’Image d’Application pour charger votre image préparée (268 x 160 px) à partir de votre ordinateur local.

### <a name="application-dashboard"></a>Tableau de bord de l’application

Pour télécharger une image du tableau de bord d’application, accédez à la **tableau de bord** page de votre application, puis sélectionnez **modifier**. Vous devez être concepteur pour effectuer cette tâche :

![Télécharger l’image du tableau de bord](media/howto-prepare-images/uploadhomepage.png)

Sous configurer une Image, sélectionnez la vignette de l’Image à charger votre image préparée à partir de votre ordinateur local. La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px.

**Enregistrez** l’image chargée. Vous pouvez la redimensionner en mode édition. Sélectionnez **fait** issue.

### <a name="device-template"></a>Modèle d’appareil

Pour charger une image sur un modèle d’appareil, accédez à **Device Templates** et choisissez le modèle d’appareil. Vous devez être concepteur pour effectuer cette tâche :

![Charger une image de modèle d’appareil](media/howto-prepare-images/uploaddevicetemplate.png)

Sélectionnez la vignette de l’image à charger votre image préparée (64 x 64 px) à partir de votre ordinateur local.

### <a name="device-dashboard"></a>Page du tableau de bord d’un appareil

Pour charger une image sur un tableau de bord d’appareil, accédez à **Device Explorer** et choisissez le modèle d’appareil, puis un appareil. Choisissez alors l’onglet **Tableau de bord**. Vous devez être concepteur pour effectuer cette tâche :

![Charger une image de tableau de bord d’appareil](media/howto-prepare-images/uploaddevicedashboard.png)

Sous configurer une Image, sélectionnez la vignette de l’Image, puis choisissez le fichier à télécharger à partir de votre ordinateur local. La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px.

**Enregistrez** l’image chargée. Vous pouvez la redimensionner et la repositionner en mode édition. Sélectionnez **fait** issue.

### <a name="device-set-dashboard"></a>Tableau de bord d’un ensemble d’appareils

Pour charger une image sur un tableau de bord d’ensemble d’appareils, accédez à **Explorateur d’appareils** et choisissez l’ensemble d’appareils, puis un appareil. Puis choisissez le **tableau de bord** page et sélectionnez **modifier**:

![Charger une image de tableau de bord d’ensemble d’appareils](media/howto-prepare-images/uploaddevicesetdashboard.png)

Sous configurer une Image, sélectionnez la vignette de l’Image à charger votre image préparée à partir de votre ordinateur local. La vignette de plus petite taille est de 200 x 200 px ; les vignettes plus grandes peuvent être des multiples carrés ou rectangulaires de petites vignettes. Par exemple, 200 x 400 px, 400 x 200 px ou 400 x 400 px.

**Enregistrez** l’image chargée. Vous pouvez la redimensionner et la repositionner en mode édition. Sélectionnez **fait** issue.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à préparer et à charger des images dans votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Gérer les appareils dans votre application Azure IoT Central](howto-manage-devices.md)