---
title: Créer une machine virtuelle Linux classique à l’aide d’Azure Classic CLI | Microsoft Docs
description: Découvrir comment créer une machine virtuelle Linux avec l’interface Azure Classic CLI à l’aide du modèle de déploiement Classic
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: cynthn
ms.openlocfilehash: 507d9e12a37d7bf187a3e56b04cb47ac0104773d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982023"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Guide pratique pour créer une machine virtuelle Linux classique à l’aide d’Azure Classic CLI
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour la version de Resource Manager, suivi [ce lien](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Cette rubrique décrit comment créer une machine virtuelle Linux avec l’interface Azure Classic CLI à l’aide du modèle de déploiement Classic. Nous utilisons une image Linux provenant des **IMAGES** disponibles sur Azure. Les commandes Azure Classic CLI proposent notamment les choix de configuration suivants :

* Connexion de la machine virtuelle à un réseau virtuel
* Ajout de la machine virtuelle à un service cloud existant
* Ajout de la machine virtuelle à un compte de stockage existant
* Ajout de la machine virtuelle à un groupe à haute disponibilité

> [!IMPORTANT]
> Si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d’hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Création d’une machine virtuelle Linux à l’aide du modèle de déploiement Classic
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]

