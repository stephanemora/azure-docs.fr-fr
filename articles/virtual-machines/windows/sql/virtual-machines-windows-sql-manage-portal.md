---
title: Gérer des machines virtuelles SQL Server dans Azure avec le portail Azure | Microsoft Docs
description: Découvrez comment accéder à la ressource Machine virtuelle SQL dans le portail Azure pour une machine virtuelle SQL Server hébergée sur Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607225"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Gérer des machines virtuelles SQL Server dans Azure avec le portail Azure

Il existe un nouveau point d’accès pour gérer votre machine virtuelle SQL Server sur Azure avec le [portail Azure](https://portal.azure.com). 

La ressource **Machines virtuelles SQL** est maintenant un service de gestion indépendant qui vous permet de voir simultanément toutes vos machines virtuelles SQL Server et de modifier des paramètres dédiés à SQL Server : 

![Ressource Machines virtuelles SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Remarques

- La ressource **Machines virtuelles SQL** est la méthode recommandée pour voir et gérer vos machines virtuelles SQL Server. Cependant, actuellement, la ressource **Machines virtuelles SQL** ne prend pas en charge la gestion des machines virtuelles SQL Server [en fin de support](virtual-machines-windows-sql-server-2008-eos-extend-support.md). Pour gérer les paramètres de vos machines virtuelles en fin de support SQL Server, utilisez à la place [l’onglet Configuration de SQL Server](#access-sql-server-configuration-tab). 
- La ressource **Machines virtuelles SQL** est disponible seulement pour les machines virtuelles SQL Server qui ont été [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-sql-virtual-machine-resource"></a>Accéder à la ressource Machines virtuelles SQL
Pour accéder à la ressource Machines virtuelles SQL, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Tous les services**. 
1. Tapez `SQL virtual machines` dans la zone de recherche.
1. (Facultatif) : Sélectionnez l’étoile en regard de **Machines virtuelles SQL** pour ajouter cette option à votre menu Favoris. 
1. Sélectionnez **Machines virtuelles SQL**. 

   ![Rechercher des machines virtuelles SQL dans tous les services](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Cette opération liste toutes les machines virtuelles SQL Server disponibles dans l’abonnement. Sélectionnez celle que vous voulez gérer pour lancer la ressource **Machines virtuelles SQL**. Utilisez la zone de recherche si votre machine virtuelle SQL Server n’apparaît pas rapidement. 

![Toutes les machines virtuelles SQL disponibles](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

La sélection de votre machine virtuelle SQL Server ouvre la ressource **Machines virtuelles SQL** : 


![Ressource Machines virtuelles SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > La ressource **Machines virtuelles SQL** est destinée aux paramètres SQL Server dédiés. Sélectionnez le nom de la machine virtuelle dans le champ **Machine virtuelle** pour accéder à des paramètres qui sont spécifiques à la machine virtuelle, mais ne concernent pas exclusivement SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Accéder à l’onglet Configuration de SQL Server
L’onglet Configuration de SQL Server a été déprécié. Pour l’instant, il s’agit de la seule méthode permettant de gérer les machines virtuelles SQL Server en [fin de support](virtual-machines-windows-sql-server-2008-eos-extend-support.md) et les machines virtuelles SQL Server qui n’ont pas été [inscrites auprès du fournisseur de ressources de machine virtuelle SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Pour accéder à l’onglet déprécié Configuration de SQL Server, vous devez accéder à la ressource **Machines virtuelles**. Pour ce faire, procédez comme suit :

1. Ouvrez le [portail Azure](https://portal.azure.com). 
1. Sélectionnez **Tous les services**. 
1. Tapez `virtual machines` dans la zone de recherche.
1. (Facultatif) : Sélectionnez l’étoile en regard de **Machines virtuelles** pour ajouter cette option à votre menu Favoris. 
1. Sélectionnez **Machines virtuelles**. 

   ![Rechercher des machines virtuelles](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Ceci liste toutes les machines virtuelles dans l’abonnement. Sélectionnez celle que vous voulez gérer pour lancer la ressource **Machine virtuelle**. Utilisez la zone de recherche si votre machine virtuelle SQL Server n’apparaît pas rapidement. 
1. Sélectionnez **Configuration de SQL Server** dans le volet **Paramètres** pour gérer votre serveur SQL Server. 

![Configuration de SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants : 

* [Vue d’ensemble de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Questions fréquentes (FAQ) relatives à SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guide des tarifs de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notes de publication de SQL Server sur une machine virtuelle Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


