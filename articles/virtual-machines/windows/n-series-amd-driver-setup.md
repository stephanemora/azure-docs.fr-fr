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
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269434"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installer les pilotes GPU AMD sur les machines virtuelles de série N exécutant Windows

Pour tirer parti des fonctionnalités GPU des nouvelles machines virtuelles de la série NVv4 Azure exécutant Windows, installez des pilotes GPU AMD. L’extension du pilote AMD sera disponible dans les prochaines semaines. Cet article indique les systèmes d’exploitation pris en charge, les pilotes et les étapes manuelles d’installation et de vérification.

Pour obtenir les spécifications de base, les capacités de stockage et les informations relatives aux disques, consultez [GPU Windows VM sizes](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Tailles de machine virtuelle Windows GPU).



## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

| Système d''exploitation | Pilote |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Installation du pilote

1. Connectez-vous à chaque machine virtuelle série NVv4 à l’aide du Bureau à distance.

2. Si vous êtes client NVv4 en préversion, arrêtez la machine virtuelle et attendez qu’elle passe à l’état Arrêté (désalloué).

3. Démarrez la machine virtuelle, puis désinstallez le pilote en préversion en exécutant « amdcleanuputility-x64.exe », situé dans le dossier « ...\AMDCleanUninstallUtility ». Le chemin exact varie en fonction de l’emplacement des fichiers d’installation du pilote précédent.  

4. Téléchargez et installez la dernière version du pilote.

5. Redémarrez la machine virtuelle.

## <a name="verify-driver-installation"></a>Vérification de l’installation du pilote

Vous pouvez vérifier l’installation du pilote dans le Gestionnaire de périphériques. L’exemple suivant illustre une configuration réussie de la carte Radeon Instinct MI25 sur une machine virtuelle NVv4 Azure.
<br />
![Propriétés du pilote GPU](./media/n-series-amd-driver-setup/device-manager.png)

Vous pouvez utiliser dxdiag pour vérifier les propriétés d'affichage du GPU, y compris la RAM vidéo. L’exemple suivant illustre une partition de 1/8e de la carte Radeon Instinct MI25 sur une machine virtuelle NVv4 Azure.
<br />
![Propriétés du pilote GPU](./media/n-series-amd-driver-setup/dxdiag.png)
