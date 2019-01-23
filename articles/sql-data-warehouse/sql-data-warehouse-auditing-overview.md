---
title: Audit dans Azure SQL Data Warehouse | Microsoft Docs
description: En savoir plus sur l’audit et sa configuration dans Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: d35ac7cac5c14a7bd57913046e8f4c09a22f177a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267434"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Audit dans Azure SQL Data Warehouse

En savoir plus sur l’audit et sa configuration dans Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Qu’est-ce que l’audit ?
La fonction d’audit de SQL Data Warehouse vous permet d’enregistrer les événements survenus dans votre base de données dans un journal d’audit au sein de votre compte Microsoft Azure Storage. L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données, et à découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

Les outils d’audit permettent et facilitent le respect des normes liées à la conformité, mais ne garantissent pas cette dernière. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Principes fondamentaux de l’audit
Éléments rendus possibles par l’audit de bases de données SQL Data Warehouse :

* **La rétention** d’une piste d’audit d’événements sélectionnés. Vous pouvez définir des catégories d’actions de base de données à auditer.
* **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
* **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Les journaux d’audit sont stockés dans votre compte de stockage Azure. Vous pouvez définir une période de rétention des journaux d'audit.


## <a id="subheading-4"></a>Définir une stratégie d’audit au niveau du serveur ou au niveau de la base de données

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut :

* Une stratégie de serveur  **s’applique aux bases de données existantes et à celles qui sont nouvellement créées** sur le serveur.

* Si *l’audit d’objets blob serveur est activé*, il *s’applique toujours à la base de données*. La base de données est auditée, quels que soient les paramètres d’audit de la base de données.

* L’activation de l’audit dans la base de données, en plus de son activation sur le serveur, ne remplace *pas* et ne modifie pas non plus les paramètres de l’audit d’objets blob serveur. Les deux audits coexistent. En d’autres termes, la base de données est auditée deux fois en parallèle (une fois par la stratégie du serveur et une autre fois par la stratégie de la base de données).

> [!NOTE]
> Nous vous recommandons d’activer **uniquement l’audit d’objets blob au niveau du serveur** et de laisser l’audit au niveau de la base de données désactivé pour toutes les bases de données.
> Vous devez éviter d’activer à la fois l’audit du serveur et l’audit de la base de données, sauf si :
> * Vous voulez utiliser un autre *compte de stockage* ou une autre *période de rétention* pour une base de données spécifique.
> * Vous souhaitez auditer des types ou des catégories d’événements pour une base de données qui sont différents de ceux qui sont audités pour les autres bases de données du serveur. Par exemple, il est possible que des insertions de table doivent être auditées uniquement pour une base de données spécifique.
> * Vous souhaitez utiliser la détection des menaces, qui est actuellement prise en charge uniquement avec l’audit au niveau de la base de données.

> [!IMPORTANT]
>L’activation de l’audit sur un entrepôt Azure SQL Data Warehouse ou sur un serveur avec un entrepôt Azure SQL Data Warehouse, **entraîne la reprise de l’entrepôt Data Warehouse**, même s’il avait précédemment été interrompu. **Veillez à suspendre l’entrepôt Data Warehouse à nouveau après l’activation de l’audit**.

## <a id="subheading-5"></a>Configurer l’audit au niveau du serveur pour toutes les bases de données

Une stratégie d’audit de serveur s’applique aux **bases de données existantes et à celles qui sont nouvellement créées** sur le serveur.

La section suivante décrit la configuration de l’audit à l’aide du portail Azure.

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez au **serveur SQL** que vous souhaitez auditer (important : assurez-vous d’afficher le serveur SQL, et non une base de données/un entrepôt de données spécifiques). Dans le menu **Sécurité**, sélectionnez **Audit et détection des menaces**.

    ![Volet de navigation][6]
4. Dans le panneau *Audit et détection des menaces*, sélectionnez **ACTIVÉ** sous **Audit**. Cette stratégie d’audit s’applique aux bases de données existantes et à celles qui sont nouvellement créées sur ce serveur.

    ![Volet de navigation][7]
5. Pour ouvrir le panneau **Stockage des journaux d’audit**, sélectionnez **Détails du stockage**. Sélectionnez ou créez le compte de stockage Azure où les journaux sont enregistrés, puis la période de rétention (les anciens journaux seront supprimés). Cliquez ensuite sur **OK**.

    ![Volet de navigation][8]

    > [!IMPORTANT]
    > Les journaux d’audit au niveau du serveur sont écrits dans des **Blobs d’ajout** dans un stockage Blob Azure avec votre abonnement Azure.
    >
    > * **Stockage Premium** n’est actuellement  **pas pris en charge** par l’ajout d’objets blob.
    > * Le **stockage dans un réseau virtuel** n’est actuellement **pas pris en charge**.

