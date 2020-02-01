---
title: Comment créer un ticket de support
description: Création d'un ticket de support dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717836"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Création d’un ticket de support pour SQL Data Warehouse
Si vous rencontrez des problèmes avec votre SQL Data Warehouse, créez un ticket de support pour que l’équipe de support d’ingénierie puisse vous aider.

## <a name="create-a-support-ticket"></a>Création d’un ticket de support
1. Ouvrez le [portail Azure](https://portal.azure.com/).
2. Dans l’écran d’accueil, cliquez sur l’onglet **Aide + support**.
   
    ![Aide + Support](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Dans le panneau Aide + support, cliquez sur **Nouvelle demande de support** et renseignez le panneau **De base**.

   Sélectionnez votre [plan de support Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * La **gestion de la facturation, des abonnements et des quotas** sont pris en charge à tous les niveaux de support.
   * Les problèmes **couverts par la garantie de réparation et d’assistance** sont pris en charge dans les plans de support suivants : [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) ou [Premier](https://azure.microsoft.com/support/plans/premier/). Les problèmes couverts par la garantie de réparation et d'assistance sont des problèmes rencontrés par les clients lors de l'utilisation d'Azure et qui peuvent être raisonnablement imputés à Microsoft.
   * Les **services de tutorat** et d’**expertise pour développeur** sont disponibles aux niveaux de support [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) et [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Si vous avez un plan de support Premier, vous pouvez également signaler les problèmes liés à SQL Data Warehouse sur le [portail en ligne Microsoft Premier](https://premier.microsoft.com/). Consultez les [plans de support Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) pour en savoir plus sur les différents plans disponibles, y compris leur portée, leur temps de réponse, leur tarification, etc.  Pour accéder aux questions fréquemment posées sur le support Azure, consultez la page [FAQ du support Azure](https://azure.microsoft.com/support/faq/).  
        
     ![Panneau De base](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Panneau 1 De base](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Renseignez le panneau **Problème**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Par défaut, le **Quota DTU** de chaque serveur SQL (par exemple, myserver.database.windows.net) est de 45 000. Ce quota constitue simplement une limite de sécurité. Vous pouvez augmenter votre quota en créant un ticket de support et en sélectionnant *Quota* en tant que type de demande. Pour calculer vos besoins en matière de DTU, multipliez le nombre total de [DWU](sql-data-warehouse-overview-what-is.md) nécessaires par 7,5. Si, par exemple, vous souhaitez héberger deux bases de données DW6000 sur un serveur SQL unique, vous devez demander un quota de DTU de 90 000.  Vous pouvez consulter votre consommation de DTU actuelle dans le panneau SQL Server dans le portail. Les bases de données suspendues et réactivées sont prises en compte dans le quota de DTU. 
   > 
   > 
   
5. Renseignez vos **coordonnées**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Cliquez sur **Créer** pour envoyer la demande de support.

## <a name="monitor-a-support-ticket"></a>Surveiller un ticket de support
Une fois votre demande de support envoyée, l’équipe de support Azure vous contactera. Pour vérifier l’état et les détails de votre demande, cliquez sur **Toutes les demandes de support** dans le tableau de bord.

![Vérification du statut](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Autres ressources
Vous pouvez également contacter la communauté SQL Data Warehouse sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) ou via le [forum MSDN Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
