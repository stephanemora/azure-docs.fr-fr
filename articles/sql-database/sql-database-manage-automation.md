---
title: Gérer des bases de données avec Azure Automation
description: Découvrez comment le service Azure Automation peut être utilisé pour gérer les bases de données Azure SQL à grande échelle.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9d826a75f05cf2031565f89e21d7f3667ecc8f17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822805"
---
# <a name="managing-azure-sql-databases-using-azure-automation"></a>Gestion des bases de données Azure SQL à l’aide d’Azure Automation

Ce guide vous présente le service Azure Automation et la manière de l'utiliser pour gérer plus simplement vos bases de données Azure SQL.

## <a name="what-is-azure-automation"></a>Qu'est-ce qu'Azure Automation ?

[Azure Automation](https://azure.microsoft.com/services/automation/) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Azure Automation permet d'automatiser les tâches fastidieuses, manuelles, propices aux erreurs et répétitives, afin d'augmenter la fiabilité, l'efficacité et le retour sur investissement pour votre organisation.

Azure Automation fournit un moteur d’exécution de workflow très fiable et très disponible, qui s'adapte à vos besoins à mesure que votre entreprise se développe. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et dégagez du temps pour votre personnel informatique/DevOps afin de lui permettre de se concentrer sur des tâches générant une valeur ajoutée pour l'entreprise, en configurant Azure Automation pour exécuter automatiquement vos tâches de gestion de cloud.

## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Comment Azure Automation peut-il aider à gérer des bases de données Azure SQL ?

Le service Azure SQL Database peut être géré dans Azure Automation à l’aide des [applets de commande PowerShell pour Azure SQL Database](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql) disponibles dans les [outils Azure PowerShell](/powershell/azure/overview). Azure Automation dispose dès le départ de ces applets de commande PowerShell pour Azure SQL Database, de sorte que vous pouvez effectuer toutes vos tâches de gestion de bases de données SQL au sein du service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d’autres services Azure, afin d’automatiser des tâches complexes entre des services Azure et des systèmes tiers.

Azure Automation a également la possibilité de communiquer directement avec les serveurs SQL en exécutant des commandes SQL à l'aide de PowerShell.

La [galerie de runbooks Azure Automation](https://azure.microsoft.com/blog/20../../introducing-the-azure-automation-runbook-gallery/) contient de nombreux runbooks d’équipe produit et de la communauté pour commencer à automatiser la gestion des bases de données Azure SQL, ainsi que d’autres services Azure et de systèmes tiers. La galerie de Runbooks inclut :

- [Exécuter des requêtes SQL avec une base de données SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
- [Mise à l’échelle verticale (haut ou bas) d’une base de données Azure SQL selon un programme planifié](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
- [Tronquer une table SQL si la base de données est proche de la taille maximale](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
- [Indexer les tables dans une base de données Azure SQL si elles sont très fragmentées](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les bases d'Azure Automation et la manière de l'utiliser pour gérer les bases de données Azure SQL, suivez ces liens pour en savoir plus sur Azure Automation.

- [Vue d’ensemble de Microsoft Azure Automation](../automation/automation-intro.md)
- [Mon premier Runbook](../automation/automation-first-runbook-graphical.md)
- [Azure Automation : Votre agent SQL dans le cloud](https://azure.microsoft.com/blog/20../../azure-automation-your-sql-agent-in-the-cloud/) 