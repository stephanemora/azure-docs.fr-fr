---
title: Restaurer un Azure SQL Data Warehouse - API REST | Microsoft Docs
description: Restaurez un Azure SQL Data Warehouse à l’aide d’API REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: daf013472e5fa533912920e4c14a552905b5d333
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935685"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Restaurer un Azure SQL Data Warehouse avec des API REST
Restaurez un Azure SQL Data Warehouse à l’aide d’API REST.

## <a name="before-you-begin"></a>Avant de commencer
**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL logique (par exemple, myserver.database.windows.net) qui dispose d’un [quota DTU](../sql-database/sql-database-what-is-a-dtu.md) par défaut.  Avant de pouvoir restaurer un SQL Data Warehouse, vérifiez que le quota DTU restant sur le serveur SQL est suffisant pour la base de données en cours de restauration. Pour demander plus de DTU, vous pouvez [Créer un ticket de support](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Restaurer un entrepôt de données actif ou en pause
Pour restaurer un entrepôt de données :

1. Suivez la procédure d’obtention de la liste des points de restauration de la base de données.
2. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données](https://msdn.microsoft.com/library/azure/dn509571.aspx) .
3. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Une fois la restauration terminée, vous pouvez configurer votre entrepôt de données récupéré en suivant [Configurer votre base de données après récupération](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Restaurer un entrepôt de données supprimé
Pour restaurer un entrepôt de données supprimé :

1. Répertoriez tous vos entrepôts de données supprimés pouvant être restaurés à l’aide de l’opération [Répertorier des bases de données déposées pouvant être restaurées](https://msdn.microsoft.com/library/azure/dn509562.aspx).
2. Obtenez des informations détaillées sur l’entrepôt de données que vous voulez restaurer en suivant l’opération [Obtenir une base de données déposée pouvant être restaurée][Obtenir une base de données déposée pouvant être restaurée].
3. Lancez la restauration en suivant la procédure [Création d’une demande de restauration de base de données](https://msdn.microsoft.com/library/azure/dn509571.aspx) .
4. Surveillez l’état de la restauration en suivant la procédure [Statut d’opération de base de données](https://msdn.microsoft.com/library/azure/dn720371.aspx) .

> [!NOTE]
> Pour configurer votre entrepôt de données une fois la restauration terminée, consultez [Configurer votre base de données après récupération](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions Azure SQL Database, consultez [Vue d’ensemble de la continuité des activités Azure SQL Database](../sql-database/sql-database-business-continuity.md).
