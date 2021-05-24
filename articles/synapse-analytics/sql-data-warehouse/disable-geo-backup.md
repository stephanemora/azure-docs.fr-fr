---
title: Désactiver les géosauvegardes
description: Guide pratique pour la désactivation des géosauvegardes pour un pool SQL dédié (anciennement SQL DW) dans Azure Synapse Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2cebee3ad9b515c6f40529fe5d25da687fd53687
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786282"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Désactiver les géosauvegardes pour un [pool SQL dédié (anciennement SQL DW)](sql-data-warehouse-overview-what-is.md) dans Azure Synapse Analytics

Dans cet article, vous allez apprendre à désactiver les géosauvegardes pour votre pool SQL dédié (anciennement SQL DW) à l’aide du portail Azure.

## <a name="disable-geo-backups-through-azure-portal"></a>Désactiver les géosauvegardes via Portail Azure

Suivez ces étapes pour désactiver les géosauvegardes pour votre pool SQL dédié (anciennement SQL DW) :

> [!NOTE]
> Si vous désactivez les géosauvegardes, vous ne pourrez plus récupérer votre pool SQL dédié (anciennement SQL DW) dans une autre région Azure. 
> 

1. Connectez-vous à votre compte [Portail Azure](https://portal.azure.com/).
1. Sélectionnez la ressource de pool SQL dédié (anciennement SQL DW) pour laquelle vous souhaitez désactiver les géosauvegardes. 
1. Sous **Paramètres** dans le volet de navigation de gauche, sélectionnez **Stratégie de géosauvegarde**.

   ![Désactiver la géosauvegarde](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Pour désactiver les géo-sauvegardes, sélectionnez **Désactivé**. 

   ![Géosauvegarde désactivée](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Sélectionnez *Enregistrer* pour vous assurer que vos paramètres sont sauvegardés. 

   ![Enregistrer les paramètres de géosauvegarde](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Étapes suivantes

- [Restaurer un pool SQL dédié (anciennement SQL DW) existant](sql-data-warehouse-restore-active-paused-dw.md)
- [Restaurer un pool SQL dédié (anciennement SQL DW) supprimé](sql-data-warehouse-restore-deleted-dw.md)