8. Cliquez sur **Enregistrer**.



## <a id="subheading-2"></a>Configurer l’audit au niveau de la base de données pour une base de données unique

Vous pouvez définir une stratégie d’audit pour une base de données spécifique ou en tant que stratégie de serveur par défaut.

Il est fortement recommandé d’utiliser l’audit au niveau du serveur et non au niveau de la base de données, comme décrit dans la section [Définir une stratégie d’audit au niveau du serveur ou au niveau de la base de données](#subheading-4)

Avant de configurer l'audit, assurez-vous que vous utilisez bien un [« Client de bas niveau »](sql-data-warehouse-auditing-downlevel-clients.md).


1. Lancez le [portail Azure](https://portal.azure.com).
2. Accédez à **Paramètres** pour l’instance SQL Data Warehouse que vous voulez auditer. Sélectionnez **Audit et détection des menaces**.

    ![][1]
3. Ensuite, activez la fonction d’audit en cliquant sur le bouton **ACTIVÉ** .

    ![][3]
4. Dans le panneau de configuration de l’audit, sélectionnez **DÉTAILS DU STOCKAGE** pour ouvrir le panneau Stockage des journaux d’audit. Sélectionnez le compte de stockage Azure pour les journaux ainsi que la période de rétention.
    >[!TIP]
    >Utilisez le même compte de stockage pour toutes les bases de données auditées afin de profiter au mieux des modèles de rapport préconfigurés.

    ![][4]

5. Cliquez sur le bouton **OK** pour enregistrer la configuration des détails du stockage.
6. Sous **JOURNALISATION PAR ÉVÈNEMENT**, cliquez sur **SUCCÈS** et sur **ÉCHEC**pour enregistrer tous les événements, ou choisissez des catégories d’événements individuelles.
7. Si vous configurez l’audit pour une base de données, vous pouvez être amené à modifier la chaîne de connexion de votre client pour garantir que l’audit des données est correctement capturé. Consultez la rubrique [Modifier le nom de domaine complet du serveur dans la chaîne de connexion](sql-data-warehouse-auditing-downlevel-clients.md), qui traite des connexions client de niveau inférieur.
8. Cliquez sur **OK**.

## <a id="subheading-3"></a>Analyse des journaux et des rapports d’audit

### <a name="server-level-policy-audit-logs"></a>Journaux d’audit avec stratégie au niveau du serveur
Les journaux d’audit au niveau du serveur sont écrits dans des **Blobs d’ajout** dans un stockage Blob Azure avec votre abonnement Azure. Ils sont enregistrés sous la forme d’une collection de fichiers d’objets blob dans un conteneur nommé **sqldbauditlogs**.

Pour plus d’informations sur la hiérarchie du dossier de stockage, sur les conventions de nommage et sur le format des journaux, consultez le [document de référence sur le format des journaux d’audit d’objets blob](https://go.microsoft.com/fwlink/?linkid=829599).

Plusieurs méthodes vous permettent d’afficher des journaux d’audit d’objets blob :

* Utilisez **Fusionner les fichiers d’audit** dans SQL Server Management Studio (à partir de SSMS 17) :
    1. Dans le menu SSMS, sélectionnez **Fichier** > **Ouvrir** > **Fusionner les fichiers d’audit**.

    2. La boîte de dialogue **Ajouter des fichiers d’audit** s’ouvre. Sélectionnez l’une des options **Ajouter** pour choisir de fusionner les fichiers d’audit à partir d’un disque local ou de les importer à partir du stockage Azure. Vous devrez fournir vos informations de stockage Azure et la clé de compte.

    3. Une fois que tous les fichiers à fusionner ont été ajoutés, cliquez sur **OK** pour terminer l’opération de fusion.

    4. Le fichier fusionné s’ouvre dans SSMS, où vous pouvez l’afficher et l’analyser, ainsi que l’exporter vers un fichier XEL ou CSV, ou une table.

* Utilisez l’[application de synchronisation](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) que nous avons créée. Elle s’exécute dans Azure et s’appuie sur les API publiques Log Analytics pour effectuer un push des journaux d’audit SQL vers Log Analytics. L’application de synchronisation effectue un push des journaux d’audit SQL vers Log Analytics pour les utiliser dans le tableau de bord Log Analytics.

* Utilisez Power BI. Vous pouvez afficher et analyser les données du journal d’audit dans Power BI. Explorez [Power BI et accédez à un modèle téléchargeable](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Téléchargez les fichiers journaux à partir de votre conteneur Azure Storage Blob via le portail ou avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).
    * Une fois que vous avez téléchargé localement un fichier journal, vous pouvez double-cliquer sur le fichier pour ouvrir, afficher et analyser les journaux dans SSMS.
    * Vous pouvez également télécharger plusieurs fichiers simultanément avec l’Explorateur de stockage Azure. Cliquez avec le bouton droit sur un sous-dossier, puis sélectionnez **Enregistrer en tant que** pour enregistrer dans un dossier local.

* Autres méthodes :
   * Après avoir téléchargé plusieurs fichiers (ou un sous-dossier contenant des fichiers journaux), vous pouvez les fusionner localement en suivant les instructions relatives à l’option Fusionner les fichiers d’audit dans SSMS décrites précédemment.

   * Affichez des journaux d’audit d’objets blob par programmation :

     * Utilisez la bibliothèque C# [Lecteur des événements étendus](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/).
     * [Interrogez des fichiers d’événements étendus](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) avec PowerShell.



<br>
### <a name="database-level-policy-audit-logs"></a>Journaux d’audit avec stratégie au niveau de la base de données
Les journaux d’audit au niveau de la base de données sont agrégés dans une collection de tables de stockage avec un préfixe **SQLDBAuditLogs** au sein du compte de stockage Azure que vous avez choisi lors de la configuration. Vous pouvez afficher les fichiers journaux à l'aide d'un outil tel que l'[Explorateur de stockage Azure](http://azurestorageexplorer.codeplex.com).

Un modèle de rapport de tableau de bord préconfiguré est disponible sous forme de [feuille de calcul Excel téléchargeable](https://go.microsoft.com/fwlink/?LinkId=403540) afin de vous aider à analyser rapidement les données de journal. Pour utiliser le modèle sur vos journaux d’audit, il vous faut Excel 2013 ou une version ultérieure et Power Query, téléchargeable [ici](https://www.microsoft.com/download/details.aspx?id=39379).

Le modèle contient des données d'exemple fictives. Vous pouvez configurer Power Query de façon à ce qu'il importe votre journal d'audit directement à partir de votre compte de stockage Azure.

## <a id="subheading-4"></a>Régénération des clés de stockage
Dans un environnement de production, vous allez probablement actualiser périodiquement vos clés de stockage. Au moment d’actualiser vos clés, vous devez réenregistrer la stratégie. Pour ce faire, procédez comme suit :

1. Dans le panneau de configuration de l’audit, décrit dans la section de configuration de l’audit précédente, faites passer la **clé d’accès du stockage** de *Principale* à *Secondaire*, puis choisissez **ENREGISTRER**.

   ![][4]
2. Accédez au panneau de configuration du stockage, puis **régénérez** la *clé d’accès primaire*.
3. Revenez au panneau de configuration de l’audit,
4. faites passer la **clé d’accès du stockage** de *Secondaire* à *Principale*, puis cliquez sur **ENREGISTRER**.
4. Retournez dans l'interface utilisateur de stockage, puis **régénérez** la *clé d'accès secondaire* (en vue du prochain cycle d'actualisation des clés).

## <a id="subheading-5"></a>Automation (PowerShell / API REST)
Vous pouvez aussi configurer l’audit dans Azure SQL Data Warehouse en utilisant les outils d’automation suivants :

* **Applets de commande PowerShell**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Prise en charge des clients de niveau inférieur pour l’audit et le masquage dynamique des données (Dynamic Data Masking)
L’audit fonctionne avec les clients SQL qui prennent en charge la redirection TDS.

Tout client qui implémente TDS 7.4 doit également prendre en charge la redirection. Cependant, cette règle comporte deux exceptions : JDBC 4.0, qui ne prend pas complètement en charge la fonctionnalité de redirection et Tedious pour Node.JS, où la redirection n’a pas été implémentée.

Pour les « clients de niveau inférieur » qui prennent en charge la version 7.3 de TDS et les versions antérieures, modifiez le nom de domaine complet du serveur dans la chaîne de connexion, comme suit :

- Nom de domaine complet du serveur d’origine dans la chaîne de connexion : <*nom du serveur*>.database.windows.net
- Nom de domaine complet du serveur modifié dans la chaîne de connexion : <*nom du serveur*>.database.**secure**.windows.net

Voici une liste non exhaustive de « clients de niveau inférieur » :

* .NET 4.0 et versions antérieures
* ODBC 10.0 et versions antérieures
* JDBC (bien que JDBC prenne en charge la version 7.4 de TDS, la fonctionnalité de redirection TDS n’est pas entièrement prise en charge)
* Tedious (pour Node.JS)

**Remarque :** la modification précédente du nom de domaine complet du serveur peut également être utile pour appliquer une stratégie d'audit au niveau de SQL Server sans passer par la configuration de chacune des bases de données (atténuation temporaire).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
