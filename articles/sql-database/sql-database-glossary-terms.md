---
title: Lexique Azure SQL Database | Microsoft Docs
description: Lexique Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/26/2019
ms.openlocfilehash: d940cfa91890d26a48b682a6eb09410b3b7de045
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568080"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Lexique Azure SQL Database

|Context|Terme|Plus d’informations|
|:---|:---|:---|
|Nom du service Azure|Azure SQL Database ou SQL Database|[Le service Azure SQL Database](sql-database-technical-overview.md)|
|Niveau de calcul|Serverless (préversion)|[Couche de calcul serverless](sql-database-serverless.md)
||approvisionné|[Couche de calcul serverless](sql-database-serverless.md)
|Options de déploiement |Base de données unique|[Bases de données uniques](sql-database-single-database.md)|
||Pool élastique|[Pool élastique](sql-database-elastic-pool.md)|
||Instance gérée|[instance gérée](sql-database-managed-instance.md)|
|Objets serveur|Serveur Azure SQL Database ou serveur de base de données|[Serveur de base de données](sql-database-servers.md)|
||Serveur SQL Database Managed Instance, serveur Managed Instance ou instance de serveur|[instance gérée](sql-database-managed-instance.md)|
Objets de base de données|Base de données Azure SQL|Toute base de données dans Azure SQL Database|
||Base de données unique|Base de données créée à l’aide de l’option de déploiement de base de données unique|
||Base de données mise en pool|Base de données créée au sein d’un pool élastique ou déplacée vers un tel pool|
||Base de données d’instance|Base de données créée dans une instance gérée|
||Base de données de base|Base de données créée au niveau de service de base du modèle d’achat DTU ou déplacée vers ce niveau|
||Base de données standard|Base de données créée au niveau de service standard du modèle d’achat DTU ou déplacée vers ce niveau|
||Base de données premium|Base de données créée au niveau de service premium du modèle d’achat DTU ou déplacée vers ce niveau|
||Base de données à usage général|Base de données créée au niveau de service Usage général du modèle d’achat vCore ou déplacée vers ce niveau|
||Base de données Hyperscale|Base de données créée au niveau de service Hyperscale du modèle d’achat vCore ou déplacée vers ce niveau|
||Base de données critique pour l’entreprise|Base de données créée au niveau de service Critique pour l’entreprise du modèle d’achat vCore ou déplacée vers ce niveau|
||Base de données configurée|Base de données configurée dans le niveau de calcul provisionné|
|[Modèles et ressources d’achat](sql-database-purchase-models.md)|Modèle d’achat DTU|[Modèle d’achat DTU](sql-database-service-tiers-dtu.md)|
||Modèle d’achat vCore|[Modèle d’achat vCore](sql-database-service-tiers-vcore.md)|
||vCore|Cœur fourni pour le système d’exploitation invité par l’hyperviseur|
||Niveau de service|Niveau de service au sein d’un modèle d’achat|
||Taille de calcul|Quantité de ressources de calcul pour une base de données unique, un pool élastique ou une instance gérée à l’intérieur d’un niveau de service|
||Quantité de stockage|Quantité de stockage disponible pour une base de données unique, un pool élastique ou une instance gérée|
||Génération de calcul|Génération de processeur à l’intérieur d’un niveau de service|
|Règles de pare-feu IP du serveur de base de données|Règles de pare-feu IP|[Règles de pare-feu IP](sql-database-firewall-configure.md)|
||Règles de pare-feu IP au niveau du serveur|[Règles de pare-feu IP au niveau du serveur](sql-database-firewall-configure.md#overview)|
|| Règles de pare-feu IP au niveau de la base de données|[Règles de pare-feu IP au niveau de la base de données](sql-database-firewall-configure.md#overview)|
||Règles et points de terminaison du réseau virtuel|[Règles et points de terminaison du réseau virtuel](sql-database-vnet-service-endpoint-rule-overview.md)|
