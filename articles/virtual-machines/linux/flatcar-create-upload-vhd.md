---
title: Création et chargement d’un disque dur virtuel Flatcar Container Linux pour Azure
description: Apprenez à créer et à charger un disque dur virtuel contenant un système d’exploitation Flatcar Container Linux.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 2bdbe93d5d593d429c4f7073227684c1e361864d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527949"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Utilisation d’une image Flatcar prédéfinie pour Azure

Vous pouvez télécharger des images de disque dur virtuel Azure prédéfinies de Flatcar Container Linux pour chacun des canaux Flatcar pris en charge :

- [stable](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [beta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [alpha](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Cette image est déjà entièrement configurée et optimisée pour s’exécuter sur Azure. Il vous suffit de la décompresser.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Création d’une machine virtuelle Flatcar pour Azure

Vous pouvez également choisir de créer votre propre image Flatcar Container Linux.

Sur n’importe quelle machine Linux, suivez les instructions détaillées dans le [Guide du kit SDK de développement Flatcar Container Linux](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). Lors de l’exécution du script `image_to_vm.sh`, veillez à indiquer `--format=azure` pour créer un disque dur virtuel Azure.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous disposez du fichier. vhd, vous pouvez utiliser le fichier résultant pour créer des machines virtuelles dans Azure. Si c’est la première fois que vous chargez un fichier .vhd sur Azure, consultez [Création d’une machine virtuelle Linux à partir d’un disque personnalisé](upload-vhd.md#option-1-upload-a-vhd).
