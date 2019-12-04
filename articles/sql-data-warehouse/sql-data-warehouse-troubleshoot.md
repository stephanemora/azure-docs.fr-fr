---
title: Résolution de problèmes
description: Résolution des problèmes d’Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/25/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b2a9a7b0b759f5853d83a4b1999887414fd5f430
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483207"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Résolution des problèmes d’Azure SQL Data Warehouse
Cet article répertorie les questions de résolution de problèmes courantes.

## <a name="connecting"></a>Connexion
| Problème                                                        | Résolution :                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Échec de connexion pour l’utilisateur 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, erreur : 18456) | Cette erreur se produit lorsqu’un utilisateur AAD tente de se connecter à la base de données master alors qu’elle ne contient pas d’utilisateur.  Pour corriger ce problème, spécifiez le SQL Data Warehouse auquel vous souhaitez vous connecter ou ajoutez l’utilisateur à la base de données master.  Consultez l’article [Présentation de la sécurité][Security overview] pour plus de détails. |
| Le serveur principal « MyUserName » n’est pas en mesure d’accéder à la base de données « master » dans le contexte de sécurité actuel. La base de données utilisateur par défaut ne peut pas être ouverte. La connexion a échoué. Échec de la connexion pour l'utilisateur 'MyUserName'. (Microsoft SQL Server, erreur : 916) | Cette erreur se produit lorsqu’un utilisateur AAD tente de se connecter à la base de données master alors qu’elle ne contient pas d’utilisateur.  Pour corriger ce problème, spécifiez le SQL Data Warehouse auquel vous souhaitez vous connecter ou ajoutez l’utilisateur à la base de données master.  Consultez l’article [Présentation de la sécurité][Security overview] pour plus de détails. |
| Erreur CTAIP                                                  | Cette erreur peut se produire lorsqu’un identifiant de connexion a été créé sur la base de données principale du serveur SQL, mais pas sur la base de données SQL Data Warehouse.  Si vous rencontrez cette erreur, consultez l’article [Vue d’ensemble de la sécurité][Security overview] .  Cet article explique comment créer un identifiant et un utilisateur sur la base de données master, puis comment créer un utilisateur dans la base de données SQL Data Warehouse. |
| Bloqué par le pare-feu                                          | Les bases de données Azure SQL sont protégées par des pare-feu au niveau du serveur et de la base de données pour s’assurer que seules les adresses IP connues ont accès à une base de données. Les pare-feu sont sécurisés par défaut, ce qui signifie que vous devez activer explicitement une adresse IP ou une plage d’adresses avant de vous connecter.  Pour configurer votre pare-feu pour l’accès, suivez les étapes décrites dans la section [Configuration de l’accès au pare-feu du serveur pour l’adresse IP de votre client][Configure server firewall access for your client IP] dans les [Instructions d’approvisionnement][Provisioning instructions]. |
| Connexion impossible avec l’outil ou le pilote                           | Avec SQL Data Warehouse, il est recommandé d’utiliser [SSMS][SSMS], [SSDT pour Visual Studio][SSDT for Visual Studio] ou [sqlcmd][sqlcmd] pour interroger vos données. Pour plus d’informations sur les pilotes et la connexion à SQL Data Warehouse, consultez les articles [Pilotes pour Azure SQL Data Warehouse][Drivers for Azure SQL Data Warehouse] et [Connexion à Azure SQL Data Warehouse][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Outils
| Problème                                                        | Résolution :                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Des utilisateurs Azure Active Directory sont manquants dans l’explorateur d’objets Visual Studio           | Il s'agit d'un problème connu.  Comme solution de contournement, vous pouvez afficher les utilisateurs dans [sys.database_principals][sys.database_principals].  Consultez la page [Authentification sur Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] pour en savoir plus sur l’utilisation d’Azure Active Directory avec SQL Data Warehouse. |
| Les scripts manuels, l’utilisation de l’Assistant Création de scripts ou la connexion via SSMS sont lents, ne répondent pas ou produisent des erreurs | Vérifiez que les utilisateurs ont été créés dans la base de données master. Dans les options de script, vérifiez aussi que l’édition du moteur est définie en tant que « Édition Microsoft Azure SQL Data Warehouse » et que le type de moteur est « Microsoft Azure SQL Database ». |
| La génération de scripts échoue dans SSMS                               | La génération d’un script pour SQL Data Warehouse échoue si l’option « Générer un script pour les objets dépendants » a la valeur « True ». Pour résoudre ce problème, les utilisateurs doivent accéder manuellement à Outils -> Options -> Explorateur d’objets SQL Server -> Générer un script pour les options dépendants et affecter la valeur false à cette option. |

## <a name="performance"></a>Performances
| Problème                                                        | Résolution :                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Résolution des problèmes de performances des requêtes                            | Si vous tentez de dépanner une requête spécifique, commencez avec l’article relatif à la [surveillance de vos requêtes][Learning how to monitor your queries]. |
| Problèmes d’espaces TempDB | [Surveillez l’utilisation de l’espace TempDB](sql-data-warehouse-manage-monitor.md#monitor-tempdb).  Les causes courantes de l’insuffisance d’espace TempDB sont les suivantes :<br>- Les ressources allouées à la requête ne sont pas suffisantes et les données sont propagées dans TempDB.  Consultez [Gestion des charges de travail](resource-classes-for-workload-management.md) <br>- Les statistiques sont manquantes ou obsolètes, ce qui entraîne un déplacement excessif des données.  Consultez la section[Maintenance des statistiques de table][Statistics] pour plus d’informations sur la création de statistiques<br>- L’espace TempDB est alloué par niveau de service.  [La mise à l’échelle de votre base de données de l'entrepôt de données SQL][Scaling your SQL Data Warehouse] à un paramètre DWU plus élevé alloue plus d’espace TempDB.|
| Des performances des requêtes et des plans médiocres sont souvent le résultat de statistiques manquantes | La cause la plus courante de la médiocrité des performances est le manque de statistiques concernant vos tables.  Consultez la section[Maintenance des statistiques de table][Statistics] pour plus d’informations sur la création de statistiques et les raisons pour lesquelles elles sont essentielles à vos performances. |
| Concurrence faible / requêtes en file d’attente                             | Comprendre la [gestion des charges de travail][Workload management] est important pour comprendre comment équilibrer l’allocation de mémoire avec l’accès concurrentiel. |
| Comment mettre en œuvre les meilleures pratiques                              | L’article [Meilleures pratiques relatives à SQL Data Warehouse][SQL Data Warehouse best practices] est le point de départ idéal pour apprendre à améliorer les performances des requêtes. |
| Comment améliorer les performances avec la mise à l’échelle                      | Pour améliorer les performances, il suffit parfois simplement d’ajouter davantage de puissance de calcul à vos requêtes en procédant à une [mise à l’échelle de votre base de données SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Performances de requêtes médiocres en raison de la qualité médiocre de l’index     | Parfois, les requêtes peuvent ralentir en raison de la [qualité médiocre des index columnstore][Poor columnstore index quality].  Consultez cet article pour plus d’informations et pour découvrir comment [Reconstruire des index pour améliorer la qualité de segment][Rebuild indexes to improve segment quality]. |

## <a name="system-management"></a>Gestion de systèmes
| Problème                                                        | Résolution :                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847 : Impossible d’effectuer l’opération car le serveur risque de dépasser le quota d’unité de transaction de base de données autorisé de 45 000. | Réduisez la [DWU][DWU] de la base de données que vous essayez de créer ou [demandez une augmentation du quota][request a quota increase]. |
| Examen de l’utilisation de l’espace                              | Consultez la section relative aux [tailles des tables][Table sizes] pour comprendre l’utilisation de l’espace de votre système. |
| Aide concernant la gestion des tables                                    | Consultez l’article [Vue d’ensemble des tables][Overview] pour obtenir de l’aide concernant la gestion de vos tables.  Cet article inclut également des liens vers des rubriques plus détaillées, notamment [Types de données de table][Data types], [Distribution d’une table][Distribute], [Indexation d’une table][Index], [Partitionnement d’une table][Partition], [Maintenance des statistiques de table][Statistics] et [Tables temporaires][Temporary]. |
| La barre de progression de TDE (Transparent Data Encryption) n’est pas mise à jour dans le portail Microsoft Azure | Vous pouvez afficher l’état de chiffrement de TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |


## <a name="differences-from-sql-database"></a>Différences par rapport à la base de données SQL
| Problème                                 | Résolution :                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Fonctionnalités de base de données SQL non prises en charge     | Voir [Fonctionnalités de tables non prises en charge][Unsupported table features]. |
| Types de données de base de données SQL non pris en charge   | Voir [Types de données non pris en charge][Unsupported data types].        |
| Limitations DELETE et UPDATE         | Consultez les rubriques [Solutions de contournement UPDATE][UPDATE workarounds], [Solutions de contournement DELETE][DELETE workarounds] et [Utilisation de CTAS pour contourner les syntaxes UPDATE et DELETE non prises en charge][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| Instruction MERGE non prise en charge      | Consultez la rubrique [Solutions de contournement MERGE][MERGE workarounds].                  |
| Limitations des procédures stockées          | Consultez [Limitations des procédures stockées][Stored procedure limitations] pour comprendre certaines limitations des procédures stockées. |
| Les fonctions définies par l’utilisateur ne prennent pas en charge les instructions SELECT | Il s’agit d’une limitation actuelle de nos fonctions définies par l’utilisateur.  Consultez [CREATE FUNCTION][CREATE FUNCTION] pour connaître la syntaxe que nous prenons en charge. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une aide supplémentaire lors de la recherche d’une solution à votre problème, voici d’autres ressources auxquelles vous pouvez recourir.

* [Blogs]
* [Demandes de fonctionnalités]
* [Vidéos]
* [Blogs de l’équipe CAT (Customer Advisory Team)]
* [Création d’un ticket de support]
* [Forum MSDN]
* [Forum Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Création d’un ticket de support]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe CAT (Customer Advisory Team)]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
