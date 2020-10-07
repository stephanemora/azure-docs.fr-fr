---
title: Gérer un serveur – Azure CLI – Azure Database pour PostgreSQL – Serveur flexible
description: Découvrez comment gérer un serveur Azure Database pour PostgreSQL – Serveur flexible à partir d’Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0f3c21d5c7f328ddef000ca7f1eaa9d5e18e6ca9
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761887"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Gérer un serveur flexible Azure Database pour PostgreSQL à l’aide d’Azure CLI

> [!IMPORTANT]
> Azure Database pour PostgreSQL – Serveur flexible est en préversion

Cet article vous explique comment gérer votre serveur flexible déployé sur Azure. Les tâches de gestion incluent notamment la mise à l’échelle du calcul et du stockage, la réinitialisation de mot de passe et l’affichage des informations relatives au serveur.

## <a name="prerequisites"></a>Prérequis
Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer. Cet article nécessite que vous exécutiez localement Azure CLI version 2.0 ou ultérieure. Pour afficher la version installée, exécutez la commande `az --version`. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli).

Vous devrez vous connecter à votre compte à l’aide de la commande [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

```azurecli-interactive
az login
```

Sélectionnez l’abonnement spécifique sous votre compte à l’aide de la commande [az account set](/cli/azure/account). Notez la valeur **id** issue de la commande **az login** ; vous devez vous en servir comme valeur de l’argument **subscription** dans la commande. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource doit être facturée. Pour accéder à l’ensemble de votre abonnement, utilisez [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Si vous n’avez pas encore créé de serveur flexible, créez-en un pour vous familiariser avec ce guide.

## <a name="scale-compute-and-storage"></a>Mettre à l’échelle le calcul et le stockage

Vous pouvez facilement effectuer un scale-up de votre niveau de calcul, de vos vCores et de votre stockage à l’aide de la commande suivante. Vous pouvez voir toutes les opérations de serveur que vous pouvez effectuer en exécutant [az postgres flexible-server server overview](https://azure.microsoft.com/services/postgresql/).

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Voici les détails des arguments ci-dessus :

**Paramètre** | **Exemple de valeur** | **Description**
---|---|---
name | mydemoserver | Entrez un nom unique pour votre serveur. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
resource-group | myResourceGroup | Indiquez le nom du groupe de ressources Azure.
sku-name|Standard_D4ds_v3|Entrez le nom du niveau de calcul et de la taille. Respecte la convention Standard_ {taille de machine virtuelle} en abrégé. Pour plus d’informations, consultez les [niveaux tarifaires](../concepts-pricing-tiers.md).
storage-size | 6144 | Capacité de stockage du serveur (en mégaoctets). 5120 minimum et augmente par incrément de 1024.

> [!IMPORTANT]
> Le stockage ne peut pas faire l’objet d’un scale-down. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gérer des bases de données PostgreSQL sur un serveur

Vous pouvez utiliser différentes applications pour vous connecter à votre serveur de base de données Azure pour PostgreSQL. Si votre ordinateur client a PostgreSQL installé, vous pouvez utiliser une instance locale de [psql](https://www.postgresql.org/docs/current/static/app-psql.html) pour vous connecter à un serveur Azure PostgreSQL. Nous allons maintenant utiliser l’utilitaire de ligne de commande psql pour nous connecter au serveur Azure pour PostgreSQL.

1. Exécutez la commande psql suivante pour vous connecter à un serveur Azure Database pour PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Par exemple, la commande suivante se connecte à la base de données par défaut appelée **postgres** sur votre serveur PostgreSQL **mydemoserver.postgres.database.azure.com** à l’aide des informations d’identification d’accès. Saisissez le `<server_admin_password>` que vous avez choisi à l’invite de mot de passe.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Une fois connecté, l’utilitaire psql affiche une invite postgres dans laquelle vous pouvez taper des commandes sql. Il est possible qu’un avertissement s’affiche au niveau de la sortie de connexion initiale, car la version de l’utilitaire psql que vous utilisez peut être différente de celle du serveur Azure Database pour PostgreSQL.

   Exemple de sortie psql :

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Si le pare-feu n’est pas configuré pour autoriser l’adresse IP de votre client, l’erreur suivante se produit :
   >
   > « psql: FATAL: aucune entrée pg_hba.conf pour l’hôte `<IP address>`, utilisateur « myadmin », base de données « postgres », SSL sur FATAL : Connexion SSL obligatoire. Spécifiez les options SSL puis réessayez.
   >
   > Confirmez que l'adresse IP de votre client est autorisée à l’étape des règles de pare-feu ci-dessus.

2. Créez une base de données vide nommée « postgresdb » à l’invite en tapant la commande suivante :

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. À l’invite, exécutez la commande suivante pour basculer la connexion sur la nouvelle base de données **postgresdb** :

    ```bash
    \c postgresdb
    ```

4. Tapez `\q`, puis sélectionnez la touche Entrée pour quitter psql.

Vous vous êtes connecté au serveur Azure Database pour PostgreSQL via psql, et vous avez créé une base de données utilisateur vide.

## <a name="reset-admin-password"></a>Réinitialiser le mot de passe administrateur
Vous pouvez changer le mot de passe du rôle d’administrateur avec cette commande.
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Assurez-vous que le mot de passe compte huit caractères minimum et 128 caractères maximum.
> Le mot de passe doit contenir des caractères de trois des catégories suivantes : Lettres majuscules, lettres minuscules, chiffres et caractères non alphanumériques.

## <a name="delete-a-server"></a>Supprimer un serveur

Si vous souhaitez simplement supprimer le serveur flexible PostgreSQL, vous pouvez exécuter la commande [az postgres flexible-server delete](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete).

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des concepts de sauvegarde et de restauration](concepts-backup-restore.md)
- [Régler et superviser le serveur](concepts-monitoring.md)
