---
title: Exemples d’interface de ligne de commande Azure sur Windows | Microsoft Docs
description: Exemples d’interface de ligne de commande Azure sur Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/01/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6861399b63b7f06bac7599704a6dd1aa87800ebf
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49403336"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Exemples d’interface de ligne de commande Azure pour machines virtuelles Windows

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure pour déployer des machines virtuelles Windows.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle Windows avec une configuration minimale. |
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer IIS. |
| [Créer une machine virtuelle et exécuter la configuration DSC](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle et utilise l’extension DSC Azure pour installer IIS. |
|**Mettre en réseau des machines virtuelles**||
| [Sécuriser le trafic réseau entre les machines virtuelles](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Crée deux machines virtuelles, toutes les ressources associées, ainsi qu’un groupe de sécurité réseau interne et un groupe de sécurité réseau externe. |
|**Sécuriser les machines virtuelles**||
| [Chiffrer une machine virtuelle et des disques de données](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une solution Key Vault, une clé de chiffrement et le principal de service, puis chiffre une machine virtuelle. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Log Analytics](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle, installe l’agent Log Analytics et inscrit la machine virtuelle auprès d’un espace de travail Log Analytics.  |
| | |
