---
title: Comprendre la hiérarchie de stockage de Azure NetApp Files | Microsoft Docs
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
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: 1cce1883295277f6c6c36d686d90370238265dbf
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775847"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Comprendre la hiérarchie de stockage de Azure NetApp Files

Avant de créer un volume dans Azure NetApp Files, vous devez acheter et configurer un pool de capacité disponible.  Pour configurer un pool de capacité, vous devez disposer d’un compte NetApp. Comprendre la hiérarchie de stockage s’avère utile pour configurer et gérer vos ressources Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Comptes NetApp

- Un compte NetApp est un groupement administratif de pools de capacité.  
- Un compte de NetApp diffère de votre compte de stockage Azure général. 
- Un compte NetApp a une portée régionale.   
- Vous pouvez avoir plusieurs comptes NetApp dans une région, mais chaque compte NetApp est lié à une seule région.

## <a name="capacity_pools"></a>Pools de capacités

- Un pool de capacité se mesure par sa capacité allouée.  
- La capacité est approvisionnée via les références SKU fixes que vous avez achetées (par exemple, une capacité de 4 Tio).
- La taille minimale d’un pool de capacité est de 4 Tio et sa taille maximale est de 500 Tio. 
- Un pool de capacité ne peut avoir qu’un seul niveau de service.  
  Actuellement, seul le niveau de service Premium est disponible.
- Chaque pool de capacité peut appartenir à un seul compte NetApp. Toutefois, vous pouvez avoir plusieurs pools de capacité au sein d’un compte NetApp.  
- Un pool de capacité ne peut pas être déplacé entre les comptes NetApp.   
  Par exemple, dans le [diagramme conceptuel de la hiérarchie de stockage](#conceptual_diagram_of_storage_hierarchy) ci-dessous, le Pool de capacité 1 ne peut pas être déplacé du compte NetApp Est des États-Unis au compte NetApp Ouest des États-Unis 2.  

## <a name="volumes"></a>Volumes

- Un volume se mesure par la consommation de capacité logique et est évolutif. La taille minimale d’un volume est de 100 Gio et sa taille maximale est de 92 Tio.
- La consommation de capacité d’un volume est comptée par rapport à la capacité configurée de son pool.
-   Vous pouvez avoir un maximum de 100 volumes par abonnement Azure et par région. 
- Chaque volume appartient à un seul pool, mais un pool peut contenir plusieurs volumes. 
- Vous pouvez déplacer un volume entre différents pools d’un même compte NetApp.    
  Par exemple, dans le [diagramme conceptuel de la hiérarchie de stockage](#conceptual_diagram_of_storage_hierarchy) ci-dessous, vous pouvez déplacer les volumes du Pool de capacité de 1 au Pool de capacité 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagramme conceptuel de la hiérarchie de stockage 
L’exemple suivant montre les relations entre l’abonnement Azure, les comptes NetApp, les pools de capacité et les volumes.   

![Diagramme conceptuel de la hiérarchie de stockage](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Étapes suivantes

- [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Register for Azure NetApp Files](azure-netapp-files-register.md) (S’inscrire à Azure NetApp Files)
