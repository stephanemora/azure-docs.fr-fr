---
title: 'Démarrage rapide : Créer un serveur – Azure CLI – Azure Database pour MySQL'
description: Ce guide de démarrage rapide explique comment utiliser l’interface CLI Azure pour créer un serveur Azure Database pour MySQL dans un groupe de ressources Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/09/2019
ms.custom: mvc
ms.openlocfilehash: acf5f3cdf761e1773d6e9384a4ceb99a645ed7cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "74773502"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Démarrage rapide : Création d’un serveur Azure Database pour MySQL à l’aide de la CLI Azure

> [!TIP]
> Envisagez d’utiliser la commande Azure CLI plus simple [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) (actuellement en préversion). Essayez le guide de [démarrage rapide](./quickstart-create-server-up-azure-cli.md).

Ce guide de démarrage rapide explique comment utiliser l’interface CLI Azure pour créer un serveur Azure Database pour MySQL dans un groupe de ressources Azure en environ cinq minutes. L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 

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

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL
Créez un serveur Azure Database pour MySQL avec la commande **[az mysql server create](/cli/azure/mysql/server#az-mysql-server-create)** . Un serveur peut gérer plusieurs bases de données. En règle générale, une base de données distincte est utilisée pour chaque projet ou pour chaque utilisateur.

**Paramètre** | **Exemple de valeur** | **Description**
---|---|---
name | mydemoserver | Choisissez un nom unique qui identifie votre serveur de base de données Azure pour MySQL. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
resource-group | myResourceGroup | Indiquez le nom du groupe de ressources Azure.
sku-name | GP_Gen5_2 | Nom du la référence SKU. Suit la convention {niveau tarifaire}\_{génération de calcul}\_{vCores} dans le raccourci. Consultez le tableau ci-dessous pour plus d’informations sur le paramètre sku-name.
backup-retention | 7 | Durée pendant laquelle la sauvegarde doit être conservée. Exprimée en jours. La plage s’étend de 7 à 35. 
geo-redundant-backup | Désactivé | Indique si les sauvegardes géoredondantes doivent être activées ou non pour ce serveur. Valeurs autorisées : Activé, Désactivé.
location | westus | Emplacement Azure du serveur.
ssl-enforcement | activé | Indique si le protocole SSL doit être activé ou non pour ce serveur. Valeurs autorisées : Activé, Désactivé.
storage-size | 51200 | Capacité de stockage du serveur (en mégaoctets). storage-size doit avoir une valeur de 5 120 Mo minimum et augmenter par incréments de 1 024 Mo. Consultez le document [Niveaux tarifaires](./concepts-pricing-tiers.md) pour plus d’informations sur les limites de taille de stockage. 
version | 5.7 | Version principale de MySQL.
admin-user | myadmin | Nom d’utilisateur du compte administrateur. Il ne peut pas être **azure_superuser** (superutilisateur), **admin**, **administrator** (administrateur), **root** (racine), **guest** (invité) ou **public**.
admin-password | *mot de passe sécurisé* | Mot de passe de l’utilisateur Administrateur. Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres et caractères non alphanumériques.


La valeur du paramètre sku-name suit la convention {tarification}\_{génération de calcul}\_{vCores} comme dans les exemples ci-dessous :
+ `--sku-name B_Gen5_1` correspond à De base, Gen 5 et 1 vCore. Cette option correspond à la plus petite référence disponible.
+ `--sku-name GP_Gen5_32` correspond à Usage général, Gen 5 et 32 vCores.
+ `--sku-name MO_Gen5_2` correspond à Mémoire optimisée, Gen 5 et 2 vCores.

Consultez la documentation des [niveaux tarifaires](./concepts-pricing-tiers.md) pour comprendre les valeurs valides par région et par niveau.

L’exemple suivant crée un serveur MySQL 5.7 dans la région USA Ouest, nommé `mydemoserver`, dans votre groupe de ressources `myresourcegroup` avec l’identifiant d’administrateur du serveur `myadmin`. Il s’agit d’un serveur à **usage général**, de **4e génération** avec **2 vCores**. Remplacez `<server_admin_password>` par votre propre valeur.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

> [!NOTE]
> Choisissez le niveau tarifaire De base si votre charge de travail n’a pas besoin d’une grande capacité de calcul et d’E/S. Notez que les serveurs créés avec le niveau tarifaire De base ne peuvent plus ensuite être mis à l’échelle vers le niveau Usage général ou Mémoire optimisée. Pour plus d’informations, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/mysql/).
> 

