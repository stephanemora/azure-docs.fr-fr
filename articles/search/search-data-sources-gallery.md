---
title: Galerie de sources de données
titleSuffix: Azure Cognitive Search
description: Liste toutes les sources de données prises en charge pour l’importation dans un index Recherche cognitive Azure.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
layout: LandingPage
ms.date: 06/23/2021
ms.openlocfilehash: e48c464ffa1aa5077f04ea1a7580e0d15e0fd7ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531836"
---
# <a name="data-sources-gallery"></a>Galerie de sources de données

Recherchez un connecteur de données auprès de Microsoft ou d’un partenaire pour simplifier l’ingestion des données dans un index de recherche. Cet article contient les sections suivantes :

+ [Sources de données en disponibilité générale par Recherche cognitive](#ga)
+ [Sources de données en préversion par Recherche cognitive](#preview)
+ [Connecteurs Power Query (préversion)](#powerquery)
+ [Sources de données de nos partenaires](#partners)

<a name="ga"></a>

## <a name="generally-available-data-sources-by-cognitive-search"></a>Sources de données en disponibilité générale par Recherche cognitive

Extrayez du contenu provenant d’autres services Azure en utilisant des indexeurs ainsi que les connecteurs de source de données suivants. 

:::row:::
:::column span="":::

---

### <a name="azure-blob-storage"></a>Stockage Blob Azure

par [Recherche cognitive](search-what-is-azure-search.md)

Extrayez du contenu et des métadonnées d’objets blob, sérialisés en documents JSON et importés dans un index de recherche en tant que documents de recherche. Établissez des propriétés dans les définitions de source de données et d’indexeur pour optimiser les différents types de contenu d’objet blob. La détection des changements est prise en charge automatiquement.

[En savoir plus](search-howto-indexing-azure-blob-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (API SQL)

par [Recherche cognitive](search-what-is-azure-search.md)

Connectez-vous à Cosmos DB via l’API SQL pour extraire des éléments sérialisés en documents JSON et importés dans un index de recherche en tant que documents de recherche, à partir d’un conteneur. Configurez le suivi des modifications pour actualiser l’index de recherche en fonction des derniers changements apportés à votre base de données.

[En savoir plus](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

par [Recherche cognitive](search-what-is-azure-search.md)

Extrayez des valeurs de champ sérialisées en documents JSON et importées dans un index de recherche en tant que documents de recherche, à partir d’une seule table ou vue. Configurez le suivi des modifications pour actualiser l’index de recherche en fonction des derniers changements apportés à votre base de données.

[En savoir plus](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

:::image type="icon" source="media/search-data-sources-gallery/azuresqlconnectorlogo_medium.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-table-storage"></a>Stockage de table Azure

par [Recherche cognitive](search-what-is-azure-search.md)

Extrayez des lignes sérialisées en documents JSON et importées dans un index de recherche en tant que documents de recherche, à partir d’une table Azure. 

[En savoir plus](search-howto-indexing-azure-tables.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

par [Recherche cognitive](search-what-is-azure-search.md)

Connectez-vous à Stockage Azure via Azure Data Lake Storage Gen2 pour extraire du contenu à partir d’une hiérarchie de répertoires et de sous-répertoires imbriqués.

[En savoir plus](search-howto-index-azure-data-lake-storage.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_storage.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="preview"></a>

## <a name="preview-data-sources-by-cognitive-search"></a>Sources de données en préversion par Recherche cognitive

Les nouvelles sources de données sont mises à disposition en tant que fonctionnalités en préversion. [Inscrivez-vous](https://aka.ms/azure-cognitive-search/indexer-preview) pour démarrer.

:::row:::
:::column span="":::

---

### <a name="cosmos-db-gremlin-api"></a>Cosmos DB (API Gremlin)

par [Recherche cognitive](search-what-is-azure-search.md)

Connectez-vous à Cosmos DB via l’API Gremlin pour extraire des éléments sérialisés en documents JSON et importés dans un index de recherche en tant que documents de recherche, à partir d’un conteneur. Configurez le suivi des modifications pour actualiser l’index de recherche en fonction des derniers changements apportés à votre base de données.

[En savoir plus](search-howto-index-cosmosdb-gremlin.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::column span="":::

---

### <a name="cosmos-db-mongo-api"></a>Cosmos DB (API Mongo)

par [Recherche cognitive](search-what-is-azure-search.md)

Connectez-vous à Cosmos DB via l’API Mongo pour extraire des éléments sérialisés en documents JSON et importés dans un index de recherche en tant que documents de recherche, à partir d’un conteneur. Configurez le suivi des modifications pour actualiser l’index de recherche en fonction des derniers changements apportés à votre base de données.

[En savoir plus](search-howto-index-cosmosdb.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_cosmos_db_logo_small.png":::

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

par [Recherche cognitive](search-what-is-azure-search.md)

Connectez-vous à un site SharePoint Online, et indexez les documents d’une ou de plusieurs bibliothèques de documents, pour les comptes et les services de recherche du même locataire. Le texte et les images normalisées sont extraits par défaut. Vous pouvez éventuellement configurer un ensemble de compétences pour accroître la transformation et l’enrichissement du contenu, ou configurer le suivi des modifications pour actualiser un index de recherche en fonction du contenu nouveau ou modifié dans SharePoint.

[En savoir plus](search-howto-index-sharepoint-online.md)

:::image type="icon" source="media/search-data-sources-gallery/sharepoint_online_logo.png":::

:::column-end:::
:::column span="":::

---

### <a name="azure-mysql"></a>Azure MySQL

par [Recherche cognitive](search-what-is-azure-search.md)

Connectez-vous à Base de données MySQL sur Azure pour extraire des lignes sérialisées en documents JSON et importées dans un index de recherche en tant que documents de recherche, à partir d’une table. Au cours des exécutions suivantes, l’indexeur prend en compte l’ensemble des changements, des chargements et des suppressions effectués dans votre base de données MySQL, et les reflète dans votre index de recherche.

[En savoir plus](search-howto-index-mysql.md)

:::image type="icon" source="media/search-data-sources-gallery/azure_mysql.png":::

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::row-end::': null
':::row::': null
':::column span=""::':
  ':::column-end::': null
  ':::column span=""::': null
---

<a name="powerquery"></a>

## <a name="power-query-connectors-preview"></a>Connecteurs Power Query (préversion)

Connectez-vous aux données d’autres plateformes cloud à l’aide d’indexeurs, et utilisez un connecteur Power Query en tant que source de données. [Inscrivez-vous](https://aka.ms/azure-cognitive-search/indexer-preview) pour démarrer.

:::row:::
:::column span="":::

---

### <a name="amazon-redshift"></a>Amazon Redshift

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Connectez-vous à [Amazon Redshift](https://aws.amazon.com/redshift/), et extrayez le contenu pouvant faire l’objet de recherches pour l’indexer dans Recherche cognitive.

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Connectez-vous à [Elasticsearch](https://www.elastic.co/elasticsearch) dans le cloud, et extrayez le contenu pouvant faire l’objet de recherches pour l’indexer dans Recherche cognitive.

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Connectez-vous à une base de données [PostgreSQL](https://www.postgresql.org/) dans le cloud, et extrayez le contenu pouvant faire l’objet de recherches pour l’indexer dans Recherche cognitive.

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce-objects"></a>Objets Salesforce

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Connectez-vous à des objets Salesforce, et extrayez le contenu pouvant faire l’objet de recherches pour l’indexer dans Recherche cognitive.

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="salesforce-reports"></a>Rapports Salesforce

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Connectez-vous à des rapports Salesforce, et extrayez le contenu pouvant faire l’objet de recherches pour l’indexer dans Recherche cognitive.

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---

### <a name="smartsheet"></a>Smartsheet

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Connectez-vous à Smartsheet, et extrayez le contenu pouvant faire l’objet de recherches pour l’indexer dans Recherche cognitive.

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="snowflake"></a>Snowflake

Technologie [Power Query](/power-query/power-query-what-is-power-query)

Extrayez des données et des métadonnées pouvant faire l’objet de recherches à partir d’une base de données Snowflake, puis remplissez un index en fonction des mappages de champ à champ entre l’index et votre source de données. 

[En savoir plus](search-how-to-index-power-query-data-sources.md)

:::column-end:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

<a name="partners"></a>

## <a name="data-sources-from-our-partners"></a>Sources de données de nos partenaires

Les connecteurs de source de données sont également fournis par des partenaires Microsoft tiers. Consultez nos [Conditions d’utilisation](search-data-sources-terms-of-use.md) ainsi que les instructions du partenaire concernant la licence et l’utilisation de la source de données avant de vous servir de celle-ci.

:::row:::
:::column span="":::

---

### <a name="aderant"></a>Aderant

par [BA Insight](https://www.bainsight.com/)

Le connecteur Aderant respecte la sécurité du système source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/aderant-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

par [Accenture](https://www.accenture.com)

Permet à votre entreprise d’analyser du contenu provenant du serveur Adobe Experience Manager, en fournissant une limitation de connexion ainsi qu’un filtrage des valeurs ou des modèles attendus.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/AEM+Connector)

:::column-end:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

par [BA Insight](https://www.bainsight.com/)

Le connecteur Adobe Experience Manager permet d’indexer du contenu managé par la plateforme Adobe Experience Manager (AEM). Il prend en charge l’analyse complète et l’analyse incrémentielle pour garantir l’actualisation de l’index.

[En savoir plus](https://www.bainsight.com/connectors/adobe-em-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="adobe-aem"></a>Adobe AEM

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Adobe Active Experience Manager (AEM), et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages, les pièces jointes et les autres types de document générés à partir d’Adobe AEM en quasi-temps réel. Le connecteur prend entièrement en charge le modèle d’autorisations d’Adobe AEM, sa gestion intégrée des utilisateurs et des groupes ainsi que les installations d’AEM basées sur Active Directory ou d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/adobe-experience-manager-aem)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

par [BA Insight](https://www.bainsight.com/)

Le connecteur Alfresco repose sur le framework de connecteurs BAI, qui représente la plateforme utilisée pour créer tous nos connecteurs en fournissant une connectivité sécurisée aux systèmes d’entreprise.

[En savoir plus](https://www.bainsight.com/connectors/alfresco-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="alfresco"></a>Alfresco

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Alfresco One, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers, dossiers et profils utilisateur d’Alfresco One en quasi-temps réel. Le connecteur prend entièrement en charge le modèle d’autorisations d’Alfresco One, sa gestion intégrée des utilisateurs et des groupes ainsi que les installations d’Alfresco One basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-alfresco-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="amazon-aurora"></a>Amazon Aurora

par [BA Insight](https://www.bainsight.com/)

Le connecteur Amazon Aurora repose sur des méthodes d’accès aux bases de données conformes aux standards du secteur. Il prend donc en charge de manière identique les bases de données d’autres systèmes tels qu’Oracle, MySQL et IBM DB2.

[En savoir plus](https://www.bainsight.com/connectors/amazon-aurora-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-rds"></a>Amazon RDS

par [BA Insight](https://www.bainsight.com/)

Le connecteur Amazon RDS repose sur des méthodes d’accès aux bases de données conformes aux standards du secteur. Il peut donc prendre en charge de manière identique les bases de données d’autres systèmes tels qu’Oracle, MySQL et IBM DB2.

[En savoir plus](https://www.bainsight.com/connectors/amazon-rds-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="amazon-s3"></a>Amazon S3

par [BA Insight](https://www.bainsight.com/)

Le connecteur Amazon S3 fonctionne avec l’ensemble du contenu stocké dans S3. Votre organisation peut utiliser le connecteur pour se connecter de manière sécurisée à S3 et indexer le contenu des compartiments S3. De puissantes fonctionnalités de filtrage permettent à votre organisation de contrôler le contenu à indexer dans S3.

[En savoir plus](https://www.bainsight.com/connectors/amazon-s3-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="aspider"></a>Aspider

par [Accenture](https://www.accenture.com)

Le connecteur Aspider permet l’analyse du contenu de sites web, à l’aide de l’authentification HTTP, des analyses incrémentielles, de la limitation de connexion, de l’analyse distribuée et de l’analyse HTTPS, entre autres fonctionnalités.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Aspider+Web+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="atlassian-confluence-cloud"></a>Atlassian Confluence (Cloud)

par [BA Insight](https://www.bainsight.com/)

Notre connecteur Confluence (version cloud) est un connecteur d’indexation de niveau entreprise, qui permet d’analyser et d’indexer le contenu stocké dans Confluence.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-confluence-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

par [BA Insight](https://www.bainsight.com)

Le connecteur Azure Active Directory de BA Insight permet d’exposer le contenu de votre locataire Azure Active Directory dans un seul index de recherche centralisé, avec le contenu d’autres dépôts, ce qui permet d’accélérer et de rendre plus efficace la recherche d’employés ou d’experts au sein d’une entreprise.

[En savoir plus](https://www.bainsight.com/connectors/azure-active-directory-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-ad"></a>Azure AD

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Microsoft Azure Active Directory (Azure AD), et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe les objets d’Azure AD via l’API Microsoft Graph. Le connecteur permet d’ingérer des principaux dans le service Recherche cognitive en quasi-temps réel pour implémenter des cas d’usage tels que la recherche d’experts, la recherche d’équipements et la recherche de lieux, ou pour fournir un filtrage de sécurité anticipé avec des sources de données personnalisées. Le connecteur prend en charge l’authentification fédérée avec Microsoft 365.

[En savoir plus](https://www.raytion.com/connectors/raytion-azure-ad-connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-blobs"></a>Objets blob Azure

par [Accenture](https://www.accenture.com)

Permet d’analyser un conteneur d’objets blob Azure, ce qui rend possible l’analyse incrémentielle, la sécurité au niveau des documents ainsi que l’accès aux dossiers et aux sous-dossiers.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Blob+Storage+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-data-lake"></a>Azure Data Lake

par [Accenture](https://www.accenture.com)

Le connecteur Azure Data Lake analyse le contenu du cloud Azure Data Lake Storage à la racine ou dans les chemins spécifiés, en appliquant l’analyse incrémentielle, l’extraction des listes ACL des objets, l’authentification OAuth 2, etc.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Data+Lake+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="azure-events-hub"></a>Azure Event Hubs

par [Accenture](https://www.accenture.com)

Analyse le contenu d’un hub Azure Event Hubs, ce qui permet l’analyse incrémentielle et la récupération de tous les types d’événement ou d’attribut.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Azure+Events+Hub+Connector)

:::column-end:::
:::column span="":::

---

### <a name="azure-sql-database"></a>Azure SQL Database

par [BA Insight](https://www.bainsight.com/)

Le connecteur Azure SQL Database de BA Insight respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/azure-sql-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="bentley"></a>Bentley

par [BA Insight](https://www.bainsight.com/)

Le connecteur Bentley AssetWise de BAI permet d’exposer le contenu d’AssetWise dans un seul index de recherche centralisé, avec le contenu d’autres dépôts.

[En savoir plus](https://www.bainsight.com/connectors/bentley-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="box"></a>Box

par [Accenture](https://www.accenture.com)

Le connecteur Box analyse le contenu d’un dépôt Box. Le connecteur récupère les éléments pris en charge à l’aide de l’API RESTful, ce qui permet l’analyse complète ou incrémentielle, l’extraction des métadonnées, l’extraction des listes ACL, etc.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Box++Connector)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Box

par [BA Insight](https://www.bainsight.com/)

Le connecteur Box permet d’exposer le contenu de Box dans SharePoint et d’autres portails, ce qui permet aux utilisateurs d’obtenir une intégration des résultats de la recherche à partir de SharePoint et de Box.

[En savoir plus](https://www.bainsight.com/connectors/box-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="box"></a>Box

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Box, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers, les dossiers, les commentaires, les utilisateurs, les groupes et les tâches de Box en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Box.

[En savoir plus](https://www.raytion.com/connectors/raytion-box-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

par [Accenture](https://www.accenture.com)

Le connecteur Confluence analyse le contenu de n’importe quel dépôt de contenu Confluence. Le connecteur récupère les espaces, les pages, les blogs, les pièces jointes et les commentaires via une API REST, ce qui permet l’analyse incrémentielle, l’extraction des listes ACL, la prise en charge des protocoles HTTP et HTTPS, etc.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Atlassian+Confluence+Connector)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

par [BA Insight](https://www.bainsight.com/)

Le connecteur Confluence est un connecteur d’indexation de niveau entreprise, qui permet d’analyser et d’indexer le contenu stocké dans Confluence. Cela permet à SharePoint, ou tout autre portail, de servir de point unique à partir duquel les utilisateurs peuvent rechercher et récupérer le contenu nécessaire provenant de plusieurs sources de contenu.

[En savoir plus](https://www.bainsight.com/connectors/confluence-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="confluence"></a>Confluence

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Atlassian Confluence, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les étiquettes des pages, des billets de blog, des pièces jointes, des commentaires, des espaces, des profils et des sites hub provenant d’instances locales de Confluence, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’Atlassian Confluence ainsi que les installations de Confluence basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-confluence-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="confluence-cloud"></a>Confluence Cloud

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Atlassian Confluence Cloud, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les étiquettes des pages, des billets de blog, des pièces jointes, des commentaires, des espaces, des profils et des sites hub provenant d’instances de Confluence Cloud, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’Atlassian Confluence Cloud.

[En savoir plus](https://www.raytion.com/connectors/raytion-confluence-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="cuadrastar"></a>CuadraSTAR

par [BA Insight](https://www.bainsight.com/)

Le connecteur CuadraSTAR analyse le contenu de CuadraSTAR et crée un seul index permettant d’utiliser Recherche cognitive Azure pour trouver des informations pertinentes dans CuadraSTAR ainsi que dans plus de 70 autres dépôts pris en charge, ce qui évite ainsi d’avoir à effectuer des recherches séparées.

[En savoir plus](https://www.bainsight.com/connectors/cuadrastar-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="database"></a>Base de données

par [Accenture](https://www.accenture.com)

Le connecteur Database Server analyse le contenu d’un serveur de base de données relationnelle ainsi que toutes ses bases de données, puis extrait les informations des lignes et des tables.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Database+Server+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="deltek"></a>Deltek

par [BA Insight](https://www.bainsight.com/)

Le connecteur Deltek Vision respecte la sécurité du système source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes. Il indexe le contenu de Deltek Vision dans Azure, SharePoint Online ou SharePoint 2016/2013, et expose ce contenu via SmartHub de BA Insight pour fournir aux utilisateurs une intégration des résultats de la recherche.

[En savoir plus](https://www.bainsight.com/connectors/deltek-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

par [Accenture](https://www.accenture.com)

Le connecteur Aspire Documentum DQL analyse le contenu de Documentum, ce qui permet les analyses basées sur des instructions DQL SELECT définies par l’utilisateur, l’analyse incrémentielle, l’extraction des listes ACL, l’extension de groupe des autorisations imbriquées, etc.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Documentum+DQL+Connector)

:::column-end:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

par [BA Insight](https://www.bainsight.com/)

Le connecteur Documentum de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des objets Documentum dans Recherche cognitive Azure, ce qui permet de disposer d’un seul jeu de résultats pouvant faire l’objet de recherches parmi le contenu provenant de plusieurs dépôts. Cela diffère de certains autres connecteurs qui exposent les enregistrements Documentum avec Recherche cognitive Azure, un par un, pour la gestion des processus.

[En savoir plus](https://www.bainsight.com/connectors/documentum-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="documentum"></a>Documentum

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’OpenText Documentum, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les dépôts, les dossiers et les fichiers ainsi que leurs métadonnées et propriétés à partir de Documentum en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’OpenText Documentum.

[En savoir plus](https://www.raytion.com/connectors/raytion-documentum-connector)

:::column-end:::
:::column span="":::

---

### <a name="egnyte"></a>Egnyte

par [BA Insight](https://www.bainsight.com/)

Le connecteur Egnyte prend en charge les analyses et index complets et incrémentiels à un débit très élevé.

[En savoir plus](https://www.bainsight.com/connectors/egnyte-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="elasticsearch"></a>Elasticsearch

par [Accenture](https://www.accenture.com)

Le connecteur Elasticsearch analyse le contenu provenant d’un index Elasticsearch, ce qui permet l’analyse de plusieurs index, la prise en charge des tranches, l’utilisation des méthodes Get et MGet pour l’extraction de contenu ainsi que la limitation de connexion.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Elasticsearch+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="elite--e3"></a>Elite / E3

par [BA Insight](https://www.bainsight.com/)

Le connecteur Elite de BA Insight permet aux avocats de disposer d’un seul point d’accès pour rechercher des informations propres aux cabinets juridiques en conformité avec le contenu Elite via le service Recherche cognitive Azure.

[En savoir plus](https://www.bainsight.com/connectors/elite-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="emc-eroom"></a>EMC eRoom

par [BA Insight](https://www.bainsight.com/)

Le connecteur eRoom établit une connexion sécurisée à l’application eRoom et mappe le contenu, notamment les métadonnées et les pièces jointes, du schéma eRoom au schéma du moteur de recherche. Il extrait ensuite le contenu et l’envoie au moteur de recherche dans le cadre d’un processus appelé analyse.

[En savoir plus](https://www.bainsight.com/connectors/eroom-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

par [BA Insight](https://www.bainsight.com/)

Les organisations qui utilisent Workplace by Facebook peuvent désormais étendre la portée de ces données à leurs index de recherche existants via le connecteur Workplace by Facebook de BA Insight.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-workplace-by-facebook/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="facebook-workplace"></a>Facebook Workplace

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Facebook Workplace, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les groupes de projets, les conversations et les documents partagés de Facebook Workplace, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Facebook Workplace.

[En savoir plus](https://www.raytion.com/connectors/raytion-facebook-workplace-connector)

:::column-end:::
:::column span="":::
---

### <a name="file-share"></a>Partage de fichiers

par [BA Insight](https://www.bainsight.com/)

Le connecteur File Share permet d’exposer le contenu des partages de fichiers (Windows, SMB/CIFS) dans un seul index de recherche centralisé, avec le contenu d’autres dépôts.

[En savoir plus](https://www.bainsight.com/connectors/file-share-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="file-system"></a>Système de fichiers

par [Accenture](https://www.accenture.com)

Le connecteur File System analyse le contenu provenant d’un emplacement de système de fichiers, ce qui permet l’analyse incrémentielle, l’extraction de métadonnées, le filtrage de documents en fonction du chemin ainsi que la prise en charge des systèmes de fichiers Windows/Linux/macOS.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/File+System+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::
---

### <a name="file-system"></a>Système de fichiers

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de systèmes de fichiers montés localement, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers et dossiers des systèmes de fichiers en quasi-temps réel.

[En savoir plus](https://www.raytion.com/connectors/raytion-file-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="firstspirit"></a>FirstSpirit

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’e-Spirit FirstSpirit, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages, les pièces jointes et les autres types de document générés à partir de FirstSpirit en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs, des groupes et des autorisations d’e-Spirit FirstSpirit ainsi que les installations de FirstSpirit basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-firstspirit-connector)

:::column-end:::
:::column span="":::

---

### <a name="gitlab"></a>GitLab

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de GitLab, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les projets, les fichiers, les dossiers, les messages de validation, les problèmes et les pages wiki de GitLab en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de GitLab.

[En savoir plus](https://www.raytion.com/connectors/raytion-gitlab-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="google-cloud-sql"></a>Google Cloud SQL

par [BA Insight](https://www.bainsight.com/)

Le connecteur Google Cloud SQL indexe le contenu de Google Cloud SQL dans l’index de Recherche cognitive Azure, et expose ce contenu via SmartHub de BA Insight pour fournir aux utilisateurs une intégration des résultats de la recherche.

[En savoir plus](https://www.bainsight.com/connectors/google-cloud-sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

par [BA Insight](https://www.bainsight.com/)

Le connecteur Google Drive de BAI permet d’exposer le contenu de Google Drive dans un seul index de recherche centralisé référençant le contenu de Google Drive, avec le contenu d’autres dépôts.

[En savoir plus](https://www.bainsight.com/connectors/google-drive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="google-drive"></a>Google Drive

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Google Drive, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers, les dossiers et les commentaires sur les lecteurs personnels et les lecteurs d’équipe à partir de Google Drive en quasi-temps réel. Le connecteur prend entièrement en charge le modèle d’autorisations intégré de Google Drive ainsi que la gestion des utilisateurs et des groupes par l’annuaire Google Admin Directory.

[En savoir plus](https://www.raytion.com/connectors/raytion-google-drive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="hp-consolidated-archive-eas"></a>HP Consolidated Archive (EAS)

par [BA Insight](https://www.bainsight.com/)

Le connecteur HP Consolidated Archive de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des documents d’archives pour divers moteurs de recherche, notamment Recherche SharePoint et Recherche Azure. Cela permet d’obtenir un seul jeu de résultats pouvant faire l’objet de recherches dans le contenu de plusieurs dépôts. Les organisations peuvent ainsi accéder à la multitude d’informations disponibles dans Consolidated Archive, SharePoint et d’autres dépôts, ce qui rend ces données immédiatement actionnables par les utilisateurs via la fonctionnalité de recherche.

[En savoir plus](https://www.bainsight.com/connectors/hp-consolidated-archive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

par [Accenture](https://www.accenture.com)

Le connecteur IBM Connections analyse le contenu du serveur IBM Connections, qui dispose de fonctionnalités d’analyse incrémentielle, d’extraction de métadonnées, d’extraction des listes ACL, de filtrage des documents par modèles regex, etc.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/IBM+Connections+Connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

par [BA Insight](https://www.bainsight.com/)

Le connecteur IBM Connections a été développé pour IBM Connections. Il établit une connexion sécurisée à l’application Connections et mappe le contenu, notamment les métadonnées et les pièces jointes, du schéma Connections au schéma du moteur de recherche. Il extrait ensuite le contenu et l’envoie au moteur de recherche dans le cadre d’un processus appelé analyse.

[En savoir plus](https://www.bainsight.com/connectors/ibm-connections-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-connections"></a>IBM Connections

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’IBM Connections, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers publics et personnels, les blogs, les wikis, les forums, les communautés, les signets, les profils et les mises à jour d’état provenant d’instances locales de Connections, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’IBM Connection ainsi que les installations de Connections basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-ibm-connections-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-connections-cloud"></a>IBM Connections Cloud

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’IBM Connections Cloud, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers publics et personnels, les blogs, les wikis, les forums, les communautés, les profils et les mises à jour d’état de Connections Cloud, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’IBM Connections Cloud.

[En savoir plus](https://www.raytion.com/connectors/raytion-ibm-connections-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="ibm-content-manager"></a>IBM Content Manager

par [BA Insight](https://www.bainsight.com/)

Le connecteur IBM Content Manager respecte la sécurité des applications sources, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/ibm-content-manager-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-db2"></a>IBM Db2

par [BA Insight](https://www.bainsight.com/)

Le connecteur Db2 permet aux organisations d’accéder à la multitude de données stockées dans les bases de données et applications DB2, et de rendre ces données immédiatement actionnables par les utilisateurs via la fonctionnalité de recherche.

[En savoir plus](https://www.bainsight.com/connectors/ibm-db2-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-filenet-p8"></a>IBM FileNet P8

par [BA Insight](https://www.bainsight.com/)

Le connecteur IBM FileNet Content Manager permet aux utilisateurs SharePoint et aux autres utilisateurs de portail de rechercher de manière sécurisée du contenu stocké dans les dépôts FileNet. L’accès au contenu est déterminé par la sécurité établie dans FileNet, ce qui garantit la sécurisation de ce contenu, que ce soit en y accédant via un autre portail ou directement dans FileNet.

[En savoir plus](https://www.bainsight.com/connectors/ibm-filenet-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ibm-lotus-notes"></a>IBM Lotus Notes

par [BA Insight](https://www.bainsight.com/)

Avec le connecteur IBM Notes Email de BA Insight, les utilisateurs peuvent effectuer des recherches dans les e-mails Lotus Notes directement à partir de SharePoint ou d’un autre portail. La sécurité définie dans IBM Notes est automatiquement reflétée dans l’expérience de recherche. Ainsi, les utilisateurs voient les résultats de la recherche à partir de leur propre boîte aux lettres, des boîtes aux lettres publiques et des autres boîtes aux lettres auxquelles l’accès leur a été octroyé.

[En savoir plus](https://www.bainsight.com/connectors/lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="ibm-websphere"></a>IBM WebSphere

par [BA Insight](https://www.bainsight.com/)

Le connecteur WebSphere de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des objets WebSphere dans le moteur de recherche de Microsoft, ce qui permet de disposer d’un seul jeu de résultats pouvant faire l’objet de recherches parmi le contenu provenant de plusieurs dépôts. Les organisations peuvent ainsi accéder à la multitude d’informations disponibles sur les plateformes Microsoft, ce qui rend ces données immédiatement actionnables par les utilisateurs via la fonctionnalité de recherche.

[En savoir plus](https://www.bainsight.com/connectors/ibm-websphere-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-cloud"></a>iManage Cloud

par [BA Insight](https://www.bainsight.com/)

Le connecteur iManage Cloud de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des documents des espaces de travail Work pour le moteur de recherche.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-imanage-work-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="imanage-work"></a>iManage Work

par [BA Insight](https://www.bainsight.com/)

Le connecteur iManage Work offre une sécurité totale et fonctionne à un débit élevé pour réduire les temps d’analyse tout en conservant un faible impact sur les performances de Work. Il nécessite uniquement un accès en lecture. Il n’est pas nécessaire d’installer un logiciel client sur un serveur iManage. Il en résulte un accès fluide et simultané à l’ensemble du contenu stocké dans iManage Work.

[En savoir plus](https://www.bainsight.com/connectors/imanage-work-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira"></a>Jira

par [BA Insight](https://www.bainsight.com/)

Le connecteur Jira permet aux utilisateurs d’effectuer des recherches sur tous les objets Jira, ce qui évite d’avoir à accéder directement à Jira.

[En savoir plus](https://www.bainsight.com/connectors/jira-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jira"></a>Jira

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Atlassian Jira, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les projets, les problèmes, les pièces jointes, les commentaires, les journaux de travail, les historiques de problèmes, les liens et les profils provenant d’instances locales de Jira en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’Atlassian Jira ainsi que les installations de Jira basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-jira-connector)

:::column-end:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

par [BA Insight](https://www.bainsight.com/)

Le connecteur Jira (version cloud) effectue des recherches sur tous les objets Jira, ce qui évite d’avoir à accéder directement à Jira.

[En savoir plus](https://www.bainsight.com/connectors/jira-cloud-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="jira-cloud"></a>Jira Cloud

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Atlassian Jira Cloud, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les projets, les problèmes, les pièces jointes, les commentaires, les journaux de travail, les historiques de problèmes, les liens et les profils de Jira Cloud en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’Atlassian Jira Cloud.

[En savoir plus](https://www.raytion.com/connectors/raytion-jira-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

par [BA Insight](https://www.bainsight.com/)

Le connecteur Jive a été développé pour Jive. Il établit une connexion sécurisée à l’application Jive et mappe le contenu, notamment les métadonnées et les pièces jointes, du schéma Jive au schéma du moteur de recherche. Il extrait ensuite le contenu et l’envoie au moteur de recherche dans le cadre d’un processus appelé analyse.

[En savoir plus](https://www.bainsight.com/connectors/jive-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="jive"></a>Jive

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Jive, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les discussions, les sondages, les fichiers, les blogs, les espaces, les groupes, les projets, les tâches, les vidéos, les messages, les idées, les profils et les mises à jour d’état provenant d’instances locales de Jive et hébergées dans le cloud, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Jive ainsi que les modèles d’authentification natifs de Jive, OAuth et l’authentification de base.

[En savoir plus](https://www.raytion.com/connectors/raytion-jive-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="kaltura"></a>Kaltura

par [BA Insight](https://www.bainsight.com/)

Le connecteur Kaltura permet d’indexer non seulement les vidéos, mais également d’autres types d’information, notamment les catégories, les données, les documents, etc.

[En savoir plus](https://www.bainsight.com/connectors/kaltura-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

par [BA Insight](https://www.bainsight.com/) 

Le connecteur LDAP permet aux organisations de se connecter à n’importe quel annuaire conforme à LDAP et d’indexer n’importe quel enregistrement à partir de celui-ci. Les organisations peuvent filtrer des sous-ensembles spécifiques d’annuaires et récupérer uniquement des champs précis, ce qui permet de rechercher facilement des utilisateurs, des contacts ou des groupes stockés n’importe où dans votre annuaire.

[En savoir plus](https://www.bainsight.com/connectors/ldap-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="ldap"></a>LDAP

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de services d’annuaire compatibles LDAP (Lightweight Directory Access Protocol), et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les objets LDAP d’Active Directory, de Novell E-Directory et d’autres services d’annuaire compatibles LDAP, en quasi-temps réel. Le connecteur permet d’ingérer des principaux dans le service Google Cloud Search pour les cas d’usage tels que la recherche d’experts, d’équipements et de lieux, ou pour implémenter un filtrage de sécurité sur des sources de données personnalisées. Le connecteur prend en charge LDAP sur SSL.

[En savoir plus](https://www.raytion.com/connectors/raytion-ldap-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="legalkey"></a>LegalKEY

par [BA Insight](https://www.bainsight.com/) 

Le connecteur OpenText LegalKEY de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des enregistrements relatifs aux clients et aux dossiers juridiques de LegalKEY dans le moteur de recherche de Microsoft, ce qui permet de disposer d’un seul jeu de résultats pouvant faire l’objet de recherches parmi le contenu provenant de plusieurs dépôts. Les organisations peuvent ainsi accéder à la multitude d’informations disponibles dans LegalKEY, SharePoint et d’autres dépôts, ce qui rend ces données immédiatement actionnables par les utilisateurs via la fonctionnalité de recherche.

[En savoir plus](https://www.bainsight.com/connectors/legalkey-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lexisnexis-interaction"></a>LexisNexis InterAction

par [BA Insight](https://www.bainsight.com/)

Le connecteur LexisNexis InterAction permet aux juristes et autres employés d’un cabinet d’avocats de trouver plus facilement les informations importantes stockées dans InterAction sans avoir à se connecter directement et à effectuer une recherche distincte.

[En savoir plus](https://www.bainsight.com/connectors/lexisnexis-interaction-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="lotus-notes-databases"></a>Lotus Notes Databases

par [BA Insight](https://www.bainsight.com/) 

Avec le connecteur IBM Notes Database, les utilisateurs peuvent trouver du contenu stocké dans les bases de données Notes à l’aide de Recherche cognitive Azure. La sécurité définie dans IBM Notes est automatiquement reflétée dans l’expérience de recherche, ce qui permet aux utilisateurs de voir le contenu auquel ils ont l’autorisation d’accéder. En fin de compte, les utilisateurs peuvent trouver tout ce dont ils ont besoin au même endroit.

[En savoir plus](https://www.bainsight.com/connectors/ibm-lotus-notes-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="m-files"></a>M-Files

par [BA Insight](https://www.bainsight.com/)

Le connecteur M-Files permet d’indexer du contenu managé par la plateforme M-Files. Il prend en charge l’analyse complète et l’analyse incrémentielle pour garantir l’actualisation de l’index.

[En savoir plus](https://www.bainsight.com/connectors/m-files-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediaplatform-primetime"></a>MediaPlatform PrimeTime

par [BA Insight](https://www.bainsight.com/)

Le connecteur d’indexation MediaPlatform PrimeTime de BA Insight permet aux utilisateurs d’accéder à du contenu via la plateforme de recherche de contenu d’entreprise d’une organisation. Cela est rendu possible grâce à la combinaison du connecteur à SmartHub de BA Insight. Le connecteur MediaPlatform PrimeTime de BA Insight récupère les informations relatives aux chaînes et aux vidéos à partir de MediaPlatform PrimeTime, puis les indexe via Recherche cognitive Azure.

[En savoir plus](https://www.bainsight.com/connectors/mediaplatform-primetime-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="mediawiki"></a>MediaWiki

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de MediaWiki, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages, les pages de discussion et les pièces jointes provenant d’instances de MediaWiki en quasi-temps réel. Le connecteur prend entièrement en charge le modèle d’autorisations intégré de MediaWiki ainsi que les installations de MediaWiki basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-mediawiki-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="micro-focus-content-manager-hpe-records-managerhp-trim"></a>Micro Focus Content Manager (HPE Records Manager/HP TRIM)

par [BA Insight](https://www.bainsight.com/)

Le connecteur HP TRIM a été développé pour HP Records Manager. Il établit une connexion sécurisée à l’application TRIM et mappe le contenu, notamment les métadonnées et les pièces jointes, du schéma TRIM au schéma du moteur de recherche. Il extrait ensuite le contenu et l’envoie au moteur de recherche dans le cadre d’un processus appelé analyse.

[En savoir plus](https://www.bainsight.com/connectors/hp-trim-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365"></a>Microsoft Dynamics 365

par [BA Insight](https://www.bainsight.com/)

Notre connecteur Microsoft Dynamics 365 CRM prend en charge à la fois les installations CRM locales et Dynamics CRM Online.

[En savoir plus](https://www.bainsight.com/connectors/microsoft-dynamics-crm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-dynamics-365-cloud"></a>Microsoft Dynamics 365 (Cloud)

par [BA Insight](https://www.bainsight.com/)

Notre connecteur Microsoft Dynamics 365 (Cloud Version) CRM établit une connexion sécurisée à l’application CRM et mappe le contenu du schéma CRM au schéma du moteur de recherche.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-microsoft-dynamics-cloud/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

par [BA Insight](https://www.bainsight.com/)

À l’aide du connecteur Microsoft Exchange Online de BA Insight, les utilisateurs peuvent récupérer du contenu d’Exchange Online via diverses plateformes de recherche.

[En savoir plus](https://www.bainsight.com/connectors/microsoft-exchange-online-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-public-folders"></a>Microsoft Exchange Public Folders

par [BA Insight](https://www.bainsight.com/)

À l’aide du connecteur Microsoft Exchange Public Folders de BAI, les utilisateurs peuvent récupérer du contenu d’Exchange via diverses plateformes de recherche.

[En savoir plus](https://www.bainsight.com/connectors/microsoft-exchange-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-exchange-server"></a>Microsoft Exchange Server

par [BA Insight](https://www.bainsight.com/)

À l’aide du connecteur Microsoft Exchange de BA Insight, les utilisateurs peuvent récupérer du contenu d’Exchange via diverses moteurs de recherche.

[En savoir plus](https://www.bainsight.com/connectors/microsoft-exchange-server-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

par [BA Insight](https://www.bainsight.com/)

Le connecteur de base de données repose sur des méthodes d’accès aux bases de données conformes aux standards du secteur. Il peut donc prendre en charge de manière identique les bases de données d’autres systèmes tels qu’Oracle, MySQL et IBM DB2. Il respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-teams"></a>Microsoft Teams

par [BA Insight](https://www.bainsight.com/)

Le connecteur Microsoft Teams de BA Insight indexe le contenu de Microsoft Teams avec le contenu d’autres systèmes d’entreprise pour fournir des résultats unifiés.

[En savoir plus](https://www.bainsight.com/connectors/microsoft-teams-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="microsoft-windows-file-server"></a>Microsoft Windows File Server

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’un serveur de fichiers Microsoft Windows, notamment son système de fichiers DFS, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers et dossiers d’un serveur de fichiers Windows en quasi-temps réel. Le connecteur prend entièrement en charge la sécurité au niveau des documents du serveur de fichiers Microsoft Windows ainsi que les dernières versions des protocoles SMB2 et SMB3.

[En savoir plus](https://www.raytion.com/connectors/raytion-windows-file-server-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="mysql"></a>MySQL

par [BA Insight](https://www.bainsight.com/)

Le connecteur MySQL repose sur des méthodes d’accès aux bases de données conformes aux standards du secteur. Il peut donc prendre en charge de manière identique les bases de données d’autres systèmes tels qu’Oracle, MySQL et IBM DB2. Il respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/mysql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="netdocuments"></a>NetDocuments

par [BA Insight](https://www.bainsight.com/)

Le connecteur NetDocuments indexe le contenu stocké dans NetDocs, ce qui permet aux utilisateurs de rechercher et de récupérer du contenu NetDocuments directement à partir de leur portail. Le connecteur applique automatiquement la sécurité des documents de NetDocs à Recherche cognitive Azure pour que les informations utilisateur restent sécurisées. Les métadonnées stockées dans NetDocuments peuvent être mappées à des termes équivalents pour offrir aux utilisateurs une expérience de recherche fluide.

[En savoir plus](https://www.bainsight.com/connectors/netdocuments-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="neudesic-the-firm-directory"></a>The Firm Directory - Neudesic

par [BA Insight](https://www.bainsight.com/)

Le connecteur Firm Directory respecte la sécurité du système source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/the-firm-directory-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="notes"></a>Notes

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’IBM Notes (anciennement Lotus Note), et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les enregistrements d’un ensemble configurable de bases de données Notes en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’IBM Notes.

[En savoir plus](https://www.raytion.com/connectors/raytion-notes-connector)

:::column-end:::
:::column span="":::

---

### <a name="nuxeo"></a>Nuxeo

par [BA Insight](https://www.bainsight.com/)

Le connecteur Nuxeo permet aux organisations d’indexer leur contenu Nuxeo, notamment les informations de sécurité ainsi que les métadonnées standard et personnalisées définies sur du contenu dans Recherche cognitive Azure aux côtés du contenu présent dans Office 365. En fin de compte, les utilisateurs peuvent trouver tout ce dont ils ont besoin au même endroit.

[En savoir plus](https://www.bainsight.com/connectors/nuxeo-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="objective"></a>Objectif

par [BA Insight](https://www.bainsight.com/)

Le connecteur Objective a été développé pour Objective. Il établit une connexion sécurisée à Objective et mappe le contenu, notamment les métadonnées, du schéma Objective au schéma du moteur de recherche. Il extrait ensuite le contenu et l’envoie au moteur de recherche dans le cadre d’un processus appelé analyse.

[En savoir plus](https://www.bainsight.com/connectors/objective-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="onedrive"></a>OneDrive

par [Accenture](https://www.accenture.com)

Le connecteur OneDrive analyse le contenu de Microsoft OneDrive, ce qui permet une analyse incrémentielle, l’inclusion et l’exclusion d’éléments en fonction de modèles regex, l’extraction de métadonnées ainsi que la récupération de plusieurs types de document.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/OneDrive+Connector)

:::column-end:::
:::column span="":::

---

### <a name="onedrive-for-business"></a>OneDrive Entreprise

par [BA Insight](https://www.bainsight.com/)

Le connecteur OneDrive de BA Insight permet d’indexer le contenu de OneDrive pour diverses plateformes de recherche, ce qui permet aux utilisateurs d’obtenir une intégration des résultats de la recherche à partir de plusieurs sources.

[En savoir plus](https://www.bainsight.com/connectors/onedrive-business-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

par [BA Insight](https://www.bainsight.com/)

Le connecteur indexe le contenu de Content Server à peu près de la même manière que le contenu du portail natif, en prenant en charge à la fois les analyses complètes et les analyses incrémentielles. La sécurité définie dans Content Server est automatiquement reflétée dans l’expérience de recherche, ce qui permet aux utilisateurs de voir uniquement le contenu auquel ils ont l’autorisation d’accéder.

[En savoir plus](https://www.bainsight.com/connectors/livelink-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-content-server"></a>OpenText Content Server

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’OpenText Content Server, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers, les dossiers, les dossiers virtuels, les documents composés, les actualités, les e-mails, les volumes, les collections, les classifications et bien d’autres objets provenant d’instances locales de Content Server, en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’OpenText Content Server.

[En savoir plus](https://www.raytion.com/connectors/raytion-opentext-content-server-connector)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-cloud"></a>OpenText Documentum (Cloud)

par [BA Insight](https://www.bainsight.com/)

Le connecteur OpenText Documentum Cloud de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des objets Documentum dans le moteur de recherche, ce qui permet de disposer d’un seul jeu de résultats pouvant faire l’objet de recherches parmi le contenu provenant de plusieurs dépôts.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-documentum-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="opentext-documentum-eroom"></a>OpenText Documentum eRoom

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’OpenText Documentum eRoom, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les dépôts, les dossiers et les fichiers ainsi que leurs métadonnées et propriétés à partir de Documentum eRoom en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’OpenText Documentum eRoom.

[En savoir plus](https://www.raytion.com/connectors/raytion-opentext-documentum-eroom-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="opentext-edocs-dm"></a>OpenText eDOCS DM

par [BA Insight](https://www.bainsight.com/)

Les utilisateurs du connecteur OpenText eDOCS DM peuvent rechercher du contenu hébergé dans les dépôts eDOCS directement à partir de Recherche cognitive Azure, ce qui leur permet d’éviter d’avoir à effectuer plusieurs recherches pour localiser le contenu nécessaire. La sécurité établie dans eDOCS est gérée par le connecteur, ce qui permet de garantir que le contenu est visible uniquement par les personnes auxquelles l’accès a été octroyé.

[En savoir plus](https://www.bainsight.com/connectors/edocs-dm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-database"></a>Oracle Database

par [BA Insight](https://www.bainsight.com/)

Le connecteur Oracle Database repose sur des méthodes d’accès aux bases de données conformes aux standards du secteur. Il peut donc prendre en charge de manière identique les bases de données d’autres systèmes tels que Microsoft SQL Server, MySQL et IBM DB2.

[En savoir plus](https://www.bainsight.com/connectors/oracle-database-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter"></a>Oracle WebCenter

par [BA Insight](https://www.bainsight.com/)

Le connecteur WebCenter intègre WebCenter à Recherche cognitive Azure, ce qui permet à tous les utilisateurs de l’organisation de trouver plus facilement les informations importantes stockées dans WebCenter sans avoir à se connecter directement et à effectuer une recherche distincte.

[En savoir plus](https://www.bainsight.com/connectors/oracle-webcenter-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="oracle-ka-cloud"></a>Oracle KA Cloud

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant d’Oracle Knowledge Advanced (KA) Cloud, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages et les pièces jointes d’Oracle KA Cloud en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes d’Oracle KA Cloud. En particulier, le connecteur gère les autorisations basées sur des extraits dans les pages Oracle KA Cloud.

[En savoir plus](https://www.raytion.com/connectors/raytion-oracle-ka-cloud-connector)

:::column-end:::
:::column span="":::

---

### <a name="oracle-webcenter-content-ucmstellent"></a>Oracle WebCenter Content (UCM/Stellent)

par [BA Insight](https://www.bainsight.com/)

Le connecteur WebCenter Content prend entièrement en charge la sécurité sous-jacente de l’ensemble du contenu mis à disposition pour Recherche cognitive Azure. Il maintient ce contenu à jour via des analyses planifiées, ce qui permet aux utilisateurs de disposer des mises à jour les plus récentes quand ils effectuent une recherche.

[En savoir plus](https://www.bainsight.com/connectors/oracle-webcenter-content-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="pirobase-cms"></a>pirobase CMS

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de pirobase CMS, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages, les pièces jointes et les autres types de document générés à partir de pirobase CMS en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de pirobase CMS.

[En savoir plus](https://www.raytion.com/connectors/raytion-pirobase-cms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="postgresql"></a>PostgreSQL

par [BA Insight](https://www.bainsight.com/)

Le connecteur PostgreSQL de BA Insight respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes. Il indexe le contenu de PostgreSQL dans Recherche cognitive Azure, et expose ce contenu via SmartHub de BA Insight pour fournir aux utilisateurs une intégration des résultats de la recherche.

[En savoir plus](https://www.bainsight.com/connectors/postgresql-connector-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="practical-law"></a>Practical Law

par [BA Insight](https://www.bainsight.com/)

Le connecteur Practical Law de BA Insight permet aux utilisateurs d’effectuer des recherches dans la base de données Practical Law, ce qui évite d’avoir à accéder directement à Practical Law.

[En savoir plus](https://www.bainsight.com/connectors/practical-law-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="prolaw"></a>ProLaw

par [BA Insight](https://www.bainsight.com/)

Le connecteur Pro Law de BA Insight connecte n’importe quel portail à ProLaw, ce qui permet d’exposer les informations de ProLaw tout en respectant les privilèges des utilisateurs au sein de ProLaw.

[En savoir plus](https://www.bainsight.com/connectors/prolaw-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="rdb-via-snapshots"></a>RDB via Snapshots

par [Accenture](https://www.accenture.com)

Le connecteur RDB via Snapshots analyse le contenu des bases de données relationnelles accessibles à l’aide de JDBC, et effectue des analyses incrémentielles à partir d’une base de données de captures instantanées. Il extrait les données directement à partir d’instructions SQL.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Snapshots+Connector)

:::column-end:::
:::column span="":::

---

### <a name="rdb-via-tables"></a>RDB via Tables

par [Accenture](https://www.accenture.com)

Le connecteur RDB via Tables analyse le contenu des bases de données relationnelles accessible à l’aide de JDBC, et effectue des analyses incrémentielles en récupérant les mises à jour provenant de tables qui contiennent les identificateurs de contenu mis à jour. Il extrait les données directement à partir d’instructions SQL.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/RDB+via+Table+Connector)

:::column-end:::
:::column span="":::

---

### <a name="s3"></a>S3

par [Accenture](https://www.accenture.com)

Le connecteur Amazon S3 analyse le contenu de n’importe quel stockage Amazon S3 (Simple Storage Service). Il effectue des analyses incrémentielles, récupère les listes ACL des objets pour la sécurité au niveau des documents S3, et inclut les documents stockés dans des compartiments, des dossiers et des sous-dossiers.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Amazon+S3+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

par [Accenture](https://www.accenture.com)

Le connecteur Salesforce analyse le contenu d’un dépôt Salesforce. Le connecteur prend en charge Salesforce Knowledge Base et Chatter ainsi que la récupération des métadonnées et des métadonnées personnalisées. Il effectue des analyses complètes ou incrémentielles, et extrait les listes ACL, les types d’élément sélectionnables et l’extension de groupe.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Salesforce+Connector)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

par [BA Insight](https://www.bainsight.com/)

Le connecteur Salesforce intègre les modules Service Cloud, Sales, Cloud et Marketing Cloud de Salesforce au service Recherche cognitive Azure, ce qui rend l’ensemble du contenu de Salesforce accessible à tous les employés via ce portail.

[En savoir plus](https://www.bainsight.com/connectors/salesforce-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="salesforce"></a>Salesforce

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Salesforce, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les comptes, les messages Chatter, les profils, les prospects, les incidents et tous les autres objets d’enregistrement de Salesforce en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Salesforce.

[En savoir plus](https://www.raytion.com/connectors/raytion-salesforce-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-erp"></a>SAP ERP

par [BA Insight](https://www.bainsight.com/)

Le connecteur SAP ERP de BA Insight est conçu pour inclure des éléments de SAP dans un index de recherche. Cela permet de tirer parti de l’IU et d’avoir une vue unifiée des informations dans SAP, SharePoint et d’autres systèmes.

[En savoir plus](https://www.bainsight.com/connectors/sap-erp-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sap-erp-cloud"></a>SAP ERP (Cloud)

par [BA Insight](https://www.bainsight.com/)

Le connecteur SAP ERP (Cloud Version) de BA Insight est conçu pour inclure des éléments de SAP dans un index de recherche.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-sap-erp-cloud/)

:::column-end:::
:::column span="":::

---

### <a name="sap-hana"></a>SAP HANA

par [BA Insight](https://www.bainsight.com/)

Le connecteur SAP HANA respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes. Il indexe le contenu de SAP HANA dans Recherche cognitive Azure, et expose ce contenu via SmartHub de BA Insight pour fournir aux utilisateurs une intégration des résultats de la recherche.

[En savoir plus](https://www.bainsight.com/connectors/sap-hana-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sap-hana-cloud"></a>SAP HANA (Cloud)

par [BA Insight](https://www.bainsight.com/)

Le connecteur SAP HANA (Cloud) respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/connector-sap-hana-cloud-version/)

:::column-end:::
:::column span="":::

---

### <a name="sap-netweaver-portal"></a>SAP NetWeaver Portal

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant du portail NWP (SAP NetWeaver Portal), et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages, les pièces jointes et d’autres types de document à partir de SAP NWP, KMC (Knowledge Management and Collaboration) et PCD (Portal Content Directory) en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de SAP NetWeaver Portal ainsi que les installations de SAP NWP basées sur Active Directory et d’autres services d’annuaire.

[En savoir plus](https://www.raytion.com/connectors/raytion-sap-netweaver-portal-connector)

:::column-end:::
:::column span="":::

---

### <a name="sap-plm-dms"></a>SAP PLM DMS

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de SAP PLM DMS, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les documents, les pièces jointes et d’autres enregistrements de SAP PLM DMS en quasi-temps réel.

[En savoir plus](https://www.raytion.com/connectors/raytion-sap-plm-dms-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="selenium"></a>Selenium

par [Accenture](https://www.accenture.com)

Le connecteur Selenium analyse le contenu des sites web à l’aide d’un navigateur Internet pour récupérer plusieurs types de document tels que des pages web, des plans de site, des documents binaires, etc. Cela permet d’éviter les problèmes de compatibilité avec les frameworks comme Angular et React.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Selenium+Crawler)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

par [Accenture](https://www.accenture.com)

Le connecteur ServiceNow récupère plusieurs types de document à partir d’un dépôt ServiceNow, par exemple les articles de base de connaissances, les éléments de catalogue et les pièces jointes. Il récupère également les listes ACL de sécurité et effectue l’extension de groupe.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/ServiceNow+Connector)

:::column-end:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

par [BA Insight](https://www.bainsight.com/)

 Le connecteur ServiceNow respecte la sécurité du système source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/servicenow-connector-sharepoint-azure-elasticsearch)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="servicenow"></a>ServiceNow

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de ServiceNow, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les problèmes, les tâches, les pièces jointes, les articles de gestion des connaissances et les pages, entre autres choses, de ServiceNow, en quasi-temps réel. Le connecteur prend en charge la gestion intégrée des utilisateurs et des groupes de ServiceNow.

[En savoir plus](https://www.raytion.com/connectors/raytion-servicenow-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint"></a>SharePoint

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Microsoft SharePoint, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les sites, les sites web, les pages modernes (SharePoint 2016 et versions ultérieures) et classiques, les pages wiki, les documents OneNote, les éléments de liste, les tâches, les éléments de calendrier, les pièces jointes ainsi que les fichiers provenant d’instances locales de SharePoint en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Microsoft SharePoint ainsi que les fournisseurs Active Directory et OAuth tels que SiteMinder et Okta. Le connecteur prend en charge les authentifications de base, NTLM et Kerberos.

[En savoir plus](https://www.raytion.com/connectors/raytion-sharepoint-connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2010"></a>SharePoint 2010

par [BA Insight](https://www.bainsight.com/)

Le connecteur SharePoint 2010 de BA Insight vous permet de vous connecter à SharePoint 2010 pour récupérer les données de n’importe quel site, bibliothèque de documents ou liste, et indexer ce contenu de manière sécurisée.

[En savoir plus](https://www.bainsight.com/connectors/sharepoint-2010-connector/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

par [Accenture](https://www.accenture.com)

Le connecteur SharePoint 2013 analyse le contenu de n’importe quelle URL d’une collection de sites SharePoint 2013. Il effectue des analyses incrémentielles à l’aide de l’horodatage du journal des modifications de SharePoint ou d’une base de données de captures instantanées, récupère les listes ACL, prend en charge les protocoles NTLM et HTTPS ainsi que les listes externes BCS, et s’exécute sans rien installer sur SharePoint.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2013+Connector)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2013"></a>SharePoint 2013

par [BA Insight](https://www.bainsight.com/)

Le connecteur SharePoint 2013 de BA Insight vous permet de vous connecter à SharePoint 2013 pour récupérer les données de n’importe quel site, bibliothèque de documents ou liste, et indexer ce contenu de manière sécurisée.

[En savoir plus](https://www.bainsight.com/connectors/sharepoint-2013-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

par [Accenture](https://www.accenture.com)

Le connecteur SharePoint 2016 analyse le contenu de n’importe quelle URL d’une collection de sites SharePoint 2016. Il effectue des analyses incrémentielles à l’aide de l’horodatage du journal des modifications de SharePoint ou d’une base de données de captures instantanées, récupère les listes ACL, prend en charge les protocoles NTLM et HTTPS ainsi que les listes externes BCS, et s’exécute sans rien installer sur SharePoint.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+2016+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-2016"></a>SharePoint 2016

par [BA Insight](https://www.bainsight.com/)

Le connecteur SharePoint de BA Insight vous permet de vous connecter à SharePoint 2016 pour récupérer les données de n’importe quel site, bibliothèque de documents ou liste, et indexer ce contenu de manière sécurisée.

[En savoir plus](https://www.bainsight.com/connectors/sharepoint-2016-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-2019"></a>SharePoint 2019

par [BA Insight](https://www.bainsight.com/)

Le connecteur SharePoint de BA Insight vous permet de vous connecter à SharePoint 2019 pour récupérer les données de n’importe quel site, bibliothèque de documents ou liste, et indexer ce contenu de manière sécurisée.

[En savoir plus](https://www.bainsight.com/connectors/connector-for-sharepoint-2019/)

:::column-end:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

par [Accenture](https://www.accenture.com)

Le connecteur SharePoint Online analyse le contenu de n’importe quelle URL d’une collection de sites SharePoint Online. Le connecteur récupère les sites, les listes, les dossiers, les éléments de liste et les pièces jointes ainsi que d’autres types de page (au format .aspx). Prend en charge l’exécution de SharePoint dans l’offre Microsoft O365.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/SharePoint+Online+Connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sharepoint-online"></a>SharePoint Online

par [BA Insight](https://www.bainsight.com/)

Le connecteur SharePoint Online de BA Insight vous permet de vous connecter à SharePoint Online pour récupérer les données de n’importe quel site, bibliothèque de documents ou liste, et indexer ce contenu de manière sécurisée.

[En savoir plus](https://www.bainsight.com/connectors/sharepoint-online-connector/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

par [BA Insight](https://www.bainsight.com/)

Le connecteur Sitecore respecte la sécurité du système source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/sitecore-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="sitecore"></a>Sitecore

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Sitecore, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les pages, les pièces jointes et d’autres types de document générés en quasi-temps réel. Le connecteur prend entièrement en charge le modèle d’autorisations de Sitecore ainsi que la gestion des utilisateurs et des groupes dans l’annuaire Active Directory associé.

[En savoir plus](https://www.raytion.com/connectors/raytion-sitecore-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="slack"></a>Slack

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Slack, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les messages, les threads et les fichiers partagés de tous les canaux publics de Slack en quasi-temps réel.

[En savoir plus](https://www.raytion.com/connectors/raytion-slack-connector)

:::column-end:::
:::column span="":::

---

### <a name="smb"></a>SMB

par [Accenture](https://www.accenture.com)

Le connecteur SMB récupère les fichiers et les répertoires sur les lecteurs partagés. Il prend en charge le système de fichiers DFS, la récupération des informations de sécurité et l’accès aux documents à des fins d’indexation sans changer la date du dernier accès.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/SMB+Connector)

:::column-end:::
:::column span="":::

---

### <a name="smb-file-share"></a>Partage de fichiers SMB

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de partages de fichiers SMB, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers et dossiers des partages de fichiers en quasi-temps réel. Le connecteur prend entièrement en charge la sécurité au niveau des documents des partages SMB ainsi que les dernières versions des protocoles SMB2 et SMB3.

[En savoir plus](https://www.raytion.com/connectors/raytion-smb-file-share-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="sql-database"></a>Base de données SQL

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de bases de données SQL, par exemple Microsoft SQL Server ou Oracle, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les enregistrements et les champs, notamment les documents binaires de bases de données SQL, en quasi-temps réel. Le connecteur prend en charge l’implémentation d’un modèle de sécurité personnalisé au niveau du document.

[En savoir plus](https://www.raytion.com/connectors/raytion-sql-database-connector)

:::column-end:::
:::column span="":::

---

### <a name="any-sql-based-crm-system"></a>Tout système CRM basé sur SQL

par [BA Insight](https://www.bainsight.com/)

Le connecteur SQL Server repose sur des méthodes d’accès aux bases de données conformes aux standards du secteur. Il peut donc prendre en charge de manière identique les bases de données d’autres systèmes tels qu’Oracle, MySQL et IBM DB2. Il respecte la sécurité de la base de données source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/sql-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="symantec-enterprise-vault"></a>Symantec Enterprise Vault

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Symantec Enterprise Vault, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les données archivées, par exemple les e-mails, les pièces jointes, les fichiers, les éléments de calendrier et les contacts d’Enterprise Vault, en quasi-temps réel. Le connecteur prend entièrement en charge les modèles d’authentification de base, NTLM et Kerberos de Symantec Enterprise Vault.

[En savoir plus](https://www.raytion.com/connectors/raytion-enterprise-vault-connector-2)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="twitter"></a>Twitter

par [Accenture](https://www.accenture.com)

Le connecteur Twitter analyse le contenu de n’importe quel compte Twitter. Il effectue des analyses complètes et incrémentielles tout en prenant en charge l’authentification basée sur le nom d’utilisateur, la clé de consommateur et la clé secrète de consommateur Twitter.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Twitter+Connector)

:::column-end:::
:::column span="":::

---

### <a name="veeva-vault"></a>Veeva Vault

par [BA Insight](https://www.bainsight.com/)

Le connecteur Veeva Vault de BA Insight indexe de manière sécurisée le texte intégral et les métadonnées des objets Veeva Vault dans Recherche cognitive Azure. Cela permet aux utilisateurs de récupérer un seul jeu de résultats pour le contenu situé dans Veeva Vault et Microsoft 365.

[En savoir plus](https://www.bainsight.com/connectors/veeva-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="veritas-enterprise-vault-symantec-evault"></a>Veritas Enterprise Vault (Symantec eVault)

par [BA Insight](https://www.bainsight.com/)

Le connecteur Veritas Enterprise Vault respecte la sécurité du système source, et fournit des analyses complètes et incrémentielles, ce qui permet aux utilisateurs de disposer à tout moment des informations les plus récentes.

[En savoir plus](https://www.bainsight.com/connectors/enterprise-vault-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="veritas-enterprise-vault"></a>Veritas Enterprise Vault

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Veritas Enterprise Vault, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les données archivées, par exemple les e-mails, les pièces jointes, les fichiers, les éléments de calendrier et les contacts d’Enterprise Vault, en quasi-temps réel. Le connecteur prend entièrement en charge les modèles d’authentification de base, NTLM et Kerberos de Veritas Enterprise Vault.

[En savoir plus](https://www.raytion.com/connectors/raytion-enterprise-vault-connector)

:::column-end:::
:::column span="":::
---

### <a name="website-crawler"></a>Website Crawler

par [BA Insight](https://www.bainsight.com/)

Le connecteur Website Crawler de BA Insight permet d’exposer le contenu de n’importe quel site web dans un seul index de recherche centralisé, avec le contenu d’autres dépôts.

[En savoir plus](https://www.bainsight.com/connectors/website-connector-for-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="west-km"></a>West km

par [BA Insight](https://www.bainsight.com/)

Le connecteur West km de BA Insight prend en charge la recherche parmi les documents de transaction et de contentieux, notamment la création de pages de résultats de la recherche personnalisés.

[En savoir plus](https://www.bainsight.com/connectors/westkm-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="windream-ecm-system"></a>windream ECM-System

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de windream ECM-System, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les fichiers et les dossiers, notamment les ensembles complets de métadonnées associées par windream ECM-System, en quasi-temps réel. Le connecteur prend entièrement en charge le modèle d’autorisations de windream ECM-System ainsi que la gestion des utilisateurs et des groupes dans l’annuaire Active Directory associé.

[En savoir plus](https://www.raytion.com/connectors/raytion-windream-ecm-system-connector)

:::column-end:::
:::column span="":::

---

### <a name="xerox-docushare"></a>Xerox DocuShare

par [BA Insight](https://www.bainsight.com/)

Les utilisateurs peuvent rechercher du contenu hébergé dans les dépôts DocuShare directement à partir de Recherche cognitive Azure, ce qui leur permet d’éviter d’avoir à effectuer plusieurs recherches pour localiser le contenu nécessaire.

[En savoir plus](https://www.bainsight.com/connectors/docushare-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::
---

### <a name="xerox-docushare"></a>Xerox DocuShare

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Xerox DocuShare, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les dépôts de données, les dossiers, les profils, les groupes et les fichiers de DocuShare en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Xerox DocuShare.

[En savoir plus](https://www.raytion.com/connectors/raytion-xerox-docushare-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

par [Accenture](https://www.accenture.com)

Le connecteur Yammer analyse le contenu des messages Yammer. Il récupère les messages par groupe, thread ou sujet, et obtient des informations relatives à l’utilisateur, au groupe et au thread.

[En savoir plus](https://contentanalytics.digital.accenture.com/display/aspire40/Yammer+Connector)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

par [BA Insight](https://www.bainsight.com/)

Le connecteur Yammer établit une connexion sécurisée à l’application Yammer et mappe le contenu, notamment les métadonnées et les pièces jointes, du schéma Yammer au schéma du moteur de recherche. Il extrait ensuite le contenu et l’envoie au moteur de recherche dans le cadre d’un processus appelé analyse.

[En savoir plus](https://www.bainsight.com/connectors/yammer-connector-sharepoint-azure-elasticsearch/)

:::column-end:::
:::column span="":::

---

### <a name="yammer"></a>Yammer

par [Raytion](https://www.raytion.com/contact)

Connecteur de recherche de contenu d’entreprise sécurisé permettant d’indexer de manière fiable du contenu provenant de Microsoft Yammer, et de l’interroger intelligemment avec Recherche cognitive Azure. Il indexe de manière robuste les canaux, les billets, les réponses, les pièces jointes, les sondages et les annonces de Yammer en quasi-temps réel. Le connecteur prend entièrement en charge la gestion intégrée des utilisateurs et des groupes de Microsoft Yammer et, en particulier, l’authentification fédérée avec Microsoft 365.

[En savoir plus](https://www.raytion.com/connectors/raytion-yammer-connector)

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::

:::row:::
:::column span="":::

---
':::column-end::': null
':::column span=""::': null
---

:::column-end:::
:::row-end:::
:::row:::
:::column span="":::

   :::column-end:::
   :::column span="":::
   :::column-end:::

:::row-end:::
