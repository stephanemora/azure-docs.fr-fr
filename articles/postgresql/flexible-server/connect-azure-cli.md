---
title: 'Démarrage rapide : Se connecter en utilisant Azure CLI - Azure Database pour PostgreSQL - Serveur flexible'
description: Ce guide de démarrage rapide indique plusieurs moyens de se connecter à Azure CLI avec Azure Database pour PostgreSQL - Serveur flexible.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 3017d10abc910233e0627037349c0dfd966fd88e
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883793"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Démarrage rapide : Se connecter et interroger avec Azure CLI, avec Azure Database pour PostgreSQL - Serveur flexible

> [!IMPORTANT]
> Azure Database pour PostgreSQL - Serveur flexible est en actuellement en préversion publique.

Ce guide de démarrage rapide montre comment se connecter à un serveur flexible Azure Database pour PostgreSQL en utilisant Azure CLI avec la commande ```az postgres flexible-server connect```. Cette commande vous permet de tester la connectivité à votre serveur de base de données et d’exécuter des requêtes. Vous pouvez également exécuter plusieurs requêtes en utilisant le mode interactif. 

## <a name="prerequisites"></a>Prérequis
- Un compte Azure. Si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/free/).
- Installez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli) (2.20.0 ou ultérieure)
- Connectez-vous en utilisant Azure CLI avec la commande ```az login``` 
- Activez la persistance des paramètres avec ```az config param-persist on```. La persistance des paramètres vous permet d’utiliser le contexte local sans devoir répéter de nombreux arguments comme le groupe de ressources ou la région.

## <a name="create-an-postgresql-flexible-server"></a>Créer un serveur flexible PostgreSQL

La première chose que nous allons créer est un serveur PostgreSQL managé. Dans [Azure Cloud Shell](https://shell.azure.com/), exécutez le script suivant et notez le **nom du serveur**, le **nom d’utilisateur** et le **mot de passe** générés à partir de cette commande.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Vous pouvez fournir des arguments supplémentaires à cette commande pour la personnaliser. Consultez tous les arguments pour [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Visualiser tous les arguments
Vous pouvez visualiser tous les arguments de cette commande avec l’argument ```--help```. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Tester la connexion au serveur de base de données
Vous pouvez tester et vérifier la connexion à la base de données à partir de votre environnement de développement en utilisant la commande.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Exemple :** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
La sortie vous indique si la connexion a réussi.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Si la connexion a échoué, essayez ces solutions :
- Vérifiez si le port 5432 est ouvert sur votre machine cliente.
- Vérifiez si le nom d’utilisateur et le mot de passe de votre administrateur de serveur sont corrects.
- Vérifiez si vous avez configuré une règle de pare-feu pour votre machine cliente.
- Si vous avez configuré votre serveur avec un accès privé dans le réseau virtuel, vérifiez que votre machine cliente est dans le même réseau virtuel.

## <a name="run-single-query"></a>Exécuter une seule requête
Vous pouvez exécuter une seule requête avec la commande en utilisant l’argument ```--querytext```, ```-q```.

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Exemple :** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Vous voyez une sortie comme celle-ci :

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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

## <a name="run-multiple-queries-using-interactive-mode"></a>Exécuter plusieurs requêtes en utilisant le mode interactif
Vous pouvez exécuter plusieurs requêtes en utilisant le mode **interactif**. Pour activer le mode interactif, exécutez la commande suivante :

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Exemple :**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Vous verrez l’expérience du shell **psql** comme ci-dessous :

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le serveur](./how-to-manage-server-cli.md)