## <a name="configure-firewall-rule"></a>Configurer une règle de pare-feu
Créez une règle de pare-feu au niveau du serveur Azure Database pour MySQL avec la commande **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create)** . Une règle de pare-feu au niveau du serveur permet à une application externe, comme l’outil de ligne de commande **mysql.exe** ou MySQL Workbench de se connecter à votre serveur via le pare-feu du service Azure MySQL. 

L’exemple suivant crée une règle de pare-feu appelée `AllowMyIP` qui autorise les connexions d’une adresse IP spécifique, 192.168.0.1. Remplacez l’adresse ou la plage d’adresses IP qui correspondent à l’emplacement de votre connexion. 

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
> 


## <a name="configure-ssl-settings"></a>Configurer les paramètres SSL
Par défaut, des connexions SSL entre votre serveur et les applications clientes sont appliquées. Ce paramètre par défaut garantit la sécurité des données « en mouvement » en chiffrant le flux de données sur Internet. Pour faciliter ce démarrage rapide, désactivez les connexions SSL pour votre serveur. La désactivation des connexions SSL n’est pas recommandée pour les serveurs de production. Pour plus d’informations, consultez l’article [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).

L’exemple suivant désactive l’application de SSL sur votre serveur MySQL.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Le résultat est au format JSON. Notez les valeurs **fullyQualifiedDomainName** et **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Connectez-vous au serveur avec l’outil de ligne de commande mysql.exe
Connectez-vous à votre serveur avec l’outil en ligne de commande **mysql.exe**. Vous pouvez télécharger MySQL [ici](https://dev.mysql.com/downloads/) et l’installer sur votre ordinateur. 

Tapez les commandes suivantes : 

1. Connectez-vous au serveur avec l’outil de ligne de commande **mysql** :
   ```bash
   mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Afficher l’état du serveur :
   ```sql
   mysql> status
   ```
   Si tout se déroule correctement, l’outil en ligne de commande doit générer le texte suivant :

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.7 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connectez-vous au serveur à l’aide de l’outil MySQL Workbench GUI
1. Lancez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir [d’ici](https://dev.mysql.com/downloads/workbench/).

2. Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

   ![configurer une nouvelle connexion](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Paramètre** | **Valeur suggérée** | **Description** |
|---|---|---|
|   Nom de connexion | Ma connexion | Spécifiez une étiquette pour cette connexion (il peut s’agir de n’importe quoi) |
| Méthode de connexion | choisissez Standard (TCP/IP) | Utilisez le protocole TCP/IP pour vous connecter à la base de données Azure pour MySQL> |
| HostName | mydemoserver.mysql.database.azure.com | Nom du serveur que vous avez noté précédemment |
| Port | 3306 | Le port par défaut pour MySQL est utilisé |
| Nom d’utilisateur | myadmin@mydemoserver | Connexion d’administrateur serveur que vous avez notée précédemment |
| Mot de passe | **** | Utilisez le mot de passe du compte Administrateur que vous avez configuré précédemment |

Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés.
À présent, vous pouvez cliquer sur la connexion pour vous connecter au serveur.

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez pas besoin de ces ressources pour un autre guide de démarrage rapide ou didacticiel, vous pouvez les supprimer en exécutant la commande suivante : 

```azurecli-interactive
az group delete --name myresourcegroup
```

Si vous souhaitez simplement supprimer le serveur nouvellement créé, vous pouvez exécuter la commande **[az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete)** .
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Conception d’une base de données MySQL avec Azure CLI](./tutorial-design-database-using-cli.md)
