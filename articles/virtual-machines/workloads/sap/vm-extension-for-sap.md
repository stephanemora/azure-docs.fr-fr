---
title: Implémenter l’extension de machine virtuelle Azure pour les solutions SAP | Microsoft Docs
description: Découvrez comment déployer l’extension de machine virtuelle pour SAP.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: bc85e68ed643e8a39f22343cfb939ab803fcdaef
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067490"
---
# <a name="implement-the-azure-vm-extension-for-sap-solutions"></a>Implémenter l’extension de machine virtuelle Azure pour les solutions SAP | Microsoft

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-resources]:vm-extension-for-sap.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Ressources SAP)
[new-monitoring]:vm-extension-for-sap.md#38d9f33f-d0af-4b8f-8134-f1f97d656fb6 (Nouvelle version de l’Extension VM pour SAP)
[std-extension]:vm-extension-for-sap-standard.md (Version standard de l’Extension Azure VM pour les solutions SAP)
[new-extension]:vm-extension-for-sap-new.md (Nouvelle version de l’Extension Azure VM pour les solutions SAP)
[azure-ps]:/powershell/azure/
[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli

[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scénarios de déploiement de machines virtuelles pour SAP sur Microsoft Azure)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solution de supervision Azure pour SAP)

> [!NOTE]
> Instruction générale de support : la prise en charge de l’extension Azure pour SAP est assurée par le biais des canaux de support SAP. Si vous avez besoin d’aide sur l’extension de machine virtuelle Azure pour solutions SAP, veuillez ouvrir un dossier de support auprès du support SAP.
 
Une fois que la machine virtuelle est préparée comme décrit dans [Scénarios de déploiement de machines virtuelles pour SAP sur Azure][deployment-guide-3], l’agent de machine virtuelle Azure est installé sur la machine virtuelle. L’étape suivante consiste à déployer l’extension Azure pour SAP, qui est disponible dans le référentiel d’extensions Azure dans les centres de données mondiaux d’Azure. Pour plus d’informations, consultez [SAP NetWeaver sur machines virtuelles Azure – Guide de planification et d’implémentation][planning-guide-9.1].
 
Pour être sûr que SAP est compatible avec votre environnement, activez l’extension de machine virtuelle Azure pour solutions SAP, comme décrit dans Configurer l’extension Azure pour SAP.

## <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Ressources SAP

Lorsque vous configurez votre déploiement de logiciels SAP, vous avez besoin des ressources SAP suivantes :

* Note SAP [1928533], qui contient :
  * une liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP
  * des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure
  * les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données
  * la version du noyau SAP requise pour Windows et Linux sur Microsoft Azure

* La note SAP [2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [1409604] contient la version requise de l’agent hôte SAP pour Windows sur Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1999351] contient des informations complémentaires de résolution des problèmes liés à l’extension Azure pour SAP.
* Applets de commande PowerShell spécifiques à SAP qui font partie d’[Azure PowerShell][azure-ps].
* Commandes d’interface de ligne de commande Azure spécifiques à SAP faisant partie de l’[interface de ligne de commande Azure][azure-cli-2].
 
## <a name="differences-between-the-two-versions-of-the-azure-vm-extension-for-sap-solutions"></a>Différences entre les deux versions de l’extension de machine virtuelle Azure pour solutions SAP

L’extension de machine virtuelle pour SAP se présente sous deux versions. Consultez la configuration requise de SAP pour connaître les versions minimales requises pour le noyau SAP et l’agent hôte SAP dans les ressources listées dans [Ressources SAP][sap-resources].

### <a name="standard-version-of-vm-extension-for-sap"></a>Version standard de l’extension de machine virtuelle pour SAP

Cette version est l’extension de machine virtuelle standard actuelle pour SAP. Il existe quelques exceptions pour lesquelles Microsoft recommande d’installer la nouvelle extension de machine virtuelle pour SAP. En outre, lors de l’ouverture d’un dossier de support, le support SAP est en mesure de demander l’installation de la nouvelle extension de machine virtuelle. Pour plus d’informations sur l’utilisation de la nouvelle version de l’extension de machine virtuelle pour SAP, consultez le chapitre [Nouvelle version de l’extension de machine virtuelle pour SAP][new-monitoring]
 
### <a name="new-version-of-vm-extension-for-sap"></a><a name="38d9f33f-d0af-4b8f-8134-f1f97d656fb6"></a>Nouvelle version de l’extension de machine virtuelle pour SAP

Cette version est la nouvelle extension de machine virtuelle Azure pour solutions SAP. Grâce aux améliorations supplémentaires et aux nouvelles offres Azure, la nouvelle extension a été conçue pour pouvoir surveiller toutes les ressources Azure d’une machine virtuelle. Cette extension a besoin d’un accès Internet à l’URL « management.azure.com ». Elle prend en charge des options de stockage supplémentaires, par exemple des disques standard et des systèmes d’exploitation. Choisissez la nouvelle version de l’extension de machine virtuelle si l’une des conditions suivantes s’applique :
 
* Vous souhaitez installer l’extension de machine virtuelle avec Terraform, les modèles Azure Resource Manager ou tout moyen autre qu’Azure CLI ou Azure PowerShell
* Vous souhaitez installer l’extension sur SUSE SLES 15 ou une version ultérieure.
* vous souhaitez installer l’extension sur Red Hat Enterprise Linux 8.1 ou une version ultérieure.
* Vous souhaitez utiliser le Disque Ultra ou les Disques managés standard Azure.
* Le support Microsoft ou SAP vous demande d’installer la nouvelle extension.
 
## <a name="recommendation"></a>Recommandation

Nous vous recommandons actuellement d’utiliser la version standard de l’extension pour chaque installation dans laquelle aucun des cas d’usage de la nouvelle version de l’extension ne s’applique. Nous travaillons actuellement à l’amélioration de la nouvelle version de l’extension de machine virtuelle pour pouvoir en faire la valeur par défaut et déprécier la version standard de l’extension. Pendant ce temps, vous pouvez utiliser la nouvelle version. Toutefois, vous devez vous assurer que l’extension de machine virtuelle peut accéder à management.azure.com.
 
> [!NOTE]
> Veillez à désinstaller l’extension de machine virtuelle avant de passer d’une version à l’autre.

## <a name="next-steps"></a>Étapes suivantes
* [Version standard de l’extension de machine virtuelle Azure pour les solutions SAP][std-extension]
* [Nouvelle version de l’extension de machine virtuelle Azure pour les solutions SAP][new-extension]

