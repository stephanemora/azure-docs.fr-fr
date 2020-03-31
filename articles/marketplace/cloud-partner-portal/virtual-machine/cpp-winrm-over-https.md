---
title: Windows Remote Management via le protocole HTTPS pour Azure | Place de marché Azure
description: Explique comment configurer une machine virtuelle Windows hébergée sur Azure, afin qu’elle puisse être gérée à distance avec PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 7c799c4a56ee5fc2074e4d644bdbcbc6d2b1ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288748"
---
# <a name="windows-remote-management-over-https"></a>Windows Remote Management via HTTPS

Cette section explique comment configurer une machine virtuelle Windows hébergée sur Azure, afin qu’elle puisse être gérée et déployée à distance avec PowerShell.  Pour activer la communication à distance PowerShell, la machine virtuelle cible doit exposer un point de terminaison HTTPS de gestion à distance de Windows (WinRM).  Pour plus d’informations sur la communication à distance PowerShell, consultez [Exécution des commandes à distance](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Pour plus d’informations sur WinRM, consultez [Gestion à distance de Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Si vous avez créé une machine virtuelle en utilisant l’une des approches Azure « classiques » : le portail Azure Service Manager ou la méthode déconseillée de l’[API Gestion des services Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100)), elle est alors automatiquement configurée avec un point de terminaison WinRM.  Toutefois, si vous créez une machine virtuelle en utilisant l’une des approches Azure « modernes » suivantes, votre machine virtuelle ne sera *pas* configurée pour WinRM via HTTPS.

- À l’aide du [Portail Azure portal](https://portal.azure.com/), généralement à partir d’une base approuvée, comme décrit dans la section [Créer un disque dur virtuel compatible avec Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Utilisation de modèles Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- À l’aide de l’interpréteur de commandes Azure PowerShell ou Azure CLI.  Pour obtenir des exemples, consultez [Démarrage rapide : créer une machine virtuelle Windows dans Azure avec PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) et [Démarrage rapide : Créer une machine virtuelle Linux avec Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Ce point de terminaison WinRM est également requis pour exécuter le kit d’outils de certification pour l’intégration de la machine virtuelle, comme décrit dans [Certifier votre image de machine virtuelle](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

En revanche, en général, les machines virtuelles Linux sont gérées à distance à l’aide d’[Azure CLI](https://docs.microsoft.com/cli/azure) ou des commandes Linux exécutées à partir d’une console SSH.  Azure fournit également plusieurs autres méthodes pour [exécuter des scripts dans votre machine virtuelle Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Pour des scénarios plus complexes, plusieurs solutions d’automatisation et d’intégration sont disponibles pour les machines virtuelles Windows ou Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurer et déployer les machines avec WinRM

Le point de terminaison WinRM pour une machine virtuelle Windows peut être configuré à deux étapes différentes de son développement :

- Pendant sa création : lors du déploiement d’une machine virtuelle sur un disque dur virtuel existant.  Il s’agit de l’approche privilégiée pour les nouvelles offres.  Cette approche nécessite la création d’un certificat Azure, à l’aide des modèles Azure Resource Manager fournis, et d’exécuter des scripts PowerShell personnalisés.
- Après le déploiement : sur une machine virtuelle existante hébergée sur Azure.  Utilisez cette approche si vous avez déjà déployé une solution de machine virtuelle sur Azure et que vous devez activer la gestion à distance de Windows sur celle-ci.  Cette approche nécessite des modifications manuelles dans le Portail Azure et l’exécution d’un script sur la machine virtuelle cible.


## <a name="next-steps"></a>Étapes suivantes
Si vous créez une machine virtuelle, vous pouvez activer WinRM pendant le [déploiement de votre machine virtuelle à partir de ses disques durs virtuels](./cpp-deploy-vm-vhd.md).  Sinon, WinRM peut être activée dans une machine virtuelle existante
