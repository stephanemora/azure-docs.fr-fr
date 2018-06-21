---
title: Extensions et fonctionnalités des machines virtuelles Azure | Microsoft Docs
description: Découvrir les extensions de machine virtuelle Azure et comment les utiliser avec des machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.openlocfilehash: 04f6d68feccf4a9b2bf2fa0f03ad8bd978cf5f17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653261"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensions et fonctionnalités des machines virtuelles Azure
Les extensions de machine virtuelle Azure sont de petites applications qui assurent des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. Vous pouvez utiliser des images existantes et les personnaliser dans le cadre de vos déploiements, pour vous dispenser de créer des images personnalisées.

La plateforme Azure héberge de nombreuses extensions, notamment des applications de monitoring, de sécurité, d’utilitaire et de configuration de machine virtuelle. Les éditeurs prennent une application, la wrappe dans une extension et simplifie l’installation, ainsi il ne vous reste plus qu’à fournir les paramètres obligatoires. 

 Il existe un grand choix d’extensions internes et tierces, et si l’application n’existe pas dans le dépôt d’extensions, libre à vous d’utiliser l’extension de script personnalisé et de configurer votre machine virtuelle avec vos propres scripts et commandes.

Exemples de scénarios clés pour lesquels vous utilisez des extensions :
* Configuration de machine virtuelle, en vous servant de DSC (Desired State Configuration) Powershell, Chef, Puppet et des extensions de scripts personnalisés pour installer des agents de configuration de machine virtuelle et configurer votre machine virtuelle. 
* Produits antivirus, tels que Symantec, ESET.
* Outil de vulnérabilité de machine virtuelle, tel que Qualys, Rapid7, HPE.
* Outils de monitoring d’application et de machine virtuelle, tels que DynaTrace, Azure Network Watcher, Site24x7 et Stackify.

Les extensions peuvent être englobées dans un nouveau déploiement de machine virtuelle. Par exemple, elles peuvent faire partie d’un déploiement plus vaste en configurant des applications lors du provisionnement de machines virtuelles, ou être exécutées en post-déploiement sur des systèmes d’exploitation prenant en charge ces extensions.

## <a name="how-can-i-find-what-extensions-are-available"></a>Comment savoir quelles extensions sont disponibles ?
Vous pouvez voir les extensions disponibles dans le panneau des machines virtuelles sur le portail, sous Extensions. Il ne s’agit là que d’un petit échantillon. Pour obtenir la liste complète, vous pouvez utiliser les outils CLI. Consultez [Découvrir les extensions de machine virtuelle pour Linux](features-linux.md) et [Découvrir les extensions de machine virtuelle pour Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Comment installer une extension ?
Vous pouvez gérer les extensions de machine virtuelle Azure en utilisant Azure CLI 2.0, Azure PowerShell, des modèles Azure Resource Manager et le portail Azure. Pour tester une extension, vous pouvez accéder au portail Azure, sélectionner l’extension de script personnalisé, puis passer dans une commande ou un script et exécuter les extensions.

Si vous souhaitez la même extension que celle que vous avez ajoutée dans le portail, en utilisant CLI ou un modèle Resource Manager, consultez la documentation se rapportant aux différentes extensions, par exemple [Extension de script personnalisé Windows](custom-script-windows.md) et [Extension de script personnalisé Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Comment gérer le cycle de vie de l’application de l’extension ?
Vous n’avez pas besoin de vous connecter directement à une machine virtuelle pour installer ou supprimer l’extension. Dans la mesure où le cycle de vie de l’application de l’extension Azure est géré en dehors de la machine virtuelle et qu’il est intégré à la plateforme Azure, vous obtenez également l’état intégré de l’extension.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Que savoir d’autre sur les extensions ?
Les extensions installent des applications, et comme pour toute application, il y a certaines conditions à respecter. En ce qui concerne les extensions, il existe une liste de systèmes d’exploitation Windows et Linux pris en charge ; l’installation des agents de machine virtuelle Azure est également obligatoire. Certaines applications d’extensions de machine virtuelle individuelle peuvent avoir leurs propres prérequis environnementaux, comme l’accès à un point de terminaison.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur le fonctionnement des extensions avec l’agent Linux, consultez [Fonctionnalités et extensions des machines virtuelles Azure pour Linux](features-linux.md).
* Pour plus d’informations sur le fonctionnement des extensions avec l’agent invité de Windows, consultez [Fonctionnalités et extensions des machines virtuelles Azure pour Windows](features-windows.md).  
* Pour installer l’agent invité de Windows, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure pour Windows](agent-windows.md).  
* Pour installer l’agent Linux, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure pour Linux](agent-linux.md).  

