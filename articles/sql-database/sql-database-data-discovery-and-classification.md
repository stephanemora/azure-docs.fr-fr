---
title: Découverte et classification des données Azure SQL Database | Microsoft Docs
description: Découverte et classification des données Azure SQL Database
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 05/18/2018
ms.author: giladm
ms.openlocfilehash: b43b010a88f313930217289549448de30a82a070
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363806"
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Découverte et classification des données Azure SQL Database
Découverte et classification des données (actuellement en préversion) offre des fonctionnalités avancées intégrées à Azure SQL Database pour la **découverte**, la **classification**, l’**étiquetage**  & et la **protection** des données sensibles dans vos bases de données.
La découverte et la classification de vos données les plus sensibles (professionnelles/financières, soins de santé, informations d’identification personnelle, et ainsi de suite) peuvent jouer un rôle essentiel dans la protection des informations de l’organisation. Elles peuvent servir d’infrastructure pour :
* Aider à répondre aux normes de confidentialité des données et aux exigences de conformité aux normes, telles que RGPD.
* Divers scénarios de sécurité, comme la surveillance (audit) et la génération d’alertes en cas d’accès anormaux aux données sensibles.
* Contrôler l’accès et renforcer la sécurité des bases de données contenant des données sensibles.

Découverte et classification des données fait partie de l’offre [SQL Advanced Threat Protection](sql-advanced-threat-protection.md) (ATP) qui est un package unifié de fonctionnalités de sécurité SQL avancées. L’accès au composant Découverte et classification des données ainsi que sa gestion se font via le portail SQL ATP central.

> [!NOTE]
> Ce document se rapporte uniquement à Azure SQL Database. Pour SQL Server (local), consultez [Découverte et classification de données SQL](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Présentation de Découverte et classification des données
Découverte et classification des données introduit un ensemble de services avancés et de nouvelles fonctionnalités SQL qui forment un nouveau paradigme de protection des informations SQL visant à protéger les données, et pas seulement la base de données :
* **Découverte et recommandations** : Le moteur de classification analyse votre base de données et identifie les colonnes contenant des données potentiellement sensibles. Il vous permet ensuite de vérifier et d’appliquer facilement les recommandations de classification appropriées par le biais du portail Azure.
* **Étiquetage** : Vous pouvez appliquer de manière permanente des étiquettes de classification de sensibilité sur des colonnes à l’aide de nouveaux attributs de métadonnées de classification introduits dans le moteur SQL. Vous pouvez ensuite utiliser ces métadonnées pour des scénarios avancés d’audit et de protection basés sur la sensibilité.
* **Interrogation de la sensibilité du jeu de résultats** : La sensibilité du jeu de résultats est calculée en temps réel à des fins d’audit.
* **Visibilité** : Vous pouvez afficher l’état de classification de la base de données dans un tableau de bord détaillé dans le portail. Vous pouvez aussi télécharger un rapport (au format Excel) à utiliser entre autres à des fins de conformité et d’audit.

## <a id="subheading-2"></a>Découvrir, classer et étiqueter des colonnes contenant des données sensibles
La section suivante décrit les étapes de découverte, de classification et d’étiquetage des colonnes contenant des données sensibles dans votre base de données, ainsi que l’affichage de l’état actuel de la classification de votre base de données et l’exportation de rapports.

La classification comprend deux attributs de métadonnées :
* Étiquettes : principaux attributs de classification, utilisés pour définir le niveau de confidentialité des données stockées dans la colonne.  
* Types d’informations : spécifiez une granularité supplémentaire concernant le type des données stockées dans la colonne.

## <a name="classify-your-sql-database"></a>Classer votre base de données SQL

1. Accédez au [portail Azure](https://portal.azure.com).

2. Accédez à **Advanced Threat Protection** sous l’en-tête Sécurité du volet Azure SQL Database. Cliquez pour activer Advanced Threat Protection, puis cliquez sur la carte **Découverte et classification des données (préversion)**.

   ![Analyser une base de données](./media/sql-data-discovery-and-classification/data_classification.png) 

3. L’onglet **Vue d’ensemble** comprend une synthèse de l’état actuel de la classification de la base de données, notamment une liste détaillée de toutes les colonnes classifiées, que vous pouvez aussi filtrer pour afficher uniquement des parties de schéma, des types d’informations et des étiquettes spécifiques. Si vous n’avez pas encore classifié de colonne, [passez à l’étape 5](#step-5).

   ![Résumé de l’état actuel de la classification](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png) 

4. Pour télécharger un rapport au format Excel, cliquez sur l’option **Exporter** dans le menu en haut de la fenêtre.

   ![Exporter vers Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png) 

5.  <a id="step-5"></a>Pour commencer à classifier vos données, cliquez sur l’onglet **Classification** en haut de la fenêtre.

    ![Classer les données](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png) 

6. Le moteur de classification analyse votre base de données à la recherche de colonnes contenant des données potentiellement sensibles, et il fournit une liste de **classifications de colonnes recommandées**. Pour afficher et appliquer les recommandations de classification :

    * Pour afficher la liste des classifications de colonnes recommandées, cliquez sur le panneau de recommandations en bas de la fenêtre :
    
      ![Classer les données](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png) 

    * Passez en revue la liste des recommandations. Pour accepter une recommandation pour une colonne spécifique, cochez la case dans la colonne de gauche de la ligne concernée. Vous pouvez également accepter *toutes les recommandations* en cochant la case dans l’en-tête de table de recommandations.

       ![Passer en revue la liste des recommandations](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png) 

    * Pour appliquer les recommandations sélectionnées, cliquez sur le bouton bleu **Accepter les recommandations sélectionnées**.

      ![Appliquer les recommandations](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png) 

7. Vous pouvez aussi, en guise d’alternative, **classifier manuellement** des colonnes ou, en plus de la classification basée sur les recommandations :

    * Cliquez sur **Ajouter une classification** dans le menu en haut de la fenêtre.
  
      ![Ajouter manuellement une classification](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png) 

    * Dans la fenêtre contextuelle qui apparaît, sélectionnez le schéma > table > colonne que vous souhaitez classifier, ainsi que l’étiquette de sensibilité et le type d’informations. Cliquez sur le bouton bleu **Ajouter une classification** en bas de la fenêtre contextuelle.

      ![Sélectionner une colonne à classer](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png) 

8. Pour terminer votre classification et étiqueter de manière permanente les colonnes de base de données avec les nouvelles métadonnées de classification, cliquez sur **Enregistrer** dans le menu en haut de la fenêtre.

   ![Enregistrer](./media/sql-data-discovery-and-classification/10_data_classification_save.png) 

## <a id="subheading-3"></a>Audit de l’accès aux données sensibles

Un aspect important du paradigme de protection des informations est la possibilité de surveiller l’accès aux données sensibles. [L’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) a été amélioré pour inclure dans le journal d’audit un nouveau champ nommé *data_sensitivity_information*, qui enregistre las classifications de la sensibilité (étiquettes) des données réelles retournées par la requête.

![Journal d’audit](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png) 

## <a id="subheading-4"></a>Étapes suivantes

- En savoir plus sur [SQL Advanced Threat Protection](sql-advanced-threat-protection.md).
- Vous pouvez configurer [l’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) pour effectuer la surveillance et l’audit de l’accès à vos données sensibles classifiées.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4