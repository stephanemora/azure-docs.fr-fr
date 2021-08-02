---
title: Résolution des problèmes liés aux chargements d’images de machines virtuelles dans Azure Stack Edge Pro GPU | Microsoft Docs
description: Explique comment résoudre les problèmes qui se produisent lors du chargement, du téléchargement ou de la suppression d’images de machines virtuelles dans Azure Stack Edge Pro GPU.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: alkohli
ms.openlocfilehash: 3ec5cfb952e666832fe887f65672e228004423bc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482198"
---
# <a name="troubleshoot-virtual-machine-image-uploads-in-azure-stack-edge-pro-gpu"></a>Résolution des problèmes liés aux chargements d’images de machines virtuelles dans Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment résoudre les problèmes qui se produisent lors du téléchargement et de la gestion d’images de machines virtuelles sur un appareil Azure Stack Edge Pro GPU.


## <a name="unable-to-add-vm-image-to-blob-container"></a>Impossible d’ajouter une image de machine virtuelle au conteneur d’objets blob

**Description de l’erreur :** sur le portail Azure, quand vous tentez de charger une image de machine virtuelle dans un conteneur d’objets blob, le bouton **Ajouter** n’est pas disponible. Il n’est pas possible de charger l’image. En effet, le bouton **Ajouter** n’est pas disponible si vous ne disposez pas des autorisations nécessaires du rôle Collaborateur sur le groupe de ressources ou l’abonnement de l’appareil.

**Solution suggérée :** vérifiez que vous disposez des autorisations de collaborateur nécessaires pour ajouter des fichiers au groupe de ressources ou au compte de stockage. Pour plus d’informations, consultez [Prérequis de la ressource Azure Stack Edge](azure-stack-edge-deploy-prep.md#prerequisites).


## <a name="invalid-blob-type-for-the-source-blob-uri"></a>Type d’objet blob non valide pour l’URI de l’objet blob source

**Description de l’erreur :** il n’est pas possible de télécharger un disque dur virtuel stocké en tant qu’objet blob de blocs. Il doit pour cela être stocké en tant qu’objet blob de pages.

**Solution suggérée :** chargez le disque dur virtuel dans le compte de stockage Azure en tant qu’objet blob de pages. Ensuite, téléchargez l’objet blob. Pour connaître les instructions de chargement, consultez [Chargement avec l’Explorateur Stockage](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload).


## <a name="only-blobs-formatted-as-vhds-can-be-imported"></a>Impossible d’importer des objets blob dans un format autre que celui de disque dur virtuel

**Description de l’erreur :** il n’est pas possible d’importer le disque dur virtuel, car il ne répond pas aux exigences de format. Il doit pour cela s’agir d’un disque dur virtuel de taille fixe de génération 1.

**Solutions suggérées :** 

- Suivez la procédure de la section [Préparation de l’image généralisée à partir du disque dur virtuel Windows pour déployer des machines virtuelles sur Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md) afin de créer un disque dur virtuel de taille fixe pour une machine virtuelle de génération 1 à partir de votre disque dur virtuel source (VHD ou VHDX).

- Si vous préférez utiliser PowerShell, procédez comme suit :

   - Vous pouvez utiliser [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps&preserve-view=true) dans le module Windows PowerShell pour Hyper-V. Il n’est pas possible de recourir à Convert-VHD pour convertir une image de machine virtuelle de la génération 2 à la génération 1. Suivez plutôt les procédures du portail décrites dans [Préparation de l’image généralisée à partir du disque dur virtuel Windows pour déployer des machines virtuelles sur Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).
  
   - Si vous avez besoin de déterminer le type de disque dur virtuel actuel, utilisez [Get-VHD](/powershell/module/hyper-v/get-vhd?view=windowsserver2019-ps&preserve-view=true).


## <a name="the-condition-specified-using-http-conditional-headers-is-not-met"></a>La condition spécifiée avec un ou plusieurs en-têtes conditionnels HTTP n’est pas remplie

**Description de l’erreur :** si des modifications sont apportées à un disque dur virtuel quand vous essayez de le télécharger à partir d’Azure, le téléchargement échoue. En effet, le disque dur virtuel dans Azure ne correspond pas au disque dur virtuel en cours de téléchargement. Cette erreur se produit également quand la tentative de téléchargement précède la fin du chargement du disque dur virtuel vers Azure.

**Solution suggérée :** attendez que le chargement du disque dur virtuel soit terminé et qu’aucune modification ne soit en cours sur le disque dur virtuel. Ensuite, réessayez de télécharger le disque dur virtuel.


## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [déployer des machines virtuelles avec le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
* Découvrez comment [déployer des machines virtuelles avec Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).
