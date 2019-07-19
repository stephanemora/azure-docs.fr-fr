---
title: Créer des images de machine virtuelle Azure partagées pour Windows à l’aide du portail | Microsoft Docs
description: Découvrez comment utiliser le Portail Azure pour créer et partager des images de machine virtuelle.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 93734045cd06f279f37d7725aa573a59c4ec0be9
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465713"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Créer une galerie d’images partagées à l’aide du Portail Azure

Une [galerie d’images partagées](shared-image-galleries.md) simplifie considérablement le partage d’images personnalisées dans votre organisation. Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des tâches de déploiement comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. 

La galerie d’images partagées vous permet de partager vos images de machine virtuelle personnalisées avec d’autres personnes de votre organisation, dans ou entre les régions, à l’intérieur d’un locataire AAD. Choisissez les images à partager, les régions dans lesquelles vous souhaitez les rendre disponibles et les personnes avec lesquelles vous voulez les partager. Vous pouvez créer plusieurs galeries afin de regrouper logiquement les images partagées. 

La galerie est une ressource de niveau supérieur qui fournit le contrôle d’accès en fonction du rôle (RBAC) complet. Les versions des images peuvent être gérées, et vous pouvez choisir de répliquer chaque version d’image vers un autre ensemble de régions Azure. La galerie fonctionne uniquement avec les images managées.

La fonctionnalité Galerie d’images partagées présente plusieurs types de ressources. Dans cet article, nous allons utiliser ou générer ce qui suit :

| Ressource | Description|
|----------|------------|
| **Image managée** | Il s’agit d’une image de base pouvant être utilisée seule ou pour créer une **version de l’image**  dans une galerie d’images. Les images managées sont créées à partir de machines virtuelles généralisées. Une image managée est un type spécial de disque dur virtuel qui peut être utilisé pour définir plusieurs machines virtuelles et qui peut maintenant être utilisé pour créer des versions d’image partagée. |
| **Galerie d’images** | Tout comme la Place de marché Azure, une **galerie d’images** est un dépôt permettant de gérer et partager des images, mais vous contrôlez les utilisateurs qui y ont accès. |
| **Définition d'image** | Les images sont définies dans une galerie et incluent des informations sur l’image et sur les exigences relatives à son utilisation en interne. Ces informations indiquent, par exemple, si l’image est Windows ou Linux, et comprennent les notes de publication et les exigences de mémoire maximale et minimale. Il s’agit d’une définition de type d’image. |
| **Version de l’image** | Une **version d’image** est ce qui vous permet de créer une machine virtuelle quand vous utilisez une galerie. Vous pouvez avoir plusieurs versions d’une image en fonction des besoins de votre environnement. Tout comme une image managée, quand vous utilisez une **version d’image** pour créer une machine virtuelle, la version d’image permet de créer des disques pour la machine virtuelle. Les versions d’image peuvent être utilisées plusieurs fois. |


## <a name="before-you-begin"></a>Avant de commencer

Pour suivre l’exemple de cet article, vous devez avoir l’image managée. Vous pouvez suivre le [Tutoriel : créer une image personnalisée d’une machine virtuelle Azure avec Azure PowerShell](tutorial-custom-images.md) si nécessaire. Si l’image managée contient un disque de données, la taille du disque de données ne peut pas être supérieure à 1 To.

Au cours de cet article, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>Créer des machines virtuelles à partir d’une image

Une fois que la version de l’image est complète, vous pouvez créer une ou plusieurs nouvelles machines virtuelles. 

> [!IMPORTANT]
> Vous ne pouvez pas utiliser le portail pour déployer une machine virtuelle à partir d’une image dans un autre locataire Azure. Pour créer une machine virtuelle à partir d’une image partagée entre des locataires, vous devez utiliser l’interface [Azure CLI](../linux/shared-images.md#create-a-vm) ou [PowerShell](shared-images.md#create-vms-from-an-image).

Cet exemple crée une machine virtuelle nommée *myVMfromImage*, dans le groupe *myResourceGroup* dans le centre de données *USA Est*.

1. Sur la page de votre version d’image, sélectionnez **Créer une machine virtuelle** dans le menu en haut de la page.
1. Pour **Groupe de ressources**, sélectionnez **Créer** et entrez *myResourceGroup* comme nom.
1. Dans **Nom de la machine virtuelle**, tapez *myVM*.
1. Pour **Région**, sélectionnez *USA Est*.
1. Pour **Options de disponibilité**, conservez la valeur par défaut *Aucune redondance d’infrastructure nécessaire*.
1. La valeur sous **Image** doit être automatiquement renseignée si vous avez démarré à partir de la page de la version d’image.
1. Pour **Taille**, choisissez une taille de machine virtuelle dans la liste des tailles disponibles, puis cliquez sur « Sélectionner ».
1. Sous **Compte d’administrateur**, indiquez un nom d’utilisateur (par exemple, *azureuser*) et un mot de passe. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Si vous souhaitez autoriser l’accès à distance à la machine virtuelle, sous **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, puis sélectionnez **RDP (3389)** dans la liste déroulante. Si vous ne souhaitez pas autoriser l’accès à distance à la machine virtuelle, conservez la sélection **Aucun** pour **Ports d’entrée publics**.
1. Lorsque vous avez terminé, sélectionnez le bouton **Vérifier + créer** en bas de la page.
1. Une fois la machine virtuelle validée, sélectionnez **Créer** en bas de la page pour démarrer le déploiement.



## <a name="clean-up-resources"></a>Supprimer des ressources

Dès que vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées. Pour cela, sélectionnez le groupe de ressources de la machine virtuelle, sélectionnez **Supprimer**, puis confirmez le nom du groupe de ressources à supprimer.

Si vous souhaitez supprimer des ressources individuelles, vous devez les supprimer dans l’ordre inverse. Par exemple, pour supprimer une définition d’image, vous devez supprimer toutes les versions de l’image créées à partir de cette image.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Créer une machine virtuelle à partir d’une version d’image](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Pour plus d’informations sur les galeries d’images partagées, consultez la [vue d’ensemble](shared-image-galleries.md). Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](troubleshooting-shared-images.md).

