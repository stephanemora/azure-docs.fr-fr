---
title: Concepts de serveur - Azure Database pour MySQL
description: Cette rubrique propose des considérations et des instructions relatives à l’utilisation des serveurs de base de données Azure pour MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb8394de49c2c5daeae156a9316466928eded148
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628473"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Concepts de serveur dans une base de données Azure pour MySQL

Cet article présente des considérations et des instructions relatives à l’utilisation des serveurs Azure Database pour MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Qu’est-ce qu’un serveur de base de données Azure pour MySQL ?

Un serveur de base de données Azure pour MySQL est un point d’administration central pour plusieurs bases de données. Il s’agit de la structure de serveur MySQL que vous connaissez peut-être en local. Plus précisément, le service Azure Database pour MySQL est géré, fournit des garanties de performances et propose des fonctionnalités et des accès au niveau du serveur.

Un serveur de base de données Azure pour MySQL :

- est créé dans un abonnement Azure ;
- représente la ressource parente des bases de données ;
- fournit un espace de noms aux bases de données ;
- constitue un conteneur avec une sémantique à durée de vie longue (la suppression d’un serveur entraîne la suppression des bases de données autonomes qu’il contient) ;
- colocalise les ressources d’une région ;
- fournit un point de terminaison de connexion pour l’accès au serveur et aux bases de données ;
- fournit l’étendue des stratégies de gestion qui s’appliquent à ses bases de données : connexions, pare-feu, utilisateurs, rôles, configurations, etc. ;
- est disponible dans plusieurs versions (pour plus d’informations, consultez la page [Versions prises en charge des bases de données Azure pour MySQL](./concepts-supported-versions.md)) ;

Dans un serveur Azure Database pour MySQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Les tarifs sont structurés par serveur, en fonction de la configuration du niveau tarifaire, des vCores et du stockage (Go). Pour plus d’informations, consultez [Niveaux tarifaires](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Comment se connecter et s’authentifier auprès d’un serveur de base de données Azure pour MySQL ?

Les éléments suivants permettent de garantir un accès sécurisé à votre base de données.

| Concept de sécurité | Description     |
| :-- | :-- |
| **Authentification et autorisation** | Le serveur de base de données Azure pour MySQL prend en charge l’authentification MySQL native. Vous pouvez vous connecter et vous authentifier auprès d’un serveur avec les informations de connexion d’administrateur du serveur. |
| **Protocole** | Le service prend en charge un protocole par messages utilisé par MySQL. |
| **TCP/IP** | Le protocole est pris en charge via TCP/IP et des sockets du domaine Unix. |
| **Pare-feu** | Pour aider à protéger vos données, une règle de pare-feu empêche tout accès à votre serveur de base de données tant que vous ne spécifiez pas les ordinateurs autorisés. Consultez la page [Règles de pare-feu d’un serveur de base de données Azure pour MySQL](./concepts-firewall-rules.md). |
| **SSL** | Le service prend en charge l’application des connexions SSL entre vos applications et votre serveur de base de données.  Consultez la page [Configurer la connectivité SSL dans une application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mysql"></a>Arrêt/Démarrage d’Azure Database pour MySQL

Azure Database pour MySQL vous donne la possibilité d’**arrêter** le serveur si vous ne l’utilisez pas, et de le **démarrer** lorsque vous reprenez l’activité. Cela permet essentiellement de réduire les coûts sur les serveurs de base de données et de payer uniquement la ressource en cours d’utilisation. Cela devient encore plus important pour les charges de travail de développement et de test, et lorsque vous utilisez uniquement le serveur pour une partie de la journée. Lorsque vous arrêtez le serveur, toutes les connexions actives sont supprimées. Plus tard, lorsque vous souhaiterez remettre le serveur en ligne, vous pourrez utiliser le [portail Azure](how-to-stop-start-server.md) ou l’interface [CLI](how-to-stop-start-server.md).

Lorsque le serveur se trouve à l’état **Arrêté**, le calcul du serveur n’est pas facturé. Toutefois, le stockage continue à être facturé tant que le stockage du serveur est conservé pour s’assurer que les fichiers de données sont disponibles lors du redémarrage du serveur.

> [!IMPORTANT]
> Lorsque vous **arrêtez** le serveur, il reste dans cet état durant les 7 jours suivants. Si vous ne le **démarrez** pas manuellement pendant cette période, le serveur sera automatiquement démarré à la fin des 7 jours. Vous pouvez choisir de l’**arrêter** de nouveau si vous n’utilisez pas le serveur.

Pendant l’arrêt du serveur, aucune opération de gestion ne peut être effectuée sur le serveur. Afin de modifier les paramètres de configuration sur le serveur, vous devez [démarrer le serveur](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Limitations de l’opération Arrêter/Démarrer
- Pas de prise en charge avec les configurations de réplica en lecture (source et réplicas).

## <a name="how-do-i-manage-a-server"></a>Comment gérer un serveur ?

Vous pouvez gérer la création, la suppression, la configuration des paramètres du serveur (my.cnf), la mise à l’échelle, la mise en réseau, la sécurité, la haute disponibilité, la sauvegarde et la restauration, ainsi que la surveillance de vos serveurs Azure Database pour MySQL à l’aide du portail Azure ou d’Azure CLI. En outre, les procédures stockées suivantes disponibles dans Azure Database pour MySQL permettent d’effectuer certaines tâches d’administration de base de données requises parce que le privilège de SUPER utilisateur n’est pas pris en charge sur le serveur.

|**Nom de la procédure stockée**|**Paramètres d’entrée**|**Paramètres de sortie**|**Remarque sur l’utilisation**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/A|Équivaut à la commande [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Met fin à la connexion associée au processlist_id fourni après avoir mis fin à l’exécution de toute instruction exécutée actuellement par la connexion.|
|*mysql.az_kill_query*|processlist_id|N/A|Équivaut à la commande [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Met fin à l’instruction exécutée actuellement par la connexion. Laisse la connexion active.|
|*mysql.az_load_timezone*|N/A|N/A|Charge les [tables de fuseau horaire](howto-server-parameters.md#working-with-the-time-zone-parameter) pour permettre d’attribuer des valeurs nommées au paramètre `time_zone` (par exemple, « US/Pacific »).|

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une vue d’ensemble du service à la page [Vue d’ensemble de la base de données Azure pour MySQL](./overview.md).
- Pour plus d’informations sur des quotas de ressources spécifiques et les limitations associées à votre **niveau tarifaire**, consultez [Niveaux tarifaires](./concepts-pricing-tiers.md).
- Pour plus d’informations sur la connexion au service, consultez la page [Bibliothèques de connexions de la base de données Azure pour MySQL](./concepts-connection-libraries.md).
