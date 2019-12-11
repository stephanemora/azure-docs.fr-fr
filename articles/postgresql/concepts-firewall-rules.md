---
title: Règles de pare-feu - Azure Database pour PostgreSQL - Serveur unique
description: Cet article explique comment utiliser des règles de pare-feu pour se connecter à Azure Database pour PostgreSQL - Serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 28c8bccaf6be49b7220a32c781b79f106ad86e52
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768637"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Règles de pare-feu dans Azure Database pour PostgreSQL - Serveur unique
Le pare-feu du serveur Azure Database pour PostgreSQL empêche tout accès à votre serveur de base de données jusqu’à ce que vous spécifiiez les ordinateurs qui disposent d’autorisations. Le pare-feu octroie l’accès au serveur en fonction de l’adresse IP d’origine de chaque demande.
Pour configurer votre pare-feu, vous créez des règles de pare-feu qui spécifient les plages d’adresses IP acceptables. Vous pouvez créer des règles de pare-feu au niveau du serveur.

**Règles de pare-feu :** Ces règles permettent aux clients d’accéder à l’ensemble de votre serveur Azure Database pour PostgreSQL, c’est-à-dire à toutes les bases de données qui se trouvent sur le même serveur logique. Les règles de pare-feu au niveau du serveur peuvent être configurées en utilisant le Portail Azure ou des commandes d’Azure CLI. Pour créer des règles de pare-feu au niveau du serveur, vous devez être le propriétaire de l’abonnement ou collaborateur.

## <a name="firewall-overview"></a>Présentation du pare-feu
Par défaut, tous les accès au serveur de base de données Azure pour PostgreSQL sont bloqués par le pare-feu. Pour pouvoir utiliser votre serveur à partir d’un autre ordinateur, vous devez spécifier une ou plusieurs règles de pare-feu au niveau du serveur afin de permettre l’accès à votre serveur. Utilisez les règles de pare-feu pour spécifier les plages d’adresses IP d’Internet à autoriser. L’accès au site web du Portail Azure proprement dit n’est pas affecté par les règles de pare-feu.
Les tentatives de connexion à partir d’Internet et d’Azure doivent franchir le pare-feu pour pouvoir atteindre votre base de données PostgreSQL, comme l’illustre le diagramme suivant :

![Exemple de flux de fonctionnement du pare-feu](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Connexion à partir d’Internet
Les règles de pare-feu au niveau du serveur s’appliquent à toutes les bases de données qui se trouvent sur le même serveur Azure Database pour PostgreSQL. Si l’adresse IP de la demande appartient à une des plages spécifiées dans les règles de pare-feu au niveau du serveur, la connexion est accordée.
Si l’adresse IP de la demande n’appartient pas aux plages spécifiées dans une règle de pare-feu au niveau du serveur, la demande de connexion échoue.
Par exemple, si votre application se connecte au pilote JDBC pour PostgreSQL, il se peut que vous rencontriez cette erreur au cours de la tentative de connexion, si le pare-feu la bloque.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Connexion à partir d’Azure
Pour autoriser des applications d’Azure à se connecter à votre serveur Azure Database pour PostgreSQL, les connexions Azure doivent être activées. Par exemple, pour héberger une application Azure Web Apps ou une application qui s’exécute sur une machine virtuelle Azure, ou pour vous connecter à partir d’une passerelle de gestion des données Azure Data Factory. Les ressources ne doivent pas obligatoirement se trouver sur le même réseau virtuel (VNET) ou dans le même groupe de ressources pour que la règle de pare-feu autorise ces connexions. Quand une application à partir d’Azure tente de se connecter à votre serveur de base de données, le pare-feu vérifie que les connexions Azure sont autorisées. Il existe deux méthodes pour activer ces types de connexions. Un paramètre de pare-feu avec des adresses de début et de fin égales à 0.0.0.0 indique que ces connexions sont autorisées. Vous pouvez également affecter la valeur **ON** à l’option **Autoriser l’accès aux services Azure** dans le portail à partir du volet **Sécurité de la connexion** et cliquer sur **Enregistrer**. Si la tentative de connexion n’est pas autorisée, la demande n’atteint pas le serveur Azure Database pour PostgreSQL.

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.
> 

![Configurer Autoriser l’accès aux services Azure dans le portail](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Connexion à partir d’un réseau virtuel
Pour vous connecter de manière sécurisée à votre serveur Azure Database pour PostgreSQL à partir d’un réseau virtuel, vous pouvez utiliser des [points de terminaison de service de réseau virtuel](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gestion par programmation des règles de pare-feu
En dehors du Portail Azure, les règles de pare-feu peuvent être gérées par programmation à l’aide d’Azure CLI.
Consultez également la page [Créer et gérer les règles de pare-feu de la base de données Azure pour PostgreSQL à l’aide d’Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="troubleshooting-firewall-issues"></a>Résolution des problèmes de pare-feu
Considérez les points suivants quand l’accès au service de serveur de base de données Microsoft Azure pour PostgreSQL présente un comportement anormal :

* **Les modifications apportées à la liste verte n’ont pas encore pris effet :** Jusqu’à cinq minutes peuvent s’écouler avant que les changements apportés à la configuration du pare-feu du serveur Azure Database pour PostgreSQL ne soient effectives.

* **La connexion n’est pas autorisée ou un mot de passe incorrect a été utilisé :** Si une connexion n’a pas d’autorisations sur le serveur Azure Database pour PostgreSQL ou que le mot de passe est incorrect, la connexion au serveur Azure Database pour PostgreSQL est refusée. Créer un paramètre de pare-feu permet uniquement aux clients de tenter de se connecter à votre serveur ; chaque client doit tout de même fournir les informations d’identification de sécurité nécessaires.

   Par exemple, avec un client JDBC, l’erreur suivante est susceptible d’apparaître.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **Adresse IP dynamique :** Si vous avez une connexion Internet avec un adressage IP dynamique et que le pare-feu demeure infranchissable, vous pouvez essayer une des solutions suivantes :

   * Demandez à votre fournisseur de services Internet (ISP) la plage d’adresses IP affectée à vos ordinateurs clients qui accèdent au serveur de base de données Azure pour PostgreSQL, puis ajoutez cette plage dans une règle de pare-feu.

   * Obtenez un adressage IP statique à la place pour vos ordinateurs clients, puis ajoutez l’adresse IP statique en tant que règle de pare-feu.

* **L’adresse IP du serveur semble être publique :** les connexions au serveur Azure Database pour PostgreSQL sont routées via une passerelle Azure accessible publiquement. Toutefois, l’adresse IP réelle du serveur est protégée par le pare-feu. Pour plus d’informations, consultez l’article [Architecture de connectivité](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations sur la création de règles de pare-feu au niveau du serveur et au niveau de la base de données, consultez les articles suivants :
* [Créer et gérer des règles de pare-feu de la base de données Azure pour PostgreSQL à l’aide du Portail Azure](howto-manage-firewall-using-portal.md)
* [Créer et gérer des règles de pare-feu de la base de données Azure pour PostgreSQL à l’aide d’Azure CLI](howto-manage-firewall-using-cli.md)
- [Points de terminaison de service de réseau virtuel dans Azure Database pour PostgreSQL](./concepts-data-access-and-security-vnet.md)
