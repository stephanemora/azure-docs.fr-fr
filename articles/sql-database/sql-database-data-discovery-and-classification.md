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
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: b3a08eb351d29fd71889807c9a21d03b564117a7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673746"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Découverte et classification des données pour Azure SQL Database et Azure Synapse Analytics

Découverte et classification des données offre des fonctionnalités avancées intégrées à Azure SQL Database pour la **découverte**, la **classification**, l’**étiquetage**  &  la **génération de rapports** des données sensibles dans vos bases de données.

La découverte et la classification de vos données les plus sensibles (professionnelles, financières, médicales, informations d’identification personnelle, etc.) peuvent jouer un rôle essentiel dans la protection des informations de l’organisation. Elles peuvent servir d’infrastructure pour :

- Aider à répondre aux normes de confidentialité des données et aux exigences de conformité aux normes.
- Divers scénarios de sécurité, comme la surveillance (audit) et la génération d’alertes en cas d’accès anormaux aux données sensibles.
- Contrôler l’accès et renforcer la sécurité des bases de données contenant des données sensibles.

Découverte et classification des données fait partie de l’offre [Advanced Data Security](sql-database-advanced-data-security.md) (ADS) qui est un package unifié de fonctionnalités de sécurité SQL avancées. L'accès au composant Découverte et classification des données ainsi que sa gestion se font via le portail SQL ADS central.

