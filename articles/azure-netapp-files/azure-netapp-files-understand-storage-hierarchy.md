---
title: Qu’est-ce que la hiérarchie de stockage d’Azure NetApp Files | Microsoft Docs
description: Décrit la hiérarchie de stockage, y compris les comptes, les pools de capacité et les volumes Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/10/2020
ms.author: b-juche
ms.openlocfilehash: 91fecbc68efec1adcee9a2c4013dea46f6da86af
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066472"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Qu’est-ce que la hiérarchie de stockage d’Azure NetApp Files

Avant de créer un volume dans Azure NetApp Files, vous devez acheter et configurer un pool de capacité disponible.  Pour configurer un pool de capacité, vous devez disposer d’un compte NetApp. Comprendre la hiérarchie de stockage s’avère utile pour configurer et gérer vos ressources Azure NetApp Files.

> [!IMPORTANT] 
> Actuellement, Azure NetApp Files ne prend pas en charge la migration des ressources entre les abonnements.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Comptes NetApp

- Un compte NetApp est un groupement administratif de pools de capacité.  
- Un compte de NetApp diffère de votre compte de stockage Azure général. 
- Un compte NetApp a une portée régionale.   
- Vous pouvez avoir plusieurs comptes NetApp dans une région, mais chaque compte NetApp est lié à une seule région.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Pools de capacités

- Un pool de capacité se mesure par sa capacité allouée.  
- La capacité est approvisionnée via les références SKU fixes que vous avez achetées (par exemple, une capacité de 4 Tio).
- Un pool de capacité ne peut avoir qu’un seul niveau de service.  
- Chaque pool de capacité peut appartenir à un seul compte NetApp. Toutefois, vous pouvez avoir plusieurs pools de capacité au sein d’un compte NetApp.  
- Un pool de capacité ne peut pas être déplacé entre les comptes NetApp.   
  Par exemple, dans le [diagramme conceptuel de la hiérarchie de stockage](#conceptual_diagram_of_storage_hierarchy) ci-dessous, le Pool de capacité 1 ne peut pas être déplacé du compte NetApp USA Est au compte NetApp USA Ouest 2.  
- Impossible de supprimer un pool de capacités tant que tous les volumes qu’il contient n’ont pas été supprimés.

## <a name="volumes"></a><a name="volumes"></a>Volumes

- Un volume se mesure par la consommation de capacité logique et est évolutif. 
- La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.
- Chaque volume appartient à un seul pool, mais un pool peut contenir plusieurs volumes. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagramme conceptuel de la hiérarchie de stockage 
L’exemple suivant montre les relations entre l’abonnement Azure, les comptes NetApp, les pools de capacité et les volumes.   

![Diagramme conceptuel de la hiérarchie de stockage](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Étapes suivantes

- [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Register for Azure NetApp Files](azure-netapp-files-register.md) (S’inscrire à Azure NetApp Files)
