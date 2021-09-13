---
title: Règles de pare-feu dans Azure Database pour PostgreSQL - Serveur flexible
description: Cet article explique comment utiliser des règles de pare-feu pour se connecter à Azure Database pour PostgreSQL - Serveur flexible avec option de déploiement d’un réseau public.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: bdda8163bacd596eafab2a9b2d10d914012a3efb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866867"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Règles de pare-feu dans Azure Database pour PostgreSQL - Serveur flexible
Quand vous utilisez Azure Database pour PostgreSQL - Serveur flexible, vous avez deux options de réseau principales. Il s’agit de l’accès privé (intégration au réseau virtuel) et de l’accès public (adresses IP autorisées). 

Avec l’accès public, le serveur Azure Database pour PostgreSQL est accessible via un point de terminaison public. Par défaut, le pare-feu bloque tout accès au serveur. Pour spécifier les hôtes IP qui peuvent accéder au serveur, vous devez créer des *règles de pare-feu* au niveau du serveur. Les règles de pare-feu spécifient les plages d’adresses IP publiques autorisées. Le pare-feu octroie l’accès au serveur en fonction de l’adresse IP d’origine de chaque demande.

Vous pouvez créer des règles de pare-feu avec le Portail Azure ou des commandes d’Azure CLI. Vous devez être le propriétaire ou un contributeur de l’abonnement.

Les règles de pare-feu au niveau du serveur s’appliquent à toutes les bases de données qui se trouvent sur le même serveur Azure Database pour PostgreSQL. Les règles n’affectent pas l’accès au site web du portail Azure.

Le schéma suivant montre comment les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu pour pouvoir atteindre les bases de données PostgreSQL :

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Schéma montrant une vue d’ensemble du fonctionnement du pare-feu.":::

## <a name="connect-from-the-internet"></a>Connexion à partir d’Internet
Si l’adresse IP source de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée. Dans le cas contraire, elle est rejetée. 

Par exemple, si votre application se connecte avec un pilote JDBC pour PostgreSQL, il se peut que vous rencontriez cette erreur, car le pare-feu bloque la connexion :

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connect-from-azure"></a>Se connecter à partir d’Azure
Nous vous conseillons de trouver l’adresse IP sortante d’une application ou d’un service et d’autoriser explicitement l’accès à ces plages ou adresses IP individuelles. Par exemple, vous pouvez retrouver l’adresse IP sortante d’une application Azure App Service ou bien utiliser une adresse IP publique liée à une machine virtuelle. 

Si aucune adresse IP sortante fixe n’est disponible pour votre service Azure, vous pouvez envisager d’autoriser les connexions depuis toutes les adresses IP pour les centres de données Azure :

1. Dans le portail Azure, dans le volet **Réseau**, activez la case à cocher **Autoriser l’accès public à partir de n’importe quel service Azure dans Azure sur ce serveur**. 
1. Sélectionnez **Enregistrer**. 

> [!IMPORTANT]
> L’option **Autoriser l’accès public à partir de n’importe quel service Azure dans Azure sur ce serveur** permet de configurer le pare-feu afin d’autoriser toutes les connexions en provenance d’Azure, notamment les connexions à partir des abonnements d’autres clients. Lorsque vous utilisez cette option, assurez-vous que vos autorisations de connexion et d’utilisation limitent l’accès aux seuls utilisateurs autorisés. 

:::image type="content" source="./media/concepts-firewall-rules/allow-public-access.png" alt-text="Capture d’écran montrant les sélections permettant d’autoriser l’accès aux services Azure dans le portail.":::

## <a name="programmatically-manage-firewall-rules"></a>Gestion par programmation des règles de pare-feu
En dehors du Portail Azure, vous pouvez gérer les règles de pare-feu par programmation à l’aide d’Azure CLI. 

À partir d’Azure CLI, un paramètre de règle de pare-feu avec une adresse de début et de fin égale à 0.0.0.0 équivaut à l’option **Autoriser l’accès public à partir de n’importe quel service Azure dans Azure sur ce serveur** dans le portail. Si les règles du pare-feu rejettent la tentative de connexion, l’application n’atteint pas le serveur Azure Database pour PostgreSQL.

## <a name="troubleshoot-firewall-problems"></a>Résoudre les problèmes de pare-feu
Considérez les possibilités suivantes lorsque l’accès à un serveur Azure Database pour PostgreSQL ne se comporte pas comme vous le souhaitez :

* **Les modifications apportées à la liste d’autorisation n’ont pas encore pris effet** : les modifications apportées à la configuration du pare-feu d’un serveur Azure Database pour PostgreSQL peuvent prendre jusqu’à cinq minutes.

* **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé** : si une connexion n’a pas d’autorisations sur le serveur de base de données Azure pour PostgreSQL ou que le mot de passe est incorrect, la connexion au serveur est refusée. La création d’un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur. Chaque client doit quand même fournir les informations d’identification de sécurité nécessaires.

  Par exemple, l’erreur suivante peut s’afficher si l’authentification échoue pour un client JDBC :

  > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **Le pare-feu n’autorise pas les adresses IP dynamiques :** si vous avez une connexion Internet avec adressage IP dynamique et que le pare-feu demeure infranchissable, essayez l’une des solutions suivantes :

  * Demandez à votre fournisseur d’accès à Internet la plage d’adresses IP attribuée à vos ordinateurs clients qui accèdent au serveur Azure Database pour PostgreSQL. Ajoutez ensuite cette plage dans une règle de pare-feu.

  * Obtenez des adresses IP statiques pour vos ordinateurs clients, puis ajoutez ces adresses IP statiques en tant que règle de pare-feu.

* **Les règles du pare-feu ne sont pas disponible pour le format IPv6** : les règles de pare-feu doivent être au format IPv4. Si vous spécifiez des règles de pare-feu au format IPv6, vous obtiendrez une erreur de validation.


## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer des règles de pare-feu d’Azure Database pour PostgreSQL à l’aide du portail Azure](how-to-manage-firewall-portal.md)
* [Créer et gérer des règles de pare-feu d’Azure Database pour PostgreSQL à l’aide d’Azure CLI](how-to-manage-firewall-cli.md)
