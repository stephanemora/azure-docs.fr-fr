---
title: Gérer la haute disponibilité redondante interzone - Azure CLI - Serveur flexible Azure Database pour MySQL
description: Cet article explique comment configurer la haute disponibilité redondante interzone dans le serveur flexible Azure Database pour MySQL avec l’interface Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107508961"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Gérer la haute disponibilité redondante interzone dans le serveur flexible Azure Database pour MySQL avec Azure CLI

> [!NOTE]
> Le serveur flexible Azure Database pour MySQL est en préversion publique. 

Cet article explique comment vous pouvez activer ou désactiver la configuration de la haute disponibilité avec redondance interzone au moment de la création du serveur dans votre serveur flexible. Vous pouvez également désactiver la haute disponibilité redondante interzone après la création du serveur. L’activation de la haute disponibilité redondante interzone après la création du serveur n’est pas prise en charge.

La fonctionnalité de haute disponibilité provisionne physiquement des réplicas principaux et secondaires séparés dans différentes zones. Pour plus d’informations, consultez la [documentation sur les concepts de haute disponibilité](./concepts/../concepts-high-availability.md). L’activation ou la désactivation de la haute disponibilité ne change pas vos autres paramètres, notamment la configuration du réseau virtuel, les paramètres du pare-feu et la conservation des sauvegardes. La désactivation de la haute disponibilité n’a pas d’impact sur la connectivité et le fonctionnement de votre application.

> [!IMPORTANT]
> La haute disponibilité redondante interzone est disponible dans un ensemble limité de régions. Consultez [ici](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) les régions prises en charge. 

## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Installez ou mettez à niveau Azure CLI avec la dernière version. Consultez [Installer Azure CLI](/cli/azure/install-azure-cli).
-  Connectez-vous au compte Azure à l’aide de la commande [az login](/cli/azure/reference-index#az-login). Notez la propriété **id**, qui fait référence à l’**ID d’abonnement** pour votre compte Azure.

    ```azurecli-interactive
    az login
    ````

- Si vous avez plusieurs abonnements, choisissez l’abonnement approprié dans lequel vous voulez créer le serveur à l’aide de la commande ```az account set```.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>Activer la haute disponibilité lors de la création du serveur
Vous pouvez uniquement créer des serveurs en utilisant les niveaux tarifaires Usage général ou Mémoire optimisée avec une haute disponibilité. Vous ne pouvez activer la haute disponibilité pour un serveur qu’au moment de sa création.

**Utilisation :**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Exemple :**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Désactiver la haute disponibilité

Vous pouvez désactiver la haute disponibilité à l’aide de la commande [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update). Notez que la désactivation de la haute disponibilité est prise en charge seulement si le serveur a été créé avec une haute disponibilité. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Exemple :**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Étapes suivantes

-   Découvrir la [continuité de l’activité](./concepts-business-continuity.md)
-   Découvrir la [haute disponibilité avec redondance interzone](./concepts-high-availability.md)
