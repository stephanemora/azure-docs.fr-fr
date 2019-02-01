---
title: Création d’un ticket de support pour Azure SQL Data Warehouse | Microsoft Docs
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
ms.openlocfilehash: 4761a50905f28c215a47d579b931ab5db9d7e424
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468068"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Création d’un ticket de support pour SQL Data Warehouse
Si vous rencontrez des problèmes avec SQL Data Warehouse, créez un ticket de support pour que l’équipe de support ingénierie puisse vous aider.

## <a name="create-a-support-ticket"></a>Création d’un ticket de support
1. Ouvrez le [portail Azure][Azure portal].
2. Dans l’écran d’accueil, cliquez sur l’onglet **Aide + support**.
   
    ![Aide + Support](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. Dans le panneau Aide + support, cliquez sur **Nouvelle demande de support** et renseignez le panneau **De base**.

   Sélectionnez votre [plan de support Azure][Azure support plan].
   
   * La **gestion de la facturation, des abonnements et des quotas** sont pris en charge à tous les niveaux de support.
   * Les problèmes **couverts par la garantie de réparation et d’assistance** sont pris en charge dans les plans de support suivants : [Developer][Developer], [Standard][Standard], [Professional Direct][Professional Direct] ou [Premier][Premier]. Les problèmes couverts par la garantie de réparation et d'assistance sont des problèmes rencontrés par les clients lors de l'utilisation d'Azure et qui peuvent être raisonnablement imputés à Microsoft.
   * Les **services de tutorat** et d’**expertise pour développeur** sont disponibles aux niveaux de support [Professional Direct][Professional Direct] et [Premier][Premier]. 
     
     Si vous avez un plan de support Premier, vous pouvez également signaler les problèmes liés à SQL Data Warehouse sur le [portail en ligne Microsoft Premier][Microsoft Premier online portal].  Consultez les [plans de support Azure][Azure support plan] pour en savoir plus sur les différents plans disponibles, y compris leur portée, leur temps de réponse, leur tarification, etc.  Pour accéder aux questions fréquemment posées sur le support Azure, consultez la page [FAQ du support Azure][Azure support FAQs].  
        
    ![Panneau De base](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
    ![Panneau 1 De base](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Renseignez le panneau **Problème**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Par défaut, le **Quota DTU** de chaque serveur SQL (par exemple, myserver.database.windows.net) est de 45 000. Ce quota constitue simplement une limite de sécurité. Vous pouvez augmenter votre quota en créant un ticket de support et en sélectionnant *Quota* en tant que type de demande. Pour calculer vos besoins en matière de DTU, multipliez le nombre total de [DWU][DWU] nécessaires par 7,5. Si, par exemple, vous souhaitez héberger deux bases de données DW6000 sur un serveur SQL unique, vous devez demander un quota de DTU de 90 000.  Vous pouvez consulter votre consommation de DTU actuelle dans le panneau SQL Server dans le portail. Les bases de données suspendues et réactivées sont prises en compte dans le quota de DTU. 
   > 
   > 
   
5. Renseignez vos **coordonnées**.
![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Cliquez sur **Créer** pour envoyer la demande de support.

## <a name="monitor-a-support-ticket"></a>Surveiller un ticket de support
Une fois que vous avez envoyé la demande de support, vous serez contacté par l'équipe de support Azure. Pour vérifier l’état et les détails de votre demande, cliquez sur **Toutes les demandes de support** dans le tableau de bord.

![Vérification du statut](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Autres ressources
En outre, vous pouvez contacter la communauté SQL Data Warehouse sur [Stack Overflow][Stack Overflow] ou le [forum MSDN Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

