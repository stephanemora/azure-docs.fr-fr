---
title: Exemples PowerShell pour les machines virtuelles Azure
description: Exemples PowerShell pour les machines virtuelles Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321750"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Exemples PowerShell pour les machines virtuelles Azure

Le tableau suivant fournit des liens vers des exemples de script PowerShell qui créent et gèrent des machines virtuelles (VM) Windows.

| Script | Description |
|---|---|
|**Créer des machines virtuelles**||
| [Créer rapidement une machine virtuelle](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées avec un minimum d’invites.|
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge.|
| [Créer une machine virtuelle et exécuter un script de configuration](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer IIS. |
| [Créer une machine virtuelle et exécuter une configuration DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle et utilise l’extension DSC Azure pour installer IIS. |
| [Charger un disque dur virtuel et créer des machines virtuelles](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Charge un fichier de disque dur virtuel local sur Azure, crée une image à partir du disque dur virtuel, puis crée une machine virtuelle à partir de cette image. |
| [Créer une machine virtuelle à partir d’un disque de système d’exploitation géré](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle en attachant un disque géré existant en tant que disque de système d’exploitation. |
| [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle à partir d’une capture instantanée en créant d’abord un disque managé à partir de la capture instantanée, puis en attachant le nouveau disque géré en tant que disque de système d’exploitation. |
|**Gérer le stockage**||
| [Créer un disque managé à partir d’un disque dur virtuel figurant dans le même abonnement ou dans un abonnement différent](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un disque managé à partir d’un disque dur virtuel spécialisé tel qu’un disque de système d’exploitation, ou d’un disque dur virtuel de données figurant dans le même abonnement ou dans un abonnement différent.  |
| [Créer un disque géré à partir d’une capture instantanée](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un disque géré à partir d’une capture instantanée. |
| [Copier un disque managé vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copie un disque managé vers le même abonnement ou un abonnement différent, mais dans la même région que le disque géré parent.
| [Exporter une capture instantanée en tant que disque dur virtuel vers un compte de stockage](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporte une capture instantanée gérée en tant que disque dur virtuel vers un compte de stockage dans une région différente. |
| [Exporter le disque dur virtuel d’un disque managé vers un compte de stockage](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Exporte le disque dur virtuel sous-jacent d’un disque managé vers un compte de stockage situé dans une autre région. |
| [Créer une capture instantanée d’un disque dur virtuel](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une capture instantanée d’un disque dur virtuel, puis l’utilise pour créer rapidement plusieurs disques managés identiques.  |
| [Copier une capture instantanée vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Copie une capture instantanée vers le même abonnement ou un abonnement différent, dans la même région que la capture instantanée parente. |
|**Sécuriser les machines virtuelles**||
| [Chiffrer une machine virtuelle et ses disques de données](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Crée un coffre de clés Azure, une clé de chiffrement et un principal de service, puis chiffre une machine virtuelle. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Azure Monitor](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée une machine virtuelle, installe l’agent Azure Log Analytics et inscrit la machine virtuelle auprès d’un espace de travail Log Analytics.  |
| [Collecter plus d’informations sur toutes les machines virtuelles dans un abonnement avec PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Crée un fichier csv qui contient le nom de la machine virtuelle, le nom du groupe de ressources, la région, le réseau virtuel, le sous-réseau, l’adresse IP privée, le type de système d’exploitation et l’adresse IP publique des machines virtuelles dans l’abonnement fourni.
| | |
