---
title: 'Démarrage rapide : Créer un serveur – Azure CLI – Azure Database pour PostgreSQL – Serveur unique'
description: Guide de démarrage rapide pour créer une base de données Azure Database pour PostgreSQL – Serveur unique à l’aide d’Azure CLI (interface de ligne de commande).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cb88f085e18526a17621d3c4960a5aad6db727ad
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496591"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Démarrage rapide : Créer une base de données Azure pour PostgreSQL (serveur unique) à l’aide d’Azure CLI

> [!TIP]
> Envisagez d’utiliser la commande Azure CLI plus simple [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) (actuellement en préversion). Essayez le guide de [démarrage rapide](./quickstart-create-server-up-azure-cli.md).

Ce guide de démarrage rapide montre comment utiliser des commandes [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) dans [Azure Cloud Shell](https://shell.azure.com) afin de créer un serveur Azure Database pour PostgreSQL en cinq minutes.  Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Prérequis
Cet article nécessite que vous exécutiez localement Azure CLI version 2.0 ou ultérieure. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

Vous devrez vous connecter à votre compte à l’aide de la commande [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure. 

```azurecli-interactive
az login
```

Sélectionnez l’ID d’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account). Notez la valeur **id** issue de la commande **az login** ; vous devez vous en servir comme valeur de l’argument **subscription** dans la commande. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Pour accéder à l’ensemble de votre abonnement, utilisez [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list).

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Créer un serveur Azure Database pour PostgreSQL

Créez un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) à l’aide de la commande [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create), puis créez votre serveur PostgreSQL à l’intérieur de ce groupe de ressources. Vous devez fournir un nom unique. L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Créez un [serveur Azure Database pour PostgreSQL](overview.md) avec la commande [az sql server create](/cli/azure/postgres/server). Un serveur peut contenir plusieurs bases de données.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Voici les détails des arguments ci-dessus : 

**Paramètre** | **Exemple de valeur** | **Description**
---|---|---
name | mydemoserver | Choisissez un nom unique qui identifie votre serveur de base de données Azure pour PostgreSQL. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
resource-group | myResourceGroup | Indiquez le nom du groupe de ressources Azure.
location | westus | Emplacement Azure du serveur.
admin-user | myadmin | Nom d’utilisateur du compte administrateur. Il ne peut pas être **azure_superuser** (superutilisateur), **admin**, **administrator** (administrateur), **root** (racine), **guest** (invité) ou **public**.
admin-password | *mot de passe sécurisé* | Mot de passe de l’utilisateur Administrateur. Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres et caractères non alphanumériques.
sku-name|GP_Gen5_2|Entrez le nom du niveau tarifaire et de la configuration de calcul. Suit la convention {niveau tarifaire} _{génération de calcul}_ {vCores} dans le raccourci. Pour plus d’informations, consultez [Azure Database pour PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- La version PostgreSQL par défaut sur votre serveur est 9.6. Consultez toutes les versions prises en charge [ici](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).
>- Pour voir tous les arguments de la commande **az postgres server create**, consultez ce [document de référence](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).
>- Le protocole SSL est activé par défaut sur votre serveur. Pour plus d’information sur le protocole SSL, consultez [Configurer la connectivité SSL](./concepts-ssl-connection-security.md).

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur 
Par défaut, le serveur créé n’est pas accessible publiquement et est protégé par des règles de pare-feu. Vous pouvez configurer la règle de pare-feu sur votre serveur à l’aide de la commande [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) afin d’accorder à votre environnement local un accès pour se connecter au serveur. 

L’exemple suivant crée une règle de pare-feu appelée `AllowMyIP` qui autorise les connexions d’une adresse IP spécifique, 192.168.0.1. Remplacez l’adresse ou la plage d’adresses IP qui correspondent à l’emplacement de votre connexion.  En cas de doute, accédez à [https://whatismyipaddress.com/](https://whatismyipaddress.com/) pour obtenir votre adresse IP.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Assurez-vous que le pare-feu de votre réseau autorise le port 5432 qui est utilisé par les serveurs Azure Database pour PostgreSQL afin d’éviter les problèmes de connectivité. 

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Le résultat est au format JSON. Prenez note des valeurs **administratorLogin** et **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Se connecter au serveur Azure Database pour PostgreSQL à l’aide de psql
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) est le client couramment utilisé pour la connexion aux serveurs PostgreSQL. Vous pouvez vous connecter à votre serveur à l’aide de **psql** avec [Azure Cloud Shell](../cloud-shell/overview.md). Le cas échéant, vous pouvez également utiliser psql sur votre environnement local. Avec votre nouveau serveur PostgreSQL, une base de données vide, « postgres », est créée, que vous pouvez utiliser pour vous connecter à psql, comme indiqué ci-dessous. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Si vous préférez utiliser un chemin d’URL pour vous connecter à Postgres, encodez par URL le signe @ dans le nom d’utilisateur avec `%40`. La chaîne de connexion pour psql serait, par exemple,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez pas besoin de ces ressources pour un autre guide de démarrage rapide ou tutoriel, vous pouvez les supprimer en exécutant la commande suivante : 

```azurecli-interactive
az group delete --name myresourcegroup
```

Si vous souhaitez simplement supprimer le serveur nouvellement créé, vous pouvez exécuter la commande [az postgres server delete](/cli/azure/postgres/server).
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)
> 
> [Déployer une application web Django avec PostgreSQL](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Se connecter avec une application Node.js](./connect-nodejs.md)

