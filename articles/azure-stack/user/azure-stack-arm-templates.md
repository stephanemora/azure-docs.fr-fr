---
title: Utiliser les modèles Azure Resource Manager dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser les modèles Azure Resource Manager dans Azure Stack pour approvisionner des ressources.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301676"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utiliser les modèles Azure Resource Manager dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser les modèles Azure Resource Manager pour déployer et provisionner toutes les ressources de votre application en une seule opération coordonnée. Vous pouvez également redéployer des modèles pour apporter des changements aux ressources d’un groupe de ressources.

Ces modèles peuvent être déployés à l’aide du portail Microsoft Azure Stack, de PowerShell, de l’interface de ligne de commande et de Visual Studio.

Les modèles de démarrage rapide suivants sont disponibles sur [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Déployer SharePoint Server (déploiement sans haute disponibilité)

Utilisez l’extension DSC PowerShell pour créer une batterie de serveurs SharePoint Server 2013 comprenant les ressources suivantes :

* Un réseau virtuel
* Trois comptes de stockage
* Deux équilibreurs de charge externes
* Une machine virtuelle configurée comme contrôleur de domaine dans une nouvelle forêt avec un domaine unique
* Une machine virtuelle configurée sous la forme d’un serveur autonome SQL Server 2014
* Une machine virtuelle configurée comme batterie de serveurs SharePoint Server 2013 composée d’une seule machine

## <a name="deploy-ad-non-high-availability-deployment"></a>Déployer AD (déploiement sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour créer un serveur de contrôleurs de domaine AD comprenant les ressources suivantes :

* Un réseau virtuel
* Un compte de stockage
* Un équilibreur de charge externe
* Une machine virtuelle configurée comme contrôleur de domaine dans une nouvelle forêt avec un domaine unique

## <a name="deploy-adsql-non-high-availability-deployment"></a>Déployer AD/SQL (déploiement sans haute disponibilité)

Utilisez l’extension PowerShell DSC pour créer un serveur autonome SQL Server 2014 comprenant les ressources suivantes :

* Un réseau virtuel
* deux comptes de stockage ;
* Un équilibreur de charge externe
* Une machine virtuelle configurée comme contrôleur de domaine dans une nouvelle forêt avec un domaine unique
* Une machine virtuelle configurée sous la forme d’un serveur autonome SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilisez l’extension PowerShell DSC pour configurer un gestionnaire local de configuration (LCM) de machines virtuelles existant et enregistrez-le dans un serveur Pull DSC de compte Azure Automation.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Création d’une machine virtuelle à partir d’une image utilisateur

Créez une machine virtuelle à partir d’une image utilisateur personnalisée. Ce modèle déploie également un réseau virtuel (avec DNS), une adresse IP publique et une interface réseau.

## <a name="basic-virtual-machine"></a>Machine virtuelle classique

Déployez une machine virtuelle Windows comprenant un réseau virtuel (avec DNS), une adresse IP publique et une interface réseau.

## <a name="cancel-a-running-template-deployment"></a>Annuler le déploiement d’un modèle en cours d’exécution

Pour annuler le déploiement d’un modèle en cours d’exécution, utilisez la cmdlet PowerShell `Stop-AzureRmResourceGroupDeployment`.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec le portail](azure-stack-deploy-template-portal.md)
* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
