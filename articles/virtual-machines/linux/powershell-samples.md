---
title: Exemples PowerShell pour les machines virtuelles Azure | Microsoft Docs
description: Exemples PowerShell pour les machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: cynthn
ms.openlocfilehash: 10c72f84a13eb1edb653d23722cb03ee97298c6b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407620"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Exemples PowerShell pour les machines virtuelles Azure

Le tableau suivant contient des liens vers des exemples de scripts PowerShell qui créent et gèrent des machines virtuelles Linux.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer une machine virtuelle prenant en charge Docker](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle, configure cette machine virtuelle en tant qu’hôte Docker et exécute un conteneur NGINX. |
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer NGINX. |
| [Créer une machine virtuelle avec WordPress](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer WordPress. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Azure Log Analytics](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crée une machine virtuelle, installe l’agent Log Analytics et inscrit la machine virtuelle auprès d’un espace de travail Log Analytics.  |
| | |
