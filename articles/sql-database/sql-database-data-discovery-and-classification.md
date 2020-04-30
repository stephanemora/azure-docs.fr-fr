---
title: Découverte et classification des données
description: Découverte et classification des données pour Azure SQL Database et Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/21/2020
tags: azure-synapse
ms.openlocfilehash: f05b4d4fec99aaa2fb79da46e2167d883d1f15ec
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767002"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Découverte et classification des données pour Azure SQL Database et Azure Synapse Analytics

La découverte et classification de données est intégrée à Azure SQL Database. Elle offre des fonctionnalités avancées pour la découverte, la classification, l’étiquetage et le signalement des données sensibles dans vos bases de données.

Vos données les plus sensibles peuvent inclure des informations commerciales, financières, médicales ou personnelles. La découverte et la classification de ces données peuvent jouer un rôle pivot dans l’approche de protection des informations de votre organisation. Elles peuvent servir d’infrastructure pour :

- Contribuer à respecter les normes en matière de confidentialité des données et d’exigences pour la conformité réglementaire.
- Divers scénarios de sécurité, comme la surveillance (audit) et la génération d’alertes en cas d’accès anormaux aux données sensibles.
- Contrôler l’accès et renforcer la sécurité des bases de données contenant des données sensibles.

Découverte et classification des données fait partie de l’offre [Advanced Data Security](sql-database-advanced-data-security.md) qui est un package unifié de fonctionnalités de sécurité SQL avancées. Vous pouvez accéder à et gérer la découverte et la classification de données via la section centrale **SQL Advanced Data Security** de le portail Azure.

