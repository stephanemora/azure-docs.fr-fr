---
title: Extensions et fonctionnalités des machines virtuelles Azure
description: Découvrir les extensions de machine virtuelle Azure et comment les utiliser avec des machines virtuelles Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74072974"
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
Vous pouvez gérer les extensions de machine virtuelle Azure en utilisant Azure CLI, Azure PowerShell, des modèles Azure Resource Manager et le portail Azure. Pour tester une extension, vous pouvez accéder au portail Azure, sélectionner l’extension de script personnalisé, puis passer dans une commande ou un script et exécuter les extensions.

Si vous souhaitez la même extension que celle que vous avez ajoutée dans le portail, en utilisant CLI ou un modèle Resource Manager, consultez la documentation se rapportant aux différentes extensions, par exemple [Extension de script personnalisé Windows](custom-script-windows.md) et [Extension de script personnalisé Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Comment gérer le cycle de vie de l’application de l’extension ?
Vous n’avez pas besoin de vous connecter directement à une machine virtuelle pour installer ou supprimer l’extension. Dans la mesure où le cycle de vie de l’application de l’extension Azure est géré en dehors de la machine virtuelle et qu’il est intégré à la plateforme Azure, vous obtenez également l’état intégré de l’extension.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Que savoir d’autre sur les extensions ?
Les extensions installent des applications, et comme pour toute application, il y a certaines conditions à respecter. En ce qui concerne les extensions, il existe une liste de systèmes d’exploitation Windows et Linux pris en charge ; l’installation des agents de machine virtuelle Azure est également obligatoire. Certaines applications d’extensions de machine virtuelle individuelle peuvent avoir leurs propres prérequis environnementaux, comme l’accès à un point de terminaison.

## <a name="troubleshoot-extensions"></a>Résoudre les problèmes liés aux extensions

Vous trouverez des informations pour résoudre les problèmes liés à chaque extension dans la section **Résolution des problèmes et support** de la vue d’ensemble de l’extension. Voici la liste des informations disponibles pour résoudre les problèmes :

| Espace de noms | Dépannage |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor Dependency pour Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Azure Monitor Dependency pour Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure Disk Encryption pour Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Azure Disk Encryption pour Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Script personnalisé pour Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Desired State Configuration pour Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Desired State Configuration pour Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Extension du pilote GPU NVIDIA pour Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Extension du pilote GPU NVIDIA pour Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Extension anti-programme malveillant pour Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure Monitor pour Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor pour Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace pour Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Réinitialiser le mot de passe (VMAccess) pour Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Capture instantanée pour Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Capture instantanée pour Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur le fonctionnement des extensions avec l’agent Linux, consultez [Fonctionnalités et extensions des machines virtuelles Azure pour Linux](features-linux.md).
* Pour plus d’informations sur le fonctionnement des extensions avec l’agent invité de Windows, consultez [Fonctionnalités et extensions des machines virtuelles Azure pour Windows](features-windows.md).  
* Pour installer l’agent invité Windows, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure pour Windows](agent-windows.md).  
* Pour installer l’agent Linux, consultez [Vue d’ensemble de l’agent de machine virtuelle Azure pour Linux](agent-linux.md).  

