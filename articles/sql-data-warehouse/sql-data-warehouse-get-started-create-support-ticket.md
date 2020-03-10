---
title: Comment créer un ticket de support
description: Création d’un ticket de support dans Azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195673"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Création d’un ticket de support pour Azure Synapse Analytics
Si vous rencontrez des problèmes avec Azure Synapse Analytics, créez un ticket de support afin que l’équipe de support d’ingénierie puisse vous aider.

## <a name="create-a-support-ticket"></a>Création d’un ticket de support
1. Ouvrez le [portail Azure](https://portal.azure.com/).
1. Dans l’écran d’accueil, cliquez sur l’onglet **Aide + support**.
   
    ![Aide + Support](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Consultez votre [plan de support Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * La **gestion de la facturation, des abonnements et des quotas** sont pris en charge à tous les niveaux de support.
   * Les problèmes **couverts par la garantie de réparation et d’assistance** sont pris en charge dans les plans de support suivants : [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) ou [Premier](https://azure.microsoft.com/support/plans/premier/). Les problèmes couverts par la garantie de réparation et d'assistance sont des problèmes rencontrés par les clients lors de l'utilisation d'Azure et qui peuvent être raisonnablement imputés à Microsoft.
   * Les **services de tutorat** et d’**expertise pour développeur** sont disponibles aux niveaux de support [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) et [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Si vous avez un plan de support Premier, vous pouvez également signaler les problèmes liés à SQL Data Warehouse sur le [portail en ligne Microsoft Premier](https://premier.microsoft.com/). Consultez les [plans de support Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) pour en savoir plus sur les différents plans disponibles, y compris leur portée, leur temps de réponse, leur tarification, etc.  Pour accéder aux questions fréquemment posées sur le support Azure, consultez la page [FAQ du support Azure](https://azure.microsoft.com/support/faq/).
1. Dans la page **Aide + Support**, sélectionnez **Nouvelle demande de support**. Sélectionnez un type de problème dans le menu déroulant. Continuez ensuite à entrer les informations sous l’onglet **De base**. Entrez un **Résumé** de votre problème, sélectionnez un **Type de problème** dans le menu, puis sélectionnez Enregistrer.

    ![Aide + Support](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Par défaut, le **Quota DTU** de chaque serveur SQL (par exemple, myserver.database.windows.net) est de 45 000. Ce quota constitue simplement une limite de sécurité. Vous pouvez augmenter votre quota en créant un ticket de support et en sélectionnant *Quota* en tant que type de demande. Pour calculer vos besoins en matière de DTU, multipliez le nombre total de [DWU](sql-data-warehouse-overview-what-is.md) nécessaires par 7,5. Si, par exemple, vous souhaitez héberger deux bases de données DW6000 sur un serveur SQL unique, vous devez demander un quota de DTU de 90 000.  Vous pouvez consulter votre consommation de DTU actuelle dans le panneau SQL Server dans le portail. Les bases de données suspendues et réactivées sont prises en compte dans le quota de DTU. 
   > 

1. Il se peut que des solutions s’affichent pour vous aider à résoudre votre problème. Si les solutions présentées sont inappropriées, sélectionnez **Suivant : Détails**. Soumettez les détails de votre problème et vos coordonnées. Sélectionnez **Suivant : Vérifier + créer**
![Détails](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Révisez vos informations, puis sélectionnez **Créer** au bas du formulaire pour soumettre la demande de support.

## <a name="monitor-a-support-ticket"></a>Surveiller un ticket de support
Une fois votre demande de support envoyée, l’équipe de support Azure vous contactera. Pour vérifier l’état et les détails de votre demande, cliquez sur **Toutes les demandes de support** dans le tableau de bord.

![Vérification du statut](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Autres ressources
Vous pouvez également contacter la communauté SQL Data Warehouse sur [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) ou via le [forum MSDN Azure SQL Data Warehouse](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
