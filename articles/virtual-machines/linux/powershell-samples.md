---
title: Exemples PowerShell
description: Liste d’exemples PowerShell pour les machines virtuelles
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: b1a154e562db9f8fdd74300bb8eb3d6839cb2ef4
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321920"
---
# <a name="azure-vm-powershell-samples-for-creating-and-managing-linux-vms"></a>Exemples PowerShell de machines virtuelles Azure pour la création et la gestion de machines virtuelles Linux

Le tableau suivant contient des liens vers des exemples de scripts PowerShell qui créent et gèrent des machines virtuelles Linux.

| Script | Description |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer une machine virtuelle prenant en charge Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle, configure cette machine virtuelle en tant qu’hôte Docker et exécute un conteneur NGINX. |
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer NGINX. |
| [Créer une machine virtuelle avec WordPress](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer WordPress. |
| [Créer une machine virtuelle à partir d’un disque de système d’exploitation géré](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle en attachant un disque géré existant en tant que disque de système d’exploitation. |
| [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle à partir d’une capture instantanée en créant d’abord un disque managé à partir de la capture instantanée, puis en attachant le nouveau disque géré en tant que disque de système d’exploitation. |
|**Gérer le stockage**||
| [Créer un disque managé à partir d’un disque dur virtuel figurant dans le même abonnement ou dans un abonnement différent](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée un disque managé à partir d’un disque dur virtuel spécialisé tel qu’un disque de système d’exploitation, ou d’un disque dur virtuel de données figurant dans le même abonnement ou dans un abonnement différent.  |
| [Créer un disque géré à partir d’une capture instantanée](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée un disque géré à partir d’une capture instantanée. |
| [Exporter une capture instantanée en tant que disque dur virtuel vers un compte de stockage](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporte une capture instantanée gérée en tant que disque dur virtuel vers un compte de stockage dans une région différente. |
| [Exporter le disque dur virtuel d’un disque managé vers un compte de stockage](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Exporte le disque dur virtuel sous-jacent d’un disque managé vers un compte de stockage situé dans une autre région. |
| [Créer une capture instantanée d’un disque dur virtuel](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une capture instantanée d’un disque dur virtuel, puis l’utilise pour créer rapidement plusieurs disques managés identiques.  |
| [Copier une capture instantanée vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copie une capture instantanée vers le même abonnement ou un abonnement différent, dans la même région que la capture instantanée parente. |
|**Surveiller les machines virtuelles**||
| [Superviser une machine virtuelle avec les journaux Azure Monitor](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée une machine virtuelle, installe l’agent Log Analytics et inscrit la machine virtuelle auprès d’un espace de travail Log Analytics.  |
| [Copier un disque managé vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Copie un disque managé vers le même abonnement ou un abonnement différent, mais dans la même région que le disque géré parent.
| [Collecter plus d’informations sur toutes les machines virtuelles dans un abonnement avec PowerShell](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Crée un fichier csv qui contient le nom de la machine virtuelle, le nom du groupe de ressources, la région, le réseau virtuel, le sous-réseau, l’adresse IP privée, le type de système d’exploitation et l’adresse IP publique des machines virtuelles dans l’abonnement fourni.
| | |
