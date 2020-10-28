---
title: Se connecter à Synapse SQL avec Azure Data Studio (préversion)
description: Utilisez Azure Data Studio (préversion) pour vous connecter à Synapse SQL et l’interroger dans Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: c3c1b61cb4f799b79ca00fc92ffadc2374a61c03
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132463"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Se connecter à Synapse SQL avec Azure Data Studio (préversion)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Vous pouvez utiliser [Azure Data Studio (préversion)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) pour vous connecter à Synapse SQL et l’interroger dans Azure Synapse Analytics. 

## <a name="connect"></a>Se connecter

Pour vous connecter à Synapse SQL, ouvrez Azure Data Studio et sélectionnez **Nouvelle connexion** .

![Ouvrir Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Choisissez **Microsoft SQL Server** comme **Type de connexion** .

La connexion nécessite les paramètres suivants :

* **Serveur :** Serveur sous la forme `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
* **Base de données :** Nom de la base de données

> [!NOTE]
> Si vous souhaitez utiliser **SQL à la demande (préversion)** l’URL doit se présenter comme suit :
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Si vous souhaitez utiliser **Pool SQL** l’URL doit se présenter comme suit :
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Choisissez **Authentification Windows** , **Azure Active Directory** ou **Connexion SQL** comme **Type d’authentification** .

Pour utiliser **Connexion SQL** en tant que type d’authentification, ajoutez les paramètres de nom d’utilisateur/mot de passe :

* **Utilisateur :** Utilisateur du serveur sous la forme `<User>`
* **Mot de passe :** Mot de passe associé à l’utilisateur

Pour utiliser Azure Active Directory, vous devez choisir le type d’authentification voulu.

![Authentification AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

La capture d’écran suivante montre les **Détails de la connexion** pour l’ **Authentification Windows**  :

![Authentification Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

La capture d’écran suivante montre les **Détails de la connexion** lors de l’utilisation d’une **Connexion SQL**  :

![Connexion SQL](./media/get-started-azure-data-studio/2-database-details.png)

Une fois la connexion établie, vous devez afficher un tableau de bord comme celui-ci : ![Tableau de bord](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Requête

Une fois connecté, vous pouvez interroger Synapse SQL à l’aide des instructions [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) prises en charge sur l’instance. Sélectionnez **Nouvelle requête** à partir de l’affichage du tableau de bord pour commencer.

![Nouvelle requête](./media/get-started-azure-data-studio/5-new-query.png)

Par exemple, vous pouvez utiliser l’instruction Transact-SQL suivante pour [interroger des fichiers Parquet](query-parquet-files.md) à l’aide de SQL à la demande :

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Étapes suivantes 
Explorer d’autres façons de se connecter à Synapse SQL : 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)

Pour plus d’informations, consultez [Connexion et interrogation de données avec Azure Data Studio à l’aide d’un pool SQL dédié dans Azure Synapse Analytics](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-dw). 
