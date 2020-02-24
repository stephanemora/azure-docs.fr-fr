---
title: Réinitialiser Azure Kinect DK
description: Explique comment réinitialiser un appareil Azure Kinect DK sur son image d’usine
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, réinitialiser
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201896"
---
# <a name="reset-azure-kinect-dk"></a>Réinitialiser Azure Kinect DK

Vous pouvez être amené à réinitialiser Azure Kinect DK sur son image d’usine (par exemple, si une mise à jour du microprogramme n’a pas été installée correctement).

1. Mettez l’appareil Azure Kinect DK hors tension. Pour ce faire, retirez le câble USB et le câble d’alimentation.
  ![Diagramme montrant l’emplacement de la vis qui recouvre le bouton de réinitialisation](media/reset-azure-kinect-dk-diagram.png)
1. Pour accéder au bouton de réinitialisation, retirez la vis située dans le verrou du trépied.
1. Rebranchez le câble d’alimentation.
1. Insérez le bout d’un trombone déplié dans le trou de la vis située dans le verrou du trépied.
1. Utilisez le trombone pour appuyer doucement sur le bouton de réinitialisation et le maintenir enfoncé.
1. Pendant que vous appuyez sur le bouton de réinitialisation, rebranchez le câble USB.
1. Au bout d’environ 3 secondes, le voyant d’alimentation devient orange. Une fois que le voyant a changé de couleur, relâchez le bouton de réinitialisation.  
   
   Une fois que vous avez relâché le bouton de réinitialisation, le voyant d’alimentation clignote en blanc et en orange pendant la réinitialisation de l’appareil. 
1. Attendez que le voyant d’alimentation soit blanc et fixe.
1. Replacez la vis dans le verrou du trépied, par-dessus le bouton de réinitialisation.
1. Utilisez la visionneuse Azure Kinect pour vérifier que le microprogramme a été réinitialisé. Pour ce faire, lancez la [visionneuse Azure Kinect](azure-kinect-viewer.md), puis sélectionnez **Device firmware version info** (Informations sur la version du microprogramme de l’appareil) pour voir la version du microprogramme qui est installée sur votre ordinateur Azure Kinect DK.

Vérifiez toujours que le microprogramme le plus récent est installé sur l’appareil. Pour connaître la dernière version du microprogramme, utilisez l’outil du microprogramme Azure Kinect. Pour savoir comment vérifier l’état du microprogramme, consultez [Vérifier la version du microprogramme de l’appareil](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Rubriques connexes

- [À propos d’Azure Kinect DK](about-azure-kinect-dk.md)
- [Configurer Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Spécifications matérielles Azure Kinect DK : environnement d’exploitation](hardware-specification.md#operating-environment)
- [Outil du microprogramme Azure Kinect](azure-kinect-firmware-tool.md)
- [Visionneuse Azure Kinect](azure-kinect-viewer.md)
- [Synchronisation de plusieurs appareils Azure Kinect DK](multi-camera-sync.md)
