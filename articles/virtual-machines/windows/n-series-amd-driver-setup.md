---
title: Configuration des pilotes GPU AMD de série N Azure pour Windows
description: Procédure de configuration des pilotes GPU AMD pour les machines virtuelles série N exécutant Windows Serveur ou Windows dans Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 6f0aa8a08b8a502edbd15d3ede157b78a13b8588
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474791"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installer les pilotes GPU AMD sur les machines virtuelles de série N exécutant Windows

Pour tirer parti des fonctionnalités GPU des nouvelles machines virtuelles de la série NVv4 Azure exécutant Windows, installez des pilotes GPU AMD. L’extension du pilote AMD sera disponible dans les prochaines semaines. Cet article indique les systèmes d’exploitation pris en charge, les pilotes et les étapes manuelles d’installation et de vérification.

Pour obtenir les spécifications de base, les capacités de stockage et les informations relatives aux disques, consultez [GPU Windows VM sizes](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Tailles de machine virtuelle Windows GPU).



## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

| Système d''exploitation | Pilote |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19.Q4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (.exe) |

## <a name="driver-installation"></a>Installation du pilote

1. Connectez-vous à chaque machine virtuelle série NVv4 à l’aide du Bureau à distance.

1. Téléchargez, extrayez et installez le pilote pris en charge pour votre système d’exploitation Windows.

## <a name="verify-driver-installation"></a>Vérification de l’installation du pilote

Vous pouvez vérifier l’installation du pilote dans le Gestionnaire de périphériques. L’exemple suivant illustre une configuration réussie de la carte Radeon Instinct MI25 sur une machine virtuelle NVv4 Azure.
<br />
![Propriétés du pilote GPU](./media/n-series-amd-driver-setup/device-manager.png)

Vous pouvez utiliser dxdiag pour vérifier les propriétés d'affichage du GPU, y compris la RAM vidéo. L’exemple suivant illustre une partition de 1/8e de la carte Radeon Instinct MI25 sur une machine virtuelle NVv4 Azure.
<br />
![Propriétés du pilote GPU](./media/n-series-amd-driver-setup/dxdiag.png)
