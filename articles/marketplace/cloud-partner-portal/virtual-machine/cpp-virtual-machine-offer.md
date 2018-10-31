---
title: Offre de machine virtuelle dans la Place de marché Microsoft Azure | Microsoft Docs
description: Vue d’ensemble du processus de publication d’une offre de machine virtuelle sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/28/2018
ms.author: pbutlerm
ms.openlocfilehash: d3682d18fb849b2d851bae0986f9e61f216aaf2c
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639131"
---
# <a name="virtual-machine-offer"></a>Offre de machine virtuelle

Cette section décrit les éléments de la publication d’une machine virtuelle (VM) et est conçue comme un guide de publication pour la [place de marché Microsoft Azure](https://azuremarketplace.microsoft.com).  De ce point de vue, il comprend les parties principales suivantes :

- [Conditions préalables](./cpp-prerequisites.md) : répertorie les exigences techniques et commerciales avant de créer ou de publier une offre de machine virtuelle
- [Créer une offre de machine virtuelle](./cpp-create-offer.md) : répertorie les étapes requises pour créer une offre de machine virtuelle à l’aide du [Portail Microsoft Cloud Partner](https://cloudpartner.azure.com).
- [Créer des ressources techniques de machine virtuelle](./cpp-create-technical-assets.md) : explique comment créer les ressources techniques pour une solution de machine virtuelle et configurer ce package comme une offre de machine virtuelle sur la place de marché Microsoft Azure.
- [Publier l’offre de machine virtuelle](./cpp-publish-offer.md) : explique comment publier l’offre sur la Place de marché Microsoft Azure.


## <a name="vm-publishing-process-flow"></a>Diagramme du processus de publication de machine virtuelle

Le schéma suivant illustre les étapes principales de la publication d’une offre de machine virtuelle. 

![Processus de publication de machine virtuelle](./media/publishvm_001.png)

1. Créer l’offre : tous les détails et les informations sur l’offre sont configurés, y compris la description de l’offre, les documents marketing, les références juridiques, les informations de support et les spécifications des ressources.

2. Créer les ressources commerciales et techniques : créez les ressources commerciales (documents juridiques et supports marketing) et techniques de la solution associée (en l’occurrence, les machines virtuelles et les disques associés). 

3. Créer la référence SKU : créez la ou les références SKU associées à l’offre et envoyez-les.  Chaque image que vous prévoyez de publier doit avoir une référence SKU unique. 
 
4. Certifier et publier l’offre : une fois l’offre et les ressources techniques finalisées, vous pouvez envoyer l’offre. Cette opération déclenche le processus de publication dans lequel la solution est testée, validée, certifiée, puis mise en ligne sur la place de marché.  

## <a name="next-steps"></a>Étapes suivantes

Avant d’envisager ces étapes, vous devez respecter les [exigences techniques et commerciales](./cpp-prerequisites.md) de la publication d’une machine virtuelle sur la place de marché Microsoft Azure. 
