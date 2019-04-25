---
title: Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure
description: Décrit comment créer et gérer des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande d’Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9a96361d3fb155ea5b400990690e3c2b1f65f819
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422370"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Création et gestion des règles de pare-feu Azure Database pour PostgreSQL à l’aide de l’interface de ligne de commande Azure
Règles de pare-feu de niveau serveur peuvent être utilisées pour gérer l’accès à une base de données Azure pour serveur PostgreSQL à partir d’une adresse IP spécifique ou de la plage d’adresses IP. À l’aide de commandes d’interface de ligne de commande Azure pratiques, vous pouvez créer, mettre à jour, supprimer, répertorier et afficher les règles de pare-feu pour gérer votre serveur. Pour une vue d’ensemble de base de données Azure pour PostgreSQL les règles de pare-feu, consultez [base de données Azure pour les règles de pare-feu de serveur PostgreSQL](concepts-firewall-rules.md).

Les règles de réseau virtuel peuvent également être utilisés pour sécuriser l’accès à votre serveur. En savoir plus sur [création et gestion de réseau virtuel de points de terminaison et des règles à l’aide de l’interface CLI Azure service](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Conditions préalables
Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- Installer l’utilitaire de ligne de commande [Azure CLI](/cli/azure/install-azure-cli) ou utiliser Azure Cloud Shell dans le navigateur.
- Un [serveur Azure Database pour PostgreSQL et une base de données](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Configuration des règles de pare-feu pour Azure Database pour PostgreSQL
Les commandes [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) permettent de configurer des règles de pare-feu.

## <a name="list-firewall-rules"></a>Répertorier les règles de pare-feu 
Pour répertorier les règles de pare-feu de serveur existant, exécutez la commande [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule).
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
La sortie répertorie les règles de pare-feu éventuelles, par défaut au format JSON. Vous pouvez utiliser le commutateur `--output table` pour obtenir une sortie dans un format de tableau plus lisible.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Créer une règle de pare-feu
Pour créer une règle de pare-feu sur le serveur, exécutez la commande [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Pour autoriser les applications à se connecter à votre serveur Azure Database pour PostgreSQL à partir d’adresses IP Azure, indiquez l’adresse IP 0.0.0.0 comme adresse IP de début et adresse IP de fin, comme dans cet exemple.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Cette option configure le pare-feu pour autoriser toutes les connexions à partir d’Azure, notamment les connexions issues des abonnements d’autres clients. Lorsque vous sélectionnez cette option, vérifiez que votre connexion et vos autorisations utilisateur limitent l’accès aux seuls utilisateurs autorisés.
> 

En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez créée, par défaut au format JSON. En cas d’échec, la sortie affiche un message d’erreur à la place.

## <a name="update-firewall-rule"></a>Mettre à jour une règle de pare-feu 
Mettez à jour une règle de pare-feu existante sur le serveur à l’aide de la commande [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule). Indiquez le nom de la règle de pare-feu existante comme entrée puis les adresses IP de début et de fin à mettre à jour.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez mise à jour, par défaut au format JSON. En cas d’échec, la sortie affiche un message d’erreur à la place.
> [!NOTE]
> Si la règle de pare-feu n’existe pas, elle est créée par la commande de mise à jour.

## <a name="show-firewall-rule-details"></a>Afficher les détails d’une règle de pare-feu
Vous pouvez également afficher les détails d’une règle de pare-feu existante au niveau du serveur en exécutant la commande [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule).
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
En cas de réussite, la sortie de la commande affiche les détails de la règle de pare-feu que vous avez spécifiée, par défaut au format JSON. En cas d’échec, la sortie affiche un message d’erreur à la place.

## <a name="delete-firewall-rule"></a>Supprimer une règle de pare-feu
Pour révoquer l’accès pour une plage d’adresses IP vers le serveur, supprimez une règle de pare-feu existante en exécutant la commande [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule). Indiquez le nom de la règle de pare-feu existante.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
En cas de réussite, aucun résultat ne s’affiche. En cas d’échec, le texte du message d’erreur est retourné.

## <a name="next-steps"></a>Étapes suivantes
- De même, vous pouvez utiliser un navigateur web pour [créer et gérer les règles de pare-feu Azure Database pour PostgreSQL feu à l’aide du portail Azure](howto-manage-firewall-using-portal.md).
- En savoir plus sur les [règles de pare-feu du serveur Azure Database pour PostgreSQL](concepts-firewall-rules.md).
- Sécuriser davantage l’accès à votre serveur par [création et gestion de réseau virtuel de points de terminaison et des règles à l’aide de l’interface CLI Azure service](howto-manage-vnet-using-cli.md).
- Pour vous aider à vous connecter à un serveur Azure Database pour PostgreSQL, consultez la rubrique [Bibliothèques de connexions pour Azure Database pour PostgreSQL](concepts-connection-libraries.md).