> [!NOTE]
> Cet article s’applique à Azure SQL Database et à Azure Synapse Analytics. Par souci de simplicité, nous utilisons *SQL Database* ici pour faire référence à la fois à SQL Database et Azure Synapse. Pour plus d’informations sur SQL Server (local), consultez [Découverte et classification de données SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Présentation de Découverte et classification des données

La découverte et classification de données introduit un ensemble de services avancés et de nouvelles fonctionnalités pour SQL Database. Elle forme un nouveau paradigme de protection des informations pour SQL Database, destiné à protéger les données, et pas seulement la base de données. Le paradigme comprend les éléments suivants :

- **Découverte et recommandations :** Le moteur de classification analyse votre base de données et identifie les colonnes contenant des données potentiellement sensibles. Il vous offre ensuite un moyen simple d’examiner et d’appliquer la classification recommandée via le portail Azure.

- **Étiquetage :** Vous pouvez appliquer des étiquettes de classification de sensibilité de manière permanente aux colonnes à l’aide de nouveaux attributs de métadonnées qui ont été ajoutés au moteur de base de données SQL. Vous pouvez ensuite utiliser ces métadonnées pour des scénarios avancés d’audit et de protection basés sur la sensibilité.

- **Sensibilité du jeu de résultats de requête :** La sensibilité du jeu de résultats de requête est calculée en temps réel à des fins d’audit.

- **Visibilité :** Vous pouvez afficher l’état de classification de la base de données dans un tableau de bord détaillé dans le portail Azure. En outre, vous pouvez télécharger un rapport au format Excel pour l’utiliser à des fins de conformité et d’audit, et d’autres besoins.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Découvrir, classer et étiqueter des colonnes contenant des données sensibles

Cette section décrit les étapes pour :

- Découvrir, classer et étiqueter les colonnes qui contiennent des données sensibles dans votre base de données.
- Afficher l’état actuel de la classification de votre base de données et exporter des rapports.

La classification comprend deux attributs de métadonnées :

- **Étiquettes** : Les principaux attributs de classification utilisés pour définir le niveau de sensibilité des données stockées dans la colonne.  
- **Types d'informations** : Attributs qui fournissent des informations plus granulaires sur le type des données stockées dans la colonne.

### <a name="define-and-customize-your-classification-taxonomy"></a>Définir et personnaliser votre taxonomie de classification

La fonctionnalité Découverte et classification des données comprend un ensemble intégré d’étiquettes de sensibilité, ainsi qu’un ensemble intégré de types d’informations et de logiques de découverte. Vous pouvez maintenant personnaliser cette taxonomie, et de définir un jeu et un classement de constructions de classification spécialement pour votre environnement.

Vous définissez et personnalisez votre taxonomie de classification dans un emplacement central pour l’ensemble de votre organisation Azure. Cet emplacement se trouve dans [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), dans la section relative à votre stratégie de sécurité. Seules les personnes disposant de droits d’administration pour le groupe d’administration racine de l’organisation peuvent effectuer cette tâche.

Dans le cadre de la gestion des stratégies Information Protection SQL, vous pouvez définir des étiquettes personnalisées, les classer et les associer à un ensemble de types d’informations. Vous pouvez également ajouter vos propres types d’informations personnalisés et les configurer avec des modèles de chaîne. Les modèles sont ajoutés à la logique de détection pour identifier ce type de données dans vos bases de données.

Pour plus d’informations sur la personnalisation et la gestion de votre stratégie, consultez le [Guide pratique sur les stratégies Information Protection SQL](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Une fois la stratégie au niveau de l’organisation définie, vous pouvez continuer à classer les bases de données individuelles à l’aide de votre stratégie personnalisée.

### <a name="classify-your-sql-database"></a>Classer votre base de données SQL

1. Accédez au [portail Azure](https://portal.azure.com).

2. Accédez à **Advanced Data Security** sous l’en-tête **Sécurité** du volet Azure SQL Database. Sélectionnez **Advanced Data Security**, puis sélectionnez la carte **Découverte et classification des données**.

   ![Volet Advanced Data Security dans le portail Azure](./media/sql-data-discovery-and-classification/data_classification.png)

3. Sur la page **Découverte et classification des données**, l’onglet **Vue d’ensemble** contient un résumé de l’état actuel de la classification de la base de données. Le résumé comprend une liste détaillée de toutes les colonnes classifiées, que vous pouvez également filtrer pour afficher uniquement des parties de schéma, des types d’informations et des étiquettes spécifiques. Si vous n’avez pas encore classifié de colonne, [passez à l’étape 5](#step-5).

   ![Résumé de l’état actuel de la classification](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Pour télécharger un rapport au format Excel, sélectionnez **Exporter** dans le menu supérieur du volet.

5. <a id="step-5"></a>Pour commencer à classer vos données, sélectionnez l’onglet **Classification** de la page **Découverte et classification des données**.

    Le moteur de classification analyse votre base de données à la recherche de colonnes contenant des données potentiellement sensibles, et il fournit une liste de classifications de colonnes recommandées.

6. Afficher et appliquer les recommandations de classification :

   - Pour afficher la liste des classifications de colonnes recommandées, sélectionnez le panneau de recommandations en bas du volet.

   - Pour accepter une recommandation associée à une colonne spécifique, sélectionnez la case dans la colonne de gauche de la ligne concernée. Pour marquer toutes les recommandations comme étant acceptées, activez la case à cocher la plus à gauche dans l’en-tête de table de recommandations.

       ![Examiner et sélectionner dans la liste de recommandations de classification](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Pour appliquer les recommandations sélectionnées, sélectionnez **Accepter les recommandations sélectionnées**.

7. Vous pouvez aussi, en guise d’alternative, classifier manuellement des colonnes ou, en plus de la classification basée sur les recommandations :

   1. Sélectionnez **Ajouter une classification** dans le menu en haut du volet.

   1. Dans la fenêtre contextuelle qui apparaît, sélectionnez le schéma, la table et la colonne que vous souhaitez classifier, ainsi que l’étiquette de sensibilité et le type d’informations.

   1. Sélectionnez **Ajouter une classification** en bas de la fenêtre contextuelle.

      ![Sélectionner une colonne à classer](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Pour terminer votre classification et étiqueter de manière permanente les colonnes de base de données avec les nouvelles métadonnées de classification, sélectionnez **Enregistrer** dans le menu en haut de la fenêtre.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Audit de l’accès aux données sensibles

Un aspect important du paradigme de protection des informations est la possibilité de surveiller l’accès aux données sensibles. [L’audit Azure SQL Database](sql-database-auditing.md) a été amélioré pour inclure un nouveau champ nommé `data_sensitivity_information` dans le journal d’audit. Ce champ enregistre les classifications (étiquettes) de sensibilité des données retournées par une requête. Voici un exemple :

![Journal d’audit](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Autorisations

Ces rôles intégrés peuvent lire la classification des données d’une base de données Azure SQL :

- Propriétaire
- Lecteur
- Contributeur
- Gestionnaire de sécurité SQL
- Administrateur de l'accès utilisateur

Ces rôles intégrés peuvent modifier la classification des données d’une base de données Azure SQL :

- Propriétaire
- Contributeur
- Gestionnaire de sécurité SQL

Pour plus d’informations sur les autorisations basées sur les rôles, consultez [RBAC pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gérer les classifications

Vous pouvez utiliser T-SQL, une API REST ou PowerShell pour gérer les classifications.

### <a name="use-t-sql"></a>Utiliser T-SQL

Vous pouvez utiliser T-SQL pour ajouter/supprimer des classifications de colonne, ainsi que pour récupérer toutes les classifications pour la base de données entière.

> [!NOTE]
> Quand vous utilisez T-SQL pour gérer les étiquettes, aucune validation n’est effectuée pour vérifier que les étiquettes que vous ajoutez à une colonne existent dans la stratégie de protection des informations de l’organisation (ensemble des étiquettes qui apparaissent dans les recommandations du portail). C’est donc à vous de valider.

Pour plus d’informations sur l’utilisation de T-SQL pour les classifications, consultez les références suivantes :

- Pour ajouter/mettre à jour la classification d’une ou plusieurs colonnes : [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Pour supprimer la classification d’une ou plusieurs colonnes : [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Pour voir toutes les classifications sur la base de données : [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Utiliser l’API REST

Vous pouvez utiliser l’API REST pour gérer par programme les classifications et les recommandations. L’API REST publiée prend en charge les opérations suivantes :

- [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) : Crée ou met à jour l’étiquette de sensibilité de la colonne spécifiée.
- [Supprimer](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) : Supprime l’étiquette de sensibilité de la colonne spécifiée.
- [Désactiver les recommandations](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) : Désactive les recommandations de sensibilité sur la colonne spécifiée.
- [Activer une recommandation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) : Active les recommandations de sensibilité sur la colonne spécifiée. (Les recommandations sont activées par défaut sur toutes les colonnes.)
- [Obtenir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) : Obtient l’étiquette de sensibilité de la colonne spécifiée.
- [Afficher la liste actuelle par base de données](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) : Obtient les étiquettes de sensibilité actuelles de la base de données spécifiée.
- [Liste recommandée par base de données](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) : Obtient les étiquettes de sensibilité recommandées pour la base de données spécifiée.

### <a name="use-powershell-cmdlets"></a>Utiliser des cmdlets PowerShell
Vous pouvez utiliser PowerShell pour gérer les classifications et les recommandations pour Azure SQL Database et les instances gérées.

#### <a name="powershell-cmdlets-for-sql-database"></a>Cmdlets PowerShell pour SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>Applets de commande PowerShell pour Managed Instance

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Enable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur [Advanced Data Security](sql-database-advanced-data-security.md).
- Vous pouvez configurer [l’audit Azure SQL Database](sql-database-auditing.md) pour effectuer la surveillance et l’audit de l’accès à vos données sensibles classifiées.
- Pour une présentation incluant la découverte et la classification des données, consultez [Découverte, classification, étiquetage et protection des données SQL | Données exposées](https://www.youtube.com/watch?v=itVi9bkJUNc).
