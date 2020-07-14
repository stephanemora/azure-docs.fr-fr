---
title: 'Portail Azure : Masquage dynamique des données'
description: Guide de prise en main du masquage dynamique des données d'Azure SQL Database sur le portail Azure
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 12a187b29737ba9388d9b33ac8bcfedf1339c059
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253781"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Prise en main du masquage des données dynamiques de base de données SQL dans le portail Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article vous montre comment implémenter [le masquage des données dynamiques](dynamic-data-masking-overview.md) avec le portail Azure. Vous pouvez également implémenter le masquage de données dynamique avec les [applets de commande Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) ou [l’API REST](https://docs.microsoft.com/rest/api/sql/).

> [!NOTE]
> Cette fonctionnalité ne peut pas être définie à l'aide du portail pour Azure Synapse (utilisez PowerShell ou l'API REST) ou SQL Managed Instance. Pour plus d’informations, consultez [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configuration du masquage des données dynamiques pour votre base de données à l’aide du portail Azure

1. Lancez le portail Azure sur [https://portal.azure.com](https://portal.azure.com).
2. Accédez à la page des paramètres de la base de données comprenant les données sensibles que vous souhaitez masquer.
3. Cliquez sur le panneau **Dynamic Data Masking** sous la section **Sécurité** de votre base de données SQL.

   ![Volet de navigation](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. Dans la page de configuration **Masquage des données dynamiques**, certaines colonnes de base de données ont été indiquées par le moteur de recommandations pour le masquage. Pour accepter les recommandations, cliquez simplement sur **Ajouter un masque** pour une ou plusieurs colonnes et un masque est créé en fonction du type par défaut pour cette colonne. Vous pouvez modifier la fonction de masquage en cliquant sur la règle de masquage et en modifiant le format du champ de masquage pour un format différent de votre choix. N'oubliez pas de cliquer sur **Enregistrer** pour enregistrer vos paramètres.

    ![Volet de navigation](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Pour ajouter un masque pour une colonne de votre base de données, au sommet de la page de configuration **Dynamic Data Masking**, cliquez sur **Ajouter un masque** pour ouvrir la page de configuration **Ajouter une règle de masquage**.

    ![Volet de navigation](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Sélectionnez le **Schéma**, la **Table** et la **Colonne** pour définir les champs désignés qui seront masqués.
7. **Sélectionnez le mode de masquage** dans la liste des catégories de masquage des données sensibles.

    ![Volet de navigation](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Cliquez sur **Ajouter** sur la page des règles de masquage pour mettre à jour l'ensemble des règles de la stratégie de masquage dynamique des données.
9. Saisissez les utilisateurs SQL ou les identités Azure Active Directory (Azure AD) qui doivent être exclues du masquage et avoir accès aux données sensibles non masquées. La liste d'utilisateurs doit être délimitée par des points-virgules. Les utilisateurs disposant de privilèges administrateur ont toujours accès aux données d'origine non masquées.

    ![Volet de navigation](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Pour que la couche d'application puisse afficher des données sensibles aux utilisateurs d'application privilégiés, ajoutez l'utilisateur SQL ou l'identité Azure AD qu'utilise l'application pour interroger la base de données. Il est vivement recommandé de limiter le nombre d'utilisateurs privilégiés dans cette liste afin de limiter l'exposition des données sensibles.

10. Cliquez sur **Enregistrer** dans la page de configuration du masquage des données afin d'enregistrer la stratégie de masquage, nouvelle ou mise à jour.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble du masquage des données dynamiques, consultez [Masquage des données dynamiques](dynamic-data-masking-overview.md).
- Vous pouvez également implémenter le masquage de données dynamique avec les [applets de commande Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql/) ou [l’API REST](https://docs.microsoft.com/rest/api/sql/).
