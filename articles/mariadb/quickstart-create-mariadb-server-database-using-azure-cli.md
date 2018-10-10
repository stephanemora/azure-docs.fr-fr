---
title: 'Démarrage rapide : Créer un serveur Azure Database for MariaDB - Azure CLI'
description: Ce guide de démarrage rapide explique comment utiliser l’interface CLI Azure pour créer un serveur Azure Database for MariaDB dans un groupe de ressources Azure.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996602"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>Créer un serveur Azure Database for MariaDB à l’aide d’Azure CLI
Ce guide de démarrage rapide explique comment utiliser l’interface Azure CLI pour créer en quelque cinq minutes un serveur Azure Database for MariaDB dans un groupe de ressources Azure. L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Si vous possédez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource existe ou est facturée. Sélectionnez un ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Créer un serveur Azure Database for MariaDB
Créez un serveur Azure Database for MariaDB avec la commande **[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create)**. Un serveur peut gérer plusieurs bases de données. En règle générale, une base de données distincte est utilisée pour chaque projet ou pour chaque utilisateur.

**Paramètre** | **Exemple de valeur** | **Description**
---|---|---
Nom | mydemoserver | Choisissez un nom unique qui identifie votre serveur Azure Database for MariaDB. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
resource-group | myResourceGroup | Indiquez le nom du groupe de ressources Azure.
sku-name | GP_Gen5_2 | Nom du la référence SKU. Suit la convention {niveau tarifaire}_{génération de calcul}_{vCores} dans le raccourci. Consultez ce tableau ci-dessous pour plus d’informations sur le paramètre sku-name.
backup-retention | 7 | Durée pendant laquelle une sauvegarde doit être conservée. L’unité est exprimée en jours. La plage s’étend de 7 à 35. 
geo-redundant-backup | Désactivé | Indique si les sauvegardes géoredondantes doivent être activées pour ce serveur ou non. Valeurs autorisées : Enabled (Activé), Disabled (Désactivé).
location | westus | Emplacement Azure pour le serveur.
ssl-enforcement | activé | Si le protocole ssl doit être activé ou non pour ce serveur. Valeurs autorisées : Enabled (Activé), Disabled (Désactivé).
storage-size | 51200 | Capacité de stockage du serveur (l’unité est en mégaoctets). La valeur valide de storage-size est de 5 120 Mo minimum et augmente par incréments de 1 024 Mo. Consultez le document des [tarifs](./concepts-pricing-tiers.md) pour plus d’informations sur les limites de taille de stockage. 
version | 10.2 | Version majeure du moteur MariaDB.
admin-user | myadmin | Nom d’utilisateur pour la connexion de l’administrateur. Il ne peut pas être **azure_superuser** (super_utilisateur), **admin**, **administrator** (administrateur), **root** (racine), **guest** (invité) ou **public**.
admin-password | Password123 | Mot de passe de l’utilisateur Administrateur. Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères appartenant à trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres et caractères non alphanumériques.

La valeur du paramètre sku-name suit la convention {tarification}\_{génération de calcul}\_{vCores} comme dans les exemples ci-dessous :
+ `--sku-name B_Gen5_4` correspond à De base, Gén 5 et 4 vCores.
+ `--sku-name GP_Gen5_32` correspond à Usage général, Gen 5 et 32 vCores.
+ `--sku-name MO_Gen5_2` correspond à Mémoire optimisée, Gen 5 et 2 vCores.

Consultez la documentation des [niveaux tarifaires](./concepts-pricing-tiers.md) pour comprendre les valeurs valides par région et par niveau.

L’exemple suivant crée un serveur dans la région USA Ouest, nommé `mydemoserver`, dans votre groupe de ressources `myresourcegroup` avec l’identifiant d’administrateur serveur `myadmin`. Il s’agit d’un serveur à **usage général**, de **5e génération** avec 2 **vCores**. Le nom du serveur correspond au nom DNS et doit ainsi être globalement unique dans Azure. Remplacez `<server_admin_password>` par votre propre valeur.
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>Configurer une règle de pare-feu
Créez une règle de pare-feu au niveau du serveur Azure Database for MariaDB avec la commande **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)**. Une règle de pare-feu au niveau du serveur permet à une application externe, comme l’outil en ligne de commande **mysql** ou MySQL Workbench de se connecter à votre serveur via le pare-feu du service Azure MariaDB. 

