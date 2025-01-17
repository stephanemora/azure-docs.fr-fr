---
title: Chiffrement double d’infrastructure - Azure Database pour MySQL
description: Découvrez comment configurer et gérer le chiffrement double d’infrastructure pour votre Azure Database pour MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 0666cce0668d315c34a9d6cade1f8d6e087f20dc
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674398"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Chiffrement double d’infrastructure - Azure Database pour MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Découvrez comment configurer et gérer le chiffrement double d’infrastructure pour votre Azure Database pour MySQL.

## <a name="prerequisites"></a>Prérequis

* Vous devez avoir un abonnement Azure et être un administrateur de cet abonnement.
* Azure Database pour MySQL - Serveur unique doit se trouver sur le niveau tarifaire Usage général ou Mémoire optimisée et sur Stockage v2 universel. Avant de poursuivre, reportez-vous aux limitations du [chiffrement double d’infrastructure](concepts-infrastructure-double-encryption.md#limitations).

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>Créer un serveur Azure Database pour MySQL avec un chiffrement double d’infrastructure - Portail

Procédez comme suit pour créer un serveur Azure Database pour MySQL avec le chiffrement double d’infrastructure à partir de Portail Azure :

1. Sélectionnez **Créer une ressource** (+) dans l’angle supérieur gauche du portail.

2. Sélectionnez **Bases de données** > **Azure Database pour MySQL**. Vous pouvez également entrer **MySQL** dans la zone de recherche pour localiser le service.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Option Azure Database pour MySQL":::

3. Fournissez les informations basiques du serveur. Sélectionnez **Paramètres supplémentaires** et cochez la case **chiffrement double d’infrastructure** pour activer le paramètre.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Sélections Azure Database pour MySQL":::

4. Sélectionnez **Vérifier + créer** pour provisionner le serveur.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Résumé Azure Database pour MySQL":::

5. Une fois le serveur créé, vous pouvez valider le double chiffrement d’infrastructure en vérifiant l’état dans le panneau **Chiffrement des données**.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Validation Azure Database pour MySQL":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>Créer un serveur Azure Database pour MySQL avec un chiffrement double d’infrastructure - CLI

Procédez comme suit pour créer un serveur Azure Database pour MySQL avec le chiffrement double d’infrastructure à partir de l’interface de ligne de commande :

Cet exemple crée un groupe de ressources nommé `myresourcegroup` à l’emplacement `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
L’exemple suivant crée un serveur MySQL 5.7 dans la région USA Ouest, nommé `mydemoserver`, dans votre groupe de ressources `myresourcegroup` avec l’identifiant d’administrateur du serveur `myadmin`. Il s’agit d’un serveur à **usage général**, de **4e génération** avec **2 vCores**. Cela activera également le double chiffrement d’infrastructure pour le serveur créé. Remplacez `<server_admin_password>` par votre propre valeur.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>Étapes suivantes

 Pour en savoir plus sur le chiffrement des données, consultez [Chiffrement double d’infrastructure des données Azure Database pour MySQL](concepts-Infrastructure-double-encryption.md).