> [!NOTE]
> Ce document a trait à Azure SQL Database et Azure Synapse. Par souci de simplicité, le nom « SQL Database » est utilisé pour faire référence à SQL Database et à Azure Synapse. Pour SQL Server (local), consultez [Découverte et classification de données SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Présentation de Découverte et classification des données

Découverte et classification des données introduit un ensemble de services avancés et de nouvelles fonctionnalités SQL qui forment un nouveau paradigme de protection des informations SQL visant à protéger les données, et pas seulement la base de données :

- **Découverte et recommandations**

  Le moteur de classification analyse votre base de données et identifie les colonnes contenant des données potentiellement sensibles. Il vous permet ensuite de vérifier et d’appliquer facilement les recommandations de classification appropriées par le biais du portail Azure.

- **Étiquetage**

  Vous pouvez appliquer de manière permanente des étiquettes de classification de sensibilité sur des colonnes à l’aide de nouveaux attributs de métadonnées de classification introduits dans le moteur SQL. Vous pouvez ensuite utiliser ces métadonnées pour des scénarios avancés d’audit et de protection basés sur la sensibilité.

- **Sensibilité du jeu de résultats de requête**

  La sensibilité du jeu de résultats de requête est calculée en temps réel à des fins d’audit.

- **Visibilité**

  Vous pouvez afficher l’état de classification de la base de données dans un tableau de bord détaillé dans le portail. Vous pouvez aussi télécharger un rapport (au format Excel) à utiliser entre autres à des fins de conformité et d’audit.

## <a name="discover-classify--label-sensitive-columns"></a><a id="discover-classify-columns"></a>Découvrir, classer et étiqueter des colonnes contenant des données sensibles

La section suivante décrit les étapes de découverte, de classification et d’étiquetage des colonnes contenant des données sensibles dans votre base de données, ainsi que l’affichage de l’état actuel de la classification de votre base de données et l’exportation de rapports.

La classification comprend deux attributs de métadonnées :

- **Étiquettes** : principaux attributs de classification, utilisés pour définir le niveau de confidentialité des données stockées dans la colonne.  
- **Types d’informations** : fournissent un niveau de granularité supplémentaire dans le type de données stockées dans la colonne.

## <a name="define-and-customize-your-classification-taxonomy"></a>Définir et personnaliser votre taxonomie de classification

La fonctionnalité Découverte et classification des données comprend un ensemble intégré d’étiquettes de sensibilité, ainsi qu’un ensemble intégré de types d’informations et de logiques de découverte. Vous avez désormais la possibilité de personnaliser cette taxonomie, et de définir un jeu et un classement de constructions de classification spécialement pour votre environnement.

La définition et la personnalisation de votre taxonomie de classification s’effectuent au même endroit pour l’ensemble de votre locataire Azure. Cet emplacement se trouve dans [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), dans la section relative à votre stratégie de sécurité. Seules les personnes disposant de droits d’administration pour le groupe d’administration racine du locataire peuvent effectuer cette tâche.

Dans le cadre de la gestion des stratégies Information Protection SQL, vous pouvez définir des étiquettes personnalisées, les classer et les associer à un ensemble de types d’informations. Vous pouvez également ajouter vos propres types d’informations personnalisées et les configurer avec des modèles de chaîne, que vous ajoutez à la logique de découverte qui sert à identifier ce type de données dans vos bases de données.
Pour plus d’informations sur la personnalisation et la gestion de votre stratégie, consultez le [Guide pratique sur les stratégies Information Protection SQL](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Une fois que la stratégie au niveau du locataire a été définie, vous pouvez passer à la classification des bases de données à l’aide de votre stratégie personnalisée.

## <a name="classify-your-sql-database"></a>Classer votre base de données SQL

1. Accédez au [portail Azure](https://portal.azure.com).

2. Accédez à **Advanced Data Security** sous l’en-tête Sécurité du volet Azure SQL Database. Cliquez pour activer Advanced Data Security, puis cliquez sur la carte **Découverte et classification des données**.

   ![Analyser une base de données](./media/sql-data-discovery-and-classification/data_classification.png)

3. L’onglet **Vue d’ensemble** comprend une synthèse de l’état actuel de la classification de la base de données, notamment une liste détaillée de toutes les colonnes classifiées, que vous pouvez aussi filtrer pour afficher uniquement des parties de schéma, des types d’informations et des étiquettes spécifiques. Si vous n’avez pas encore classifié de colonne, [passez à l’étape 5](#step-5).

   ![Résumé de l’état actuel de la classification](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Pour télécharger un rapport au format Excel, cliquez sur l’option **Exporter** dans le menu en haut de la fenêtre.

5. <a id="step-5"></a>Pour commencer à classifier vos données, cliquez sur l’onglet **Classification** en haut de la fenêtre.

6. Le moteur de classification analyse votre base de données à la recherche de colonnes contenant des données potentiellement sensibles, et il fournit une liste de **classifications de colonnes recommandées**. Pour afficher et appliquer les recommandations de classification :

   - Pour afficher la liste des classifications de colonnes recommandées, cliquez sur le panneau de recommandations en bas de la fenêtre.

   - Passez en revue la liste des recommandations. Pour accepter une recommandation pour une colonne spécifique, cochez la case dans la colonne de gauche de la ligne concernée. Vous pouvez également accepter *toutes les recommandations* en cochant la case dans l’en-tête de table de recommandations.

       ![Passer en revue la liste des recommandations](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Pour appliquer les recommandations sélectionnées, cliquez sur le bouton bleu **Accepter les recommandations sélectionnées**.

7. Vous pouvez aussi, en guise d’alternative, **classifier manuellement** des colonnes ou, en plus de la classification basée sur les recommandations :

   - Cliquez sur **Ajouter une classification** dans le menu en haut de la fenêtre.

   - Dans la fenêtre contextuelle qui apparaît, sélectionnez le schéma > table > colonne que vous souhaitez classifier, ainsi que l’étiquette de sensibilité et le type d’informations. Cliquez sur le bouton bleu **Ajouter une classification** en bas de la fenêtre contextuelle.

      ![Sélectionner une colonne à classer](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Pour terminer votre classification et étiqueter de manière permanente les colonnes de base de données avec les nouvelles métadonnées de classification, cliquez sur **Enregistrer** dans le menu en haut de la fenêtre.

## <a name="auditing-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Audit de l’accès aux données sensibles

Un aspect important du paradigme de protection des informations est la possibilité de surveiller l’accès aux données sensibles. [L’audit Azure SQL Database](sql-database-auditing.md) a été amélioré pour inclure dans le journal d’audit un nouveau champ nommé *data_sensitivity_information*, qui enregistre las classifications de la sensibilité (étiquettes) des données réelles retournées par la requête.

![Journal d’audit](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Autorisations

Les rôles intégrés suivants peuvent lire la classification des données d’une base de données Azure SQL : `Owner`, `Reader`, `Contributor`, `SQL Security Manager` et `User Access Administrator`.

Les rôles intégrés suivants peuvent modifier la classification des données d’une base de données Azure SQL : `Owner`, `Contributor`, `SQL Security Manager`.

En savoir plus sur le [contrôle d’accès en fonction du rôle pour les ressources Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="manage-classification"></a>Gérer les classifications

### <a name="using-t-sql"></a>Utilisation de T-SQL
Vous pouvez utiliser T-SQL pour ajouter/supprimer des classifications de colonne, ainsi que pour récupérer toutes les classifications pour la base de données entière.

> [!NOTE]
> Quand vous utilisez T-SQL pour gérer les étiquettes, aucune validation n’est effectuée pour vérifier que les étiquettes ajoutées à une colonne existent dans la stratégie de protection des informations de l’organisation (ensemble des étiquettes qui apparaissent dans les recommandations du portail). Il vous appartient d’effectuer cette validation.

- Ajouter/Mettre à jour la classification d’une ou plusieurs colonnes : [ADD SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Supprimer la classification d’une ou plusieurs colonnes : [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Voir toutes les classifications sur la base de données : [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="using-rest-api"></a>Utilisation de l’API REST
Vous pouvez utiliser l’API REST pour gérer par programme les classifications et les recommandations. L’API REST publiée prend en charge les opérations suivantes :

- [Créer ou mettre à jour](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) : crée ou met à jour l’étiquette de sensibilité d’une colonne donnée
- [Supprimer](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) : supprime l’étiquette de sensibilité d’une colonne donnée
- [Désactiver la recommandation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) : désactive les recommandations de sensibilité sur une colonne donnée
- [Activer la recommandation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) : active les recommandations de sensibilité sur une colonne donnée (les recommandations sont activées par défaut sur toutes les colonnes)
- [Obtenir](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) : obtient l’étiquette de sensibilité d’une colonne donnée
- [Liste actuelle par base de données](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) : affiche les étiquettes de sensibilité actuelles d’une base de données spécifiée
- [Liste recommandée par base de données](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) : affiche les étiquettes de sensibilité recommandées d’une base de données spécifiée

### <a name="using-powershell-cmdlet"></a>Utilisation de la cmdlet PowerShell
Vous pouvez utiliser PowerShell pour gérer les classifications et les recommandations pour Azure SQL Database et Managed Instance.

#### <a name="powershell-cmdlet-for-azure-sql-database"></a>Cmdlet PowerShell pour Azure SQL Database
- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Enable-AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instance"></a>Applets de commande PowerShell pour Managed Instance
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
