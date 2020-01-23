---
title: Authentication
description: Découvrez comment vous authentifier sur Azure SQL Data Warehouse à l’aide d’Azure Active Directory (AAD) ou de l’authentification SQL Server.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 706c68cb7a139a5c4f6def5aed7ad67e49090ede
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545141"
---
# <a name="authenticate-to-azure-sql-data-warehouse"></a>S’authentifier sur Azure SQL Data Warehouse
Découvrez comment vous authentifier sur Azure SQL Data Warehouse à l’aide d’Azure Active Directory (AAD) ou de l’authentification SQL Server.

Pour vous connecter à SQL Data Warehouse, vous devez transmettre des informations d’identification de sécurité à des fins d’authentification. Lors de l’établissement d’une connexion, certains paramètres de connexion sont configurés dans le cadre de l’établissement de votre session de requête.  

Vous pouvez consulter l’article [Sécuriser une base de données dans SQL Data Warehouse](sql-data-warehouse-overview-manage-security.md)pour plus d’informations sur la sécurité et la manière d’activer des connexions à votre entrepôt de données.

## <a name="sql-authentication"></a>Authentification SQL
Pour vous connecter à SQL Data Warehouse, vous devez fournir les informations suivantes :

* Nom complet du serveur
* Authentification SQL
* Nom d’utilisateur
* Mot de passe
* Base de données par défaut (facultatif)

Par défaut, votre connexion se connecte à la base de données *MASTER* et non à votre base de données utilisateur. Pour vous connecter à votre base de données utilisateur, vous pouvez choisir d’effectuer l’une des deux opérations suivantes :

* Spécifiez la base de données par défaut lors de l’inscription de votre serveur auprès de l’Explorateur d’objets SQL Server dans SQL Server Data Tools (SSDT), SSMS, ou dans votre chaîne de connexion d’application. Par exemple, insérez le paramètre InitialCatalog pour une connexion ODBC.
* Sélectionnez la base de données utilisateur avant de créer une session dans SSDT.

> [!NOTE]
> L’instruction Transact-SQL **USE MyDatabase;** n’est pas prise en charge pour la modification de la base de données pour une connexion. Pour obtenir des conseils en matière de connexion à SQL Data Warehouse avec SSDT, consultez l’article [Envoyer des requêtes avec Visual Studio](sql-data-warehouse-query-visual-studio.md) .
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Authentification Azure Active Directory (AAD)
L’[authentification Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) est un mécanisme servant à se connecter à Microsoft Azure SQL Data Warehouse à l’aide d’identités dans Azure Active Directory (Azure AD). Avec l’authentification Azure Active Directory, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs SQL Data Warehouse et elle simplifie la gestion des autorisations. 

### <a name="benefits"></a>Avantages
Les avantages d’Azure Active Directory incluent :

* Fournit une alternative à l’authentification SQL Server.
* Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
* Permet la rotation de mot de passe à un emplacement unique
* Gérer les autorisations de base de données à l’aide de groupes (AAD) externes.
* Élimine le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
* Utilise les utilisateurs de base de données autonome pour authentifier les identités au niveau de la base de données.
* Prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Data Warehouse.
* Prend en charge l'authentification multifacteur via l'authentification universelle Active Directory pour divers outils, notamment [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) et [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory est encore relativement nouveau et présente certaines limitations. Pour vous assurer qu’Azure Active Directory est adapté à votre environnement, consultez [Limitations et fonctionnalités Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), en particulier les considérations supplémentaires.
> 
> 

### <a name="configuration-steps"></a>Configuration
Suivez ces étapes pour configurer l’authentification Azure Active Directory.

1. Créer et renseigner un répertoire Azure Active Directory
2. Facultatif : associez ou modifiez l’annuaire Active Directory actuellement associé à votre abonnement Azure
3. Créer un administrateur Azure Active Directory pour Azure SQL Data Warehouse
4. Configurer vos ordinateurs clients
5. Créer des utilisateurs de base de données autonome dans votre base de données mappés sur les identités Azure AD
6. Se connecter à l’entrepôt de données à l’aide des identités Azure AD

Actuellement, les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Comme solution de contournement, vous pouvez afficher les utilisateurs dans [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Rechercher des informations détaillées
* Les étapes de configuration et d’utilisation de l’authentification Azure Active Directory sont presque identiques pour Azure SQL Database et Azure SQL Data Warehouse. Suivez les étapes détaillées dans la rubrique [Connexion à SQL Database ou SQL Data Warehouse avec l’authentification Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Créez des rôles de base de données personnalisés et ajoutez des utilisateurs aux rôles. Accordez ensuite des autorisations granulaires aux rôles. Pour plus d’informations, consultez [Prise en main des autorisations du moteur de base de données](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Étapes suivantes
Pour commencer à interroger votre entrepôt de données avec Visual Studio et d’autres applications, consultez [Soumettre des requêtes avec Visual Studio](sql-data-warehouse-query-visual-studio.md).
