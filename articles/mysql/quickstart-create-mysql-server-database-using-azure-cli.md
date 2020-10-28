---
title: 'Démarrage rapide : Créer un serveur – Azure CLI – Azure Database pour MySQL'
description: Ce guide de démarrage rapide explique comment utiliser l’interface CLI Azure pour créer un serveur Azure Database pour MySQL dans un groupe de ressources Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 161d1c95507d366ce00f75580a100fd2607675d9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544016"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Démarrage rapide : Création d’un serveur Azure Database pour MySQL à l’aide de la CLI Azure

> [!TIP]
> Envisagez d’utiliser la commande Azure CLI plus simple [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) (actuellement en préversion). Essayez le guide de [démarrage rapide](./quickstart-create-server-up-azure-cli.md).

Ce guide de démarrage rapide montre comment utiliser les commandes [Azure CLI](/cli/azure/get-started-with-azure-cli) dans [Azure Cloud Shell](https://shell.azure.com) afin de créer un serveur Azure Database pour MySQL en cinq minutes. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Prérequis
Cet article nécessite que vous exécutiez localement Azure CLI version 2.0 ou ultérieure. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

Vous devrez vous connecter à votre compte à l’aide de la commande [az login](/cli/azure/reference-index#az-login). Notez la propriété **id** , qui fait référence à l’ **ID d’abonnement** pour votre compte Azure. 

```azurecli-interactive
az login
```

Sélectionnez l’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account). Notez la valeur **id** issue de la commande **az login**  ; vous devez vous en servir comme valeur de l’argument **subscription** dans la commande. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Pour accéder à l’ensemble de votre abonnement, utilisez [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Création d’un serveur Azure Database pour MySQL
Créez un [groupe de ressources Azure](../azure-resource-manager/management/overview.md) à l’aide de la commande [az group create](/cli/azure/group), puis créez votre serveur MySQL à l’intérieur de ce groupe de ressources. Vous devez fournir un nom unique. L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Créez un serveur Azure Database pour MySQL avec la commande [az sql server create](/cli/azure/mysql/server#az-mysql-server-create). Un serveur peut contenir plusieurs bases de données.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Voici les détails des arguments ci-dessus : 

**Paramètre** | **Exemple de valeur** | **Description**
---|---|---
name | mydemoserver | Entrez un nom unique pour votre serveur Azure Database pour MySQL. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
resource-group | myResourceGroup | Indiquez le nom du groupe de ressources Azure.
location | westus | Emplacement Azure du serveur.
admin-user | myadmin | Nom d’utilisateur du compte administrateur. Il ne peut pas être **azure_superuser** (superutilisateur), **admin** , **administrator** (administrateur), **root** (racine), **guest** (invité) ou **public** .
admin-password | *mot de passe sécurisé* | Mot de passe de l’utilisateur Administrateur. Il doit contenir entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres et caractères non alphanumériques.
sku-name|GP_Gen5_2|Entrez le nom du niveau tarifaire et de la configuration de calcul. Suit la convention {niveau tarifaire} _{génération de calcul}_ {vCores} dans le raccourci. Pour plus d’informations, consultez les [niveaux tarifaires](./concepts-pricing-tiers.md).

>[!IMPORTANT] 
>- La version MySQL par défaut sur votre serveur est 5.7. Nous disposons également des versions 5.6 et 8.0.
>- Pour voir tous les arguments de la commande **az mysql server create** , consultez ce [document de référence](/cli/azure/mysql/server#az-mysql-server-create).
>- Le protocole SSL est activé par défaut sur votre serveur. Pour plus d’information sur le protocole SSL, consultez [Configurer la connectivité SSL](howto-configure-ssl.md).

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur 
Par défaut, le serveur créé est protégé par des règles de pare-feu et n’est pas accessible publiquement. Vous pouvez configurer la règle de pare-feu sur votre serveur à l’aide de la commande [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule). Cela vous permettra de vous connecter au serveur localement.

L’exemple suivant crée une règle de pare-feu appelée `AllowMyIP` qui autorise les connexions d’une adresse IP spécifique, 192.168.0.1. Remplacez l’adresse IP à partir de laquelle vous allez vous connecter. Vous pouvez utiliser une plage d’adresses IP si nécessaire. En cas de doute, accédez à [https://whatismyipaddress.com/](https://whatismyipaddress.com/) pour obtenir votre adresse IP.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Le résultat est au format JSON. Notez les valeurs **fullyQualifiedDomainName** et **administratorLogin** .
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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Se connecter au serveur Azure Database pour MySQL à l’aide du client de ligne de commande mysql
Vous pouvez vous connecter à votre serveur à l’aide d’un outil en ligne de commande populaire, **[mysql.exe](https://dev.mysql.com/downloads/)** , avec [Azure Cloud Shell](../cloud-shell/overview.md). Vous pouvez également utiliser la ligne de commande mysql dans votre environnement local.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Nettoyer les ressources
Si vous n’avez pas besoin de ces ressources pour un autre guide de démarrage rapide ou didacticiel, vous pouvez les supprimer en exécutant la commande suivante : 

```azurecli-interactive
az group delete --name myresourcegroup
```

Si vous souhaitez simplement supprimer le serveur nouvellement créé, vous pouvez exécuter la commande [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Créer une application PHP sur Windows avec MySQL](../app-service/tutorial-php-mysql-app.md)
>[Créer une application PHP sur Linux avec MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)
>[Créer une application Spring basée sur Java avec MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)