L’exemple suivant crée une règle de pare-feu appelée `AllowMyIP` qui autorise les connexions d’une adresse IP spécifique, 192.168.0.1. Remplacez l’adresse ou la plage d’adresses IP qui correspondent à l’emplacement de votre connexion. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Les connexions à Azure Database for MariaDB communiquent sur le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
> 

## <a name="configure-ssl-settings"></a>Configurer les paramètres SSL
Par défaut, des connexions SSL entre votre serveur et les applications clientes sont appliquées. Ce paramètre par défaut garantit la sécurité des données « en mouvement » en chiffrant le flux de données sur Internet. Pour faciliter ce démarrage rapide, désactivez les connexions SSL pour votre serveur. La désactivation des connexions SSL n’est pas recommandée pour les serveurs de production. Pour plus d’informations, voir [Configuration de la connectivité SSL dans votre application pour se connecter de manière sécurisée à Azure Database for MariaDB](./howto-configure-ssl.md).

L’exemple suivant désactive l’application de SSL sur votre serveur MariaDB.
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Le résultat est au format JSON. Notez les valeurs **fullyQualifiedDomainName** et **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-server-using-mysql-command-line"></a>Se connecter au serveur avec la ligne de commande mysql
Connectez-vous à votre serveur à l’aide de l’outil en ligne de commande **mysql**. Vous pouvez télécharger cet outil [ici](https://dev.mysql.com/downloads/) et l’installer sur votre ordinateur. Une autre possibilité consiste à cliquer sur le bouton **Essayer** des exemples de code ou sur le bouton `>_` de la barre d’outils supérieure droite du Portail Azure et à lancer **Azure Cloud Shell**.

Tapez les commandes suivantes : 

1. Connectez-vous au serveur avec l’outil de ligne de commande **mysql** :
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. Affichez l’état du serveur à l’invite mysql> :
```sql
status
```
Si tout se déroule correctement, l’outil en ligne de commande doit générer le texte suivant :

```bash
C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.39.0 MariaDB Server

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

Connection id:          64681
Current database:
Current user:           myadmin@40.118.201.21
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MariaDB Server
Protocol version:       10
Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 1 day 3 hours 28 min 50 sec

Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
--------------

mysql>
```

> [!TIP]
> Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.7 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-server-using-mysql-workbench"></a>Se connecter au serveur à l’aide de MySQL Workbench
1.  Lancez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir [d’ici](https://dev.mysql.com/downloads/workbench/).

2.  Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

   ![configurer une nouvelle connexion](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **Paramètre** | **Valeur suggérée** | **Description** |
|---|---|---|
|   Nom de connexion | Ma connexion | Spécifiez une étiquette pour cette connexion (il peut s’agir de n’importe quoi) |
| Méthode de connexion | choisissez Standard (TCP/IP) | Utilisez le protocole TCP/IP pour vous connecter à Azure Database for MariaDB |
| Nom d’hôte | mydemoserver.mariadb.database.azure.com | Nom du serveur que vous avez noté précédemment |
| Port | 3306 | Le port par défaut pour MariaDB est utilisé. |
| Nom d’utilisateur | myadmin@mydemoserver | Connexion d’administrateur serveur que vous avez notée précédemment |
| Mot de passe | **** | Utilisez le mot de passe du compte Administrateur que vous avez configuré précédemment |

Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés.
À présent, vous pouvez cliquer sur la connexion pour vous connecter au serveur.

## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous n’avez pas besoin de ces ressources pour un autre guide de démarrage rapide ou didacticiel, vous pouvez les supprimer en exécutant la commande suivante : 

```azurecli-interactive
az group delete --name myresourcegroup
```

Si vous souhaitez simplement supprimer le serveur nouvellement créé, vous pouvez exécuter la commande **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)**.
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->