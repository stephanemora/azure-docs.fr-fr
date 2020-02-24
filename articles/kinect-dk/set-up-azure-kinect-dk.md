---
title: Démarrage rapide - Configurer Azure Kinect DK
description: Ce guide de démarrage rapide fournit des instructions sur la configuration du matériel Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, kit de développement, azure dk, configurer, matériel, rapide, usb, alimentation, visionneuse, capteur, streaming, configuration, sdk, microprogramme
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211276"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Démarrage rapide : Configurer Azure Kinect DK

Ce guide de démarrage rapide fournit des instructions sur la configuration d’Azure Kinect DK. Nous allons vous montrer comment tester la visualisation du flux de capteur et utiliser la [visionneuse Kinect Azure](azure-kinect-viewer.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="system-requirements"></a>Configuration système requise

Consultez la [configuration requise](system-requirements.md) pour voir si la configuration de votre ordinateur hôte répond aux exigences minimales d’Azure Kinect DK.

## <a name="set-up-hardware"></a>Configurer le matériel

> [!NOTE]
> Veillez à supprimer le film de protection de la caméra avant d’utiliser l’appareil.

1. Branchez le câble d’alimentation sur la prise jack située à l’arrière de l’appareil. Connectez l’adaptateur secteur USB à l’autre extrémité du câble, puis branchez l’adaptateur à une prise d’alimentation.
2. Connectez le câble de données USB à votre appareil, puis à un port USB 3.0 de votre PC.
   >[!NOTE]
   >Pour de meilleurs résultats, connectez le câble directement à l’appareil et au PC. Évitez d’utiliser des extensions ou des adaptateurs supplémentaires dans la connexion.

3. Vérifiez que le voyant d’alimentation (situé à côté du câble USB) est blanc et fixe.
  
   La mise sous tension de l’appareil prend quelques secondes. L’appareil est prêt à être utilisé lorsque le voyant de diffusion des données situé à l’avant s’éteint.  

   Pour plus d’informations sur le voyant d’alimentation, consultez [À quoi correspond ce voyant ?](hardware-specification.md#what-does-the-light-mean)

    ![Fonctionnalités complètes de l’appareil](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Télécharger le Kit de développement logiciel (SDK)

1. Sélectionnez le lien [Télécharger le SDK](sensor-sdk-download.md).
2. Installez le SDK sur votre PC.

## <a name="update-firmware"></a>Mettre à jour le microprogramme

Pour fonctionner correctement, le SDK a besoin de la dernière version du microprogramme de l’appareil. Pour vérifier et mettre à jour la version de votre microprogramme, suivez les étapes décrites dans [Mettre à jour le microprogramme Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Vérifier que l’appareil diffuse des données

1. Lancez la [visionneuse Azure Kinect](azure-kinect-viewer.md). Vous pouvez démarrer cet outil à l’aide de l’une des méthodes suivantes :
   - Vous pouvez lancer la visionneuse à partir de la ligne de commande ou en double-cliquant sur le fichier exécutable. Le fichier `k4aviewer.exe` se trouve dans le répertoire des outils du SDK (par exemple, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, où `X.Y.Z` correspond à la version installée du SDK).
   - Vous pouvez lancer la visionneuse Azure Kinect à partir du menu **Démarrer** de l’appareil.
2. Dans la visionneuse Azure Kinect, sélectionnez **Open Device (Ouvrir l’appareil)**  > **Start (Démarrer)** .

    ![Visionneuse Azure Kinect](./media/quickstarts/viewer.png)

3. Vérifiez que l’outil visualise chaque flux de capteur :
   - Caméra à profondeur de champ
   - Caméra couleur
   - Caméra infrarouge
   - Unités de mesure inertielle
   - Microphones

    ![Outil de visualisation](./media/quickstarts/visualization-tool.png)

Vous avez terminé la configuration d’Azure Kinect DK. Vous pouvez maintenant commencer à développer votre application ou à intégrer des services.

Si vous rencontrez des problèmes, consultez [Résolution des problèmes](troubleshooting.md).

## <a name="see-also"></a>Voir aussi

- [Informations sur le matériel Azure Kinect DK](hardware-specification.md)
- [Mettre à jour le microprogramme de l’appareil](update-device-firmware.md)
- En savoir plus sur la [visionneuse Azure Kinect](azure-kinect-viewer.md)

## <a name="next-steps"></a>Étapes suivantes

Une fois que le service Azure Kinect DK est prêt et opérationnel, vous pouvez également apprendre à :
> [!div class="nextstepaction"]
> [Enregistrer des flux de capteur dans un fichier](record-sensor-streams-file.md)
