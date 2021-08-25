---
title: 'Démarrage rapide : Se connecter en utilisant Azure CLI - Azure Database pour MySQL - Serveur flexible'
description: Ce guide de démarrage rapide indique plusieurs moyens de se connecter à Azure CLI avec Azure Database pour MySQL - Serveur flexible.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: f75b933769e94986b1c140fb8bcbadca44b96cf1
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643187"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Démarrage rapide : Se connecter et interroger avec Azure CLI, avec Azure Database pour MySQL - Serveur flexible

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database pour MySQL - Serveur flexible est actuellement en préversion publique.

Ce guide de démarrage rapide montre comment se connecter à un serveur flexible Azure Database pour MySQL en utilisant Azure CLI avec la commande ```az mysql flexible-server connect``` et exécuter une seule requête ou un fichier sql avec la commande ```az mysql flexible-server execute```. Cette commande vous permet de tester la connectivité à votre serveur de base de données et d’exécuter des requêtes. Vous pouvez également exécuter plusieurs requêtes en utilisant le mode interactif.

## <a name="prerequisites"></a>Prérequis

- Un compte Azure. Si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/free/).
- Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli) (2.20.0 ou ultérieure)
- Connectez-vous en utilisant Azure CLI avec la commande ```az login``` 
- Activez la persistance des paramètres avec ```az config param-persist on```. La persistance des paramètres vous permet d’utiliser le contexte local sans devoir répéter de nombreux arguments comme le groupe de ressources ou la région, etc.

## <a name="create-an-mysql-flexible-server"></a>Créer un serveur flexible MySQL

La première chose que nous allons créer est un serveur MySQL managé. Dans [Azure Cloud Shell](https://shell.azure.com/), exécutez le script suivant et notez le **nom du serveur**, le **nom d’utilisateur** et le **mot de passe** générés à partir de cette commande.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Vous pouvez fournir des arguments supplémentaires à cette commande pour la personnaliser. Consultez tous les arguments pour [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Création d'une base de données
Exécutez la commande suivante pour créer une base de données **newdatabase** si vous n’en avez pas déjà créé une.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Visualiser tous les arguments
Vous pouvez visualiser tous les arguments de cette commande avec l’argument ```--help```. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Tester la connexion au serveur de base de données
Exécutez le script suivant pour tester et vérifier la connexion à la base de données à partir de votre environnement de développement.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Exemple :**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Vous devez voir la sortie suivante pour une connexion réussie :

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Si la connexion a échoué, essayez ces solutions :
- Vérifiez si le port 3306 est ouvert sur votre machine cliente.
- Vérifiez si le nom d’utilisateur et le mot de passe de votre administrateur de serveur sont corrects.
- Vérifiez si vous avez configuré une règle de pare-feu pour votre machine cliente.
- Si vous avez configuré votre serveur avec un accès privé dans le réseau virtuel, vérifiez que votre machine cliente est dans le même réseau virtuel.

## <a name="run-multiple-queries-using-interactive-mode"></a>Exécuter plusieurs requêtes en utilisant le mode interactif
Vous pouvez effectuer plusieurs requêtes en utilisant le mode **interactif**. Pour activer le mode interactif, exécutez la commande suivante :

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Exemple :**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Vous verrez l’expérience du shell **MySQL** comme ci-dessous :

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

## <a name="run-single-query"></a>Exécuter une seule requête
Exécutez la commande suivante pour exécuter une seule requête en utilisant l’argument ```--querytext```, ```-q```.

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exemple :**
```azurecli
az mysql flexible-server execute -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Vous voyez une sortie comme celle-ci :

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-sql-file"></a>Exécuter un fichier SQL
Vous pouvez exécuter un fichier sql avec la commande en utilisant l’argument ```--file-path```, ```-q```.

```azurecli
az mysql flexible-server execute -n <server-name> -u <username> -p "<password>" -d <database-name> --file-path "<file-path>"
```

**Exemple :** 
```azurecli
az mysql flexible-server execute -n mysqldemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -f "./test.sql"
```

Vous voyez une sortie comme celle-ci :

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Running sql file '.\test.sql'...
Successfully executed the file.
Closed the connection to mysqldemoserver.
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
* [Se connecter à Azure Database pour MySQL - Serveur flexible en utilisant des connexions chiffrées](how-to-connect-tls-ssl.md)
* [Gérer le serveur](./how-to-manage-server-cli.md)

