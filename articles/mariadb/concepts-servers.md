---
title: Concepts de serveur dans Azure Database for MariaDB
description: Cette rubrique comporte des considérations et des instructions relatives à l’utilisation des serveurs Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cf57acdcbcfa792a6c5ab62c6e8ec0589d625df7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994584"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Concepts de serveur dans Azure Database for MariaDB
Cet article comporte des considérations et des instructions relatives à l’utilisation des serveurs Azure Database for MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Qu’est-ce qu’un serveur Azure Database for MariaDB ?

Un serveur Azure Database for MariaDB est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur MariaDB que vous connaissez peut-être en local. Plus précisément, le service Azure Database for MariaDB est géré, fournit des garanties de performances et propose des fonctionnalités et un accès au niveau du serveur.

Un serveur Azure Database for MariaDB :

- est créé dans un abonnement Azure ;
- représente la ressource parente des bases de données ;
- fournit un espace de noms aux bases de données ;
- constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données autonomes qu’il contient) ;
- colocalise les ressources d’une région ;
- fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données ;
- fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc. ;
- est disponible dans le moteur MariaDB version 10.2. Pour plus d’informations, consultez [Versions prises en charge des bases de données Azure Database for MariaDB](./concepts-supported-versions.md).

Dans un serveur Azure Database for MariaDB, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Les tarifs sont structurés par serveur, en fonction de la configuration du niveau tarifaire, des vCores et du stockage (Go). Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Comment sécuriser un serveur Azure Database for MariaDB ?

Les éléments suivants permettent de garantir un accès sécurisé à votre base de données.
|||
| :--| :--|
| **Authentification et autorisation** | Le serveur Azure Database for MariaDB prend en charge l’authentification MySQL native. Vous pouvez vous connecter et vous authentifier auprès d’un serveur avec les informations de connexion d’administrateur du serveur. |
| **Protocole** | Le service prend en charge un protocole par messages utilisé par MySQL. |
| **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. |
| **Pare-feu** | Pour aider à protéger vos données, une règle de pare-feu empêche tout accès à votre serveur de base de données tant que vous ne spécifiez pas les ordinateurs autorisés. Consultez [Règles de pare-feu d’un serveur Azure Database for MariaDB](./concepts-firewall-rules.md). |
| **SSL** | Le service prend en charge l’application des connexions SSL entre vos applications et votre serveur de base de données.  <!--See [Configure SSL connectivity in your application to securely connect to Azure Database for MariaDB](./howto-configure-ssl.md).--> |

## <a name="how-do-i-manage-a-server"></a>Comment gérer un serveur ?
Vous pouvez gérer des serveurs Azure Database for MariaDB à l’aide du portail Azure ou de l’interface Azure CLI.

## <a name="next-steps"></a>Étapes suivantes
- Vous trouverez une vue d’ensemble du service dans la page [Vue d’ensemble d’Azure Database for MariaDB](./overview.md).
- Pour plus d’informations sur des quotas de ressources spécifiques et les limitations associées à votre **niveau de service**, consultez [Niveaux de service](./concepts-pricing-tiers.md)
<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
