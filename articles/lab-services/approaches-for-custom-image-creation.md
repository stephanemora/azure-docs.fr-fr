---
title: Azure Lab Services - Approches recommandées pour la création d’images personnalisées pour les labos
description: Décrit les approches de création d’images personnalisées pour les labos.
ms.date: 07/27/2021
ms.topic: article
ms.openlocfilehash: 621456c910b5137ed14a77d8d2cfeb9664910fa9
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450637"
---
# <a name="recommended-approaches-for-creating-custom-images"></a>Approches recommandées pour la création d’images personnalisées
Cet article décrit les approches recommandées suivantes pour créer une image personnalisée :

-   Créer et enregistrer une image personnalisée à partir du [modèle de machine virtuelle d’un labo](how-to-create-manage-template.md).
-   Apporter une image personnalisée depuis l’extérieur du contexte d’un labo en utilisant :
    - Une [machine virtuelle Azure](https://azure.microsoft.com/services/virtual-machines/).
    - Un disque dur virtuel dans votre environnement de labo physique.

## <a name="save-a-custom-image-from-a-labs-template-vm"></a>Enregistrer une image personnalisée à partir du modèle de machine virtuelle d’un labo

L’utilisation du modèle de machine virtuelle d’un labo pour créer et enregistrer une image personnalisée est la manière la plus simple de créer une image, car elle est prise en charge avec le portail Azure Lab Services. Ainsi, les services informatiques et les enseignants peuvent créer des images personnalisées en utilisant le modèle de machine virtuelle d’un labo.

Par exemple, vous pouvez commencer avec l’une des images de la Place de marché Azure, puis installer les applications logicielles et les outils qui sont nécessaires pour un cours. Une fois que vous avez terminé de configurer l’image, vous pouvez l’enregistrer dans la [galerie d’images partagées connectée](how-to-attach-detach-shared-image-gallery.md) afin que vous et d’autres enseignants puissiez utiliser l’image pour créer des labos.

Il existe quelques points clés à connaître avec cette approche :

- Lab Services enregistre automatiquement une image *spécialisée* quand vous l’exportez à partir du modèle de machine virtuelle. Dans la plupart des cas, les images spécialisées sont particulièrement adaptées à la création de labos, car l’image conserve les informations propres à l’ordinateur et les profils utilisateur. L’utilisation d’une image spécialisée aide à s’assurer que le logiciel installé s’exécute de la même façon quand l’image est utilisée pour créer des labos. Si vous avez besoin de créer une image *généralisée*, vous devez utiliser l’une des autres approches recommandées dans cet article pour créer une image personnalisée.

    Vous pouvez créer des labos basés sur des images généralisées et spécialisées dans Azure Lab Services. Pour plus d’informations sur les différences, consultez [Images généralisées et spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

- Pour des scénarios plus avancés avec la configuration de votre image, il peut s’avérer utile de créer une image en dehors des labos en utilisant une machine virtuelle Azure ou un disque dur virtuel à partir de votre environnement lab physique. Pour plus d’informations, lisez les sections suivantes.

### <a name="use-a-labs-template-vm-to-save-a-custom-image"></a>Utiliser le modèle de machine virtuelle d’un labo pour enregistrer une image personnalisée 

Vous pouvez utiliser le modèle de machine virtuelle d’un labo pour créer des images personnalisées Windows ou Linux. Pour plus d’informations, consultez [Enregistrer une image dans la galerie d’images partagées](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery).

## <a name="bring-a-custom-image-from-an-azure-vm"></a>Apporter une image personnalisée depuis une machine virtuelle Azure

Une autre approche consiste à utiliser une machine virtuelle Azure pour configurer une image personnalisée. Une fois que vous avez terminé de configurer l’image, vous pouvez l’enregistrer dans une galerie d’images partagées afin que vous et vos collègues puissiez l’utiliser pour créer des labos.

L’utilisation d’une machine virtuelle Azure offre plus de flexibilité :
- Vous pouvez créer des images [généralisées ou spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images). En revanche, si vous utilisez le [modèle de machine virtuelle d’un labo](how-to-use-shared-image-gallery.md) pour exporter une image, l’image est toujours spécialisée.
- Vous avez accès à des fonctionnalités plus avancées d’une machine virtuelle Azure qui peuvent être utiles pour configurer une image. Par exemple, vous pouvez utiliser des [extensions](../virtual-machines/extensions/overview.md) pour effectuer une configuration postdéploiement et une automatisation. Vous pouvez aussi accéder aux [diagnostics de démarrage](../virtual-machines/boot-diagnostics.md) et à la [console série](/troubleshoot/azure/virtual-machines/serial-console-overview) de la machine virtuelle.

La configuration d’une image avec une machine virtuelle Azure est plus complexe. Ainsi, les services informatiques sont généralement responsables de la création d’images personnalisées sur des machines virtuelles Azure.

### <a name="use-an-azure-vm-to-set-up-a-custom-image"></a>Utiliser une machine virtuelle Azure pour configurer une image personnalisée

Voici les principales étapes à suivre pour apporter une image personnalisée depuis une machine virtuelle Azure :

1. Créez une [machine virtuelle Azure](https://azure.microsoft.com/services/virtual-machines/) en utilisant une image de la Place de marché Windows ou Linux.
1. Connectez-vous à la machine virtuelle Azure et installez d’autres logiciels. Vous pouvez également effectuer d’autres personnalisations nécessaires à votre labo.
1. Une fois que vous avez fini de configurer l’image, [enregistrez l’image de la machine virtuelle dans une galerie d’images partagées](../virtual-machines/image-version.md). Dans le cadre de cette étape, vous devez également créer la définition et la version de l’image.
1. Une fois que l’image personnalisée est enregistrée dans la galerie, vous pouvez l’utiliser pour créer des labos. 


Les étapes varient selon que vous créez une image personnalisée Windows ou Linux. Lisez les articles suivants pour obtenir des instructions détaillées :

-   [Apporter une image personnalisée Windows à partir d’une machine virtuelle Azure](how-to-bring-custom-windows-image-azure-vm.md)
-   [Apporter une image personnalisée Linux à partir d’une machine virtuelle Azure](how-to-bring-custom-linux-image-azure-vm.md)

## <a name="bring-a-custom-image-from-a-vhd-in-your-physical-lab-environment"></a>Apporter une image personnalisée depuis un disque dur virtuel dans votre environnement de labo physique

La troisième approche à prendre en compte consiste à apporter une image personnalisée depuis un disque dur virtuel dans votre environnement de labo physique dans une galerie d’images partagées. Une fois que l’image se trouve dans une galerie d’images partagées, vous et d’autres enseignants pouvez utiliser l’image pour créer des labos.

Voici quelques raisons pour lesquelles vous voudrez peut-être utiliser cette approche :

- Vous pouvez créer des images [généralisées ou spécialisées](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) à utiliser dans vos labos. En revanche, si vous utilisez le [modèle de machine virtuelle d’un labo](how-to-use-shared-image-gallery.md) pour exporter une image, l’image est toujours spécialisée.
- Vous pouvez accéder aux ressources qui existent dans votre environnement local. Par exemple, il est possible que votre environnement local comporte de grands fichiers d’installation dont la copie sur le modèle de machine virtuelle d’un labo prend trop de temps.
- Vous pouvez charger des images créées avec d’autres outils, tels que [Microsoft Endpoint Configuration Manager](/mem/configmgr/core/understand/introduction), pour ne pas avoir à configurer manuellement une image à l’aide d’un modèle de machine virtuelle de labo.

L’approche consistant à apporter une image personnalisée depuis un disque dur virtuel est l’approche la plus avancée, car vous devez vous assurer que l’image est correctement configurée pour fonctionner dans Azure. Ainsi, les services informatiques sont généralement responsables de la création d’images personnalisées à partir de disques durs virtuels.

### <a name="bring-a-custom-image-from-a-vhd"></a>Apporter une image personnalisée à partir d’un disque dur virtuel

Voici les principales étapes à suivre pour apporter une image personnalisée depuis un disque dur virtuel :

1. Utilisez [Windows Hyper-V](/virtualization/hyper-v-on-windows/about/) sur votre ordinateur local pour créer un disque dur virtuel Windows ou Linux.
1. Connectez-vous à la machine virtuelle Hyper-V et installez d’autres logiciels. Vous pouvez également effectuer d’autres personnalisations nécessaires à votre labo.
1. Une fois que vous avez fini de configurer l’image, chargez le disque dur virtuel pour créer un [disque managé](../virtual-machines/managed-disks-overview.md) dans Azure.
1. À partir du disque managé, créez la version et la [définition de l’image](../virtual-machines/shared-image-galleries.md#image-definitions) dans une galerie d’images partagées.
1. Une fois que l’image personnalisée est enregistrée dans la galerie, vous pouvez l’utiliser pour créer des labos. 

Les étapes varient selon que vous créez une image personnalisée Windows ou Linux. Lisez les articles suivants pour obtenir des instructions détaillées :

-   [Apporter une image personnalisée Windows à partir d’un disque dur virtuel](upload-custom-image-shared-image-gallery.md)
-   [Apporter une image personnalisée Linux à partir d’un disque dur virtuel](how-to-bring-custom-linux-image-vhd.md)

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Shared Image Gallery](../virtual-machines/shared-image-galleries.md)
* [Attacher ou détacher une galerie d’images partagées](how-to-attach-detach-shared-image-gallery.md)
* [Utiliser une galerie d’images partagées](how-to-use-shared-image-gallery.md)
