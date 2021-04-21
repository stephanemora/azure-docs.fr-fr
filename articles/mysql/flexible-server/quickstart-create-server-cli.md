---
title: 'Démarrage rapide : Créer un serveur - Azure CLI - Azure Database pour MySQL - Serveur flexible'
description: Ce guide de démarrage rapide explique comment utiliser l'interface Azure CLI pour créer un serveur flexible Azure Database pour MySQL dans un groupe de ressources Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770234"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Démarrage rapide : Créer un serveur flexible Azure Database pour MySQL à l'aide d'Azure CLI

Ce guide de démarrage rapide montre comment utiliser les commandes [Azure CLI](/cli/azure/get-started-with-azure-cli) dans [Azure Cloud Shell](https://shell.azure.com) afin de créer un serveur flexible Azure Database pour MySQL en cinq minutes. Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

> [!IMPORTANT] 
> La fonctionnalité Serveur flexible Azure Database pour MySQL est actuellement disponible en préversion publique

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

Pour ouvrir Cloud Shell, sélectionnez simplement **Essayer** en haut à droite d’un bloc de code. Vous pouvez également ouvrir Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/bash](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell et sélectionnez **Entrée** pour les exécuter.

Si vous préférez installer et utiliser l’interface de ligne de commande en local, ce guide de démarrage rapide nécessite au minimum la version 2.0 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prérequis

Vous devrez vous connecter à votre compte à l’aide de la commande [az login](/cli/azure/reference-index#az_login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

```azurecli-interactive
az login
```

Sélectionnez l’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account#az_account_set). Notez la valeur **id** issue de la commande **az login** ; vous devez vous en servir comme valeur de l’argument **subscription** dans la commande. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Pour accéder à l’ensemble de votre abonnement, utilisez [az account list](/cli/azure/account#az_account_list).

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Créer un serveur flexible

Créez un [groupe de ressources Azure](../../azure-resource-manager/management/overview.md) à l'aide de la commande `az group create`, puis créez votre serveur flexible MySQL à l'intérieur de ce groupe de ressources. Vous devez fournir un nom unique. L’exemple suivant crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Créez un serveur flexible à l'aide de la commande `az mysql flexible-server create`. Un serveur peut contenir plusieurs bases de données. La commande suivante crée un serveur en utilisant les valeurs par défaut du service et les valeurs issues du [contexte local](/cli/azure/local-context) de votre interface Azure CLI : 

```azurecli-interactive
az mysql flexible-server create
```

Le serveur créé possède les attributs suivants : 
- Nom du serveur généré automatiquement, nom d'utilisateur administrateur, mot de passe administrateur, nom du groupe de ressources (s'il n'est pas déjà spécifié dans le contexte local) et même emplacement que votre groupe de ressources 
- Valeurs par défaut du service pour les configurations restantes : Niveau de calcul (Modulable), Taille de calcul/SKU (B1MS), Période de rétention des sauvegardes (7 jours) et Version de MySQL (5.7)
- La méthode de connectivité par défaut est Accès privé (intégration au réseau virtuel), avec un réseau virtuel et un sous-réseau générés automatiquement

> [!NOTE] 
> Une fois le serveur créé, la méthode de connectivité ne peut pas être modifiée. Par exemple, si vous avez sélectionné *Accès privé (intégration au réseau virtuel)* lors de la création du serveur, vous ne pouvez pas passer à *Accès public (adresses IP autorisées)* à l'issue de cette création. Nous vous recommandons vivement de sélectionner l'Accès privé lors de la création d'un serveur afin de pouvoir y accéder en toute sécurité à l'aide de l'intégration au réseau virtuel. Pour en savoir plus sur l'accès privé, consultez l'[article consacré aux concepts](./concepts-networking.md).

Si vous souhaitez changer des valeurs par défaut, reportez-vous à la [documentation de référence](/cli/azure/mysql/flexible-server) d’Azure CLI pour obtenir la liste complète des paramètres CLI configurables. 

Voici un exemple de sortie : 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Si vous souhaitez changer des valeurs par défaut, reportez-vous à la [documentation de référence](/cli/azure/mysql/flexible-server) d’Azure CLI pour obtenir la liste complète des paramètres CLI configurables. 

## <a name="create-a-database"></a>Création d'une base de données
Exécutez la commande suivante pour créer une base de données **newdatabase** si vous n’en avez pas déjà créé une.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut être bloqué. Si c’est le cas, vous ne pouvez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Pour vous connecter à votre serveur, vous devez fournir des informations sur l’hôte et des informations d’identification pour l’accès.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Le résultat est au format JSON. Notez les valeurs **fullyQualifiedDomainName** et **administratorLogin**. Voici un exemple de sortie JSON : 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Se connecter et tester la connexion à l'aide d'Azure CLI

Azure Database pour MySQL - Serveur flexible vous permet de vous connecter à votre serveur MySQL à l'aide de la commande Azure CLI ```az mysql flexible-server connect```. Cette commande vous permet de tester la connectivité à votre serveur de base de données, de créer une base de données de démarrage rapide et d'exécuter des requêtes directement sur votre serveur sans avoir à installer mysql.exe ou MySQL Workbench.  Vous pouvez aussi exécuter la commande en mode interactif pour exécuter plusieurs requêtes.

Exécutez le script suivant pour tester et vérifier la connexion à la base de données à partir de votre environnement de développement.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Exemple :**
```azurecli-interactive
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

Exécutez la commande suivante pour exécuter une seule requête en utilisant l’argument ```--querytext```, ```-q```.

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exemple :**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Pour en savoir plus sur l'utilisation de la commande ```az mysql flexible-server connect```, consultez la documentation relative [à la connexion et à l'interrogation](connect-azure-cli.md).

## <a name="connect-using-mysql-command-line-client"></a>Se connecter à l'aide du client de ligne de commande mysql

Si vous avez créé votre serveur flexible à l’aide d’un accès privé (intégration au réseau virtuel), vous devez vous connecter à votre serveur à partir d’une ressource figurant au sein du même réseau virtuel que votre serveur. Vous pouvez créer une machine virtuelle et l’ajouter au réseau virtuel créé avec votre serveur flexible. Pour plus d’informations, reportez-vous à la [documentation de la configuration des accès privés](how-to-manage-virtual-network-portal.md).

Si vous avez créé votre serveur flexible en utilisant un accès public (adresses IP autorisées), vous pouvez ajouter votre adresse IP locale à la liste des règles de pare-feu sur votre serveur. Pour obtenir des instructions pas à pas, consultez la [documentation sur la création ou la gestion de règles de pare-feu](how-to-manage-firewall-portal.md).

Vous pouvez utiliser [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) ou [MySQL Workbench](./connect-workbench.md) pour vous connecter au serveur à partir de votre environnement local. Le serveur flexible Azure Database pour MySQL prend en charge la connexion de vos applications clientes au service MySQL en utilisant le protocole TLS (Transport Layer Security), anciennement SSL (Secure Sockets Layer). TLS est un protocole standard qui garantit la sécurité des connexions réseau entre votre serveur de base de données et vos applications clientes, ce qui vous permet de respecter les exigences de conformité. Pour vous connecter à votre serveur flexible MySQL, vous devez télécharger le [certificat SSL public](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) pour la vérification de l'autorité de certification. Pour en savoir plus sur les connexions chiffrées ou la désactivation de SSL, consultez la documentation [Se connecter à Azure Database pour MySQL - Serveur flexible en utilisant des connexions chiffrées](how-to-connect-tls-ssl.md).

L'exemple suivant montre comment vous connecter à votre serveur flexible à l'aide de l'interface de ligne de commande mysql. Si ce n'est déjà fait, vous devez d'abord installer l'interface de ligne de commande mysql. Vous devez télécharger le certificat DigiCertGlobalRootCA requis pour les connexions SSL. Utilisez le paramètre de chaîne de connexion --ssl-mode=REQUIRED pour appliquer la vérification du certificat TLS/SSL. Passez le chemin d'accès du fichier de certificat local au paramètre --ssl-ca. Remplacez les valeurs par le nom et le mot de passe réels de votre serveur.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Si vous avez provisionné votre serveur flexible en utilisant un **accès public**, vous pouvez également utiliser [Azure Cloud Shell](https://shell.azure.com/bash) pour vous connecter à votre serveur flexible en utilisant le client MySQL préinstallé, comme indiqué ci-dessous :

Pour pouvoir utiliser Azure Cloud Shell pour vous connecter à votre serveur flexible, vous devez autoriser l’accès réseau depuis Azure Cloud Shell vers votre serveur flexible. Pour ce faire, accédez au panneau **Mise en réseau** de votre serveur flexible MySQL sur le portail Azure, puis sous la section **Pare-feu**, cochez la case « Autoriser l'accès public à ce serveur à partir de n'importe quel service Azure », comme illustré dans la capture d'écran ci-dessous, et cliquez sur Enregistrer pour enregistrer le paramètre.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Capture d'écran montrant comment autoriser l'accès d'Azure Cloud Shell au serveur flexible MySQL pour la configuration du réseau d'accès public.":::
 
 
> [!NOTE]
> Cocher la case **Autoriser l’accès public à partir de n’importe quel service Azure dans Azure à ce serveur** doit être utilisé seulement à des fins de développement ou de test. Elle configure le pare-feu pour autoriser les connexions à partir d’adresses IP allouées à n’importe quel service ou ressource Azure, y compris les connexions depuis les abonnements d’autres clients.

Cliquez sur **Essayer** pour lancer Azure Cloud Shell, puis utilisez les commandes suivantes pour vous connecter à votre serveur flexible. Utilisez le nom du serveur, le nom d’utilisateur et le mot de passe dans la commande. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Lorsque vous vous connectez à votre serveur flexible à l'aide d'Azure Cloud Shell, vous devez utiliser le paramètre --ssl=true et non --ssl-mode=REQUIRED.
> Ceci est principalement dû au fait que le client mysql.exe de la distribution MariaDB est préinstallé sur Azure Cloud Shell et que celui-ci requiert le paramètre --ssl, alors que le client mysql de la distribution Oracle requiert le paramètre --ssl-mode.

Si le message d’erreur suivant s’affiche lors de la connexion à votre serveur flexible à la suite de la commande précédente, c’est que vous n’avez pas défini la règle de pare-feu en utilisant l’option « Autoriser l’accès public à partir de n’importe quel service Azure dans Azure à ce serveur » mentionnée précédemment ou que cette option n’est pas enregistrée. Reconfigurez le pare-feu, puis réessayez.

ERREUR 2002 (HY000) : Connexion impossible au serveur MySQL sur <servername> (115)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas besoin de ces ressources pour un autre guide de démarrage rapide ou didacticiel, vous pouvez les supprimer en exécutant la commande suivante :

```azurecli-interactive
az group delete --name myresourcegroup
```

Si vous souhaitez simplement supprimer le serveur nouvellement créé, vous pouvez exécuter la commande `az mysql server delete`.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
> [Se connecter et exécuter des requêtes à l'aide d'Azure CLI](connect-azure-cli.md)
> [Se connecter à Azure Database pour MySQL - Serveur flexible en utilisant des connexions chiffrées](how-to-connect-tls-ssl.md)
> [Créer une application web PHP (Laravel) avec MySQL](tutorial-php-database-app.md)
