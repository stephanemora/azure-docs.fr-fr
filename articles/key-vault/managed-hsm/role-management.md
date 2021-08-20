---
title: Gestion des rôles de plan de données HSM managé - Azure Key Vault | Microsoft Docs
description: Utilisez cet article pour gérer les affectations de rôles pour votre HSM managé
services: key-vault
author: mbaldwin
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7a4179d35faffbf04a70a63aafead120259802f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471260"
---
# <a name="managed-hsm-role-management"></a>Gestion du rôle HSM managé

> [!NOTE]
> Key Vault prend en charge deux types de ressources : les coffres et les HSM managés. Cet article concerne le **HSM managé**. Si vous souhaitez apprendre à gérer un coffre, consultez [Gérer Key Vault à l’aide de l’interface de ligne de commande Azure](../general/manage-with-cli2.md).

Pour obtenir une vue d’ensemble de Managed HSM, consultez [Qu’est-ce que Managed HSM ?](overview.md). Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Cet article vous explique comment gérer les rôles pour un plan de données HSM managé. Pour en savoir plus sur le modèle de contrôle d’accès HSM managé, consultez [Contrôle d’accès HSM managé](access-control.md).

Pour permettre à un principal de sécurité (par exemple un utilisateur, un principal du service, un groupe ou une identité managée) d’effectuer des opérations de plan de données HSM managé, il doit se voir attribuer un rôle qui autorise l’exécution de ces opérations. Par exemple, si vous souhaitez permettre à une application d’effectuer une opération de signature à l’aide d’une clé, elle doit recevoir un rôle contenant« Microsoft.KeyVault/managedHSM/keys/sign/action » comme l’une des actions de données. Un rôle peut être attribué au niveau d’une étendue spécifique. Le RBAC local HSM managé prend en charge deux étendues, à l’échelle du HSM (`/` ou `/keys`) et par clé (`/keys/<keyname>`).

Pour obtenir la liste de tous les rôles intégrés au HSM managé et les opérations qu’ils autorisent, consultez [Rôles intégrés au HSM managé](built-in-roles.md).

## <a name="prerequisites"></a>Prérequis

Pour utiliser les commandes Azure CLI de cet article, vous devez disposer des éléments suivants :

* Un abonnement à Microsoft Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [essai gratuit](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI version 2.25.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).
* HSM managé dans votre abonnement. Consultez [Démarrage rapide : Provisionner et activer un HSM managé à l’aide d’Azure CLI](quick-create-cli.md) pour provisionner et activer un HSM managé.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper la commande suivante :

```azurecli
az login
```

Pour plus d’informations sur les options de connexion via l’interface CLI, consultez [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli)

## <a name="create-a-new-role-assignment"></a>Créer une attribution de rôle

### <a name="assign-roles-for-all-keys"></a>Attribuer des rôles pour toutes les clés

Utilisez la commande `az keyvault role assignment create` pour attribuer un rôle **Responsable du chiffrement du HSM managé** à l’utilisateur identifié par le nom d’utilisateur principal **user2\@contoso.com** pour toutes les **clés** (étendue`/keys`) dans ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Affecter un rôle pour une clé spécifique

Utilisez la commande `az keyvault role assignment create` pour attribuer un rôle **Responsable du chiffrement du HSM managé** à l’utilisateur identifié par le nom d’utilisateur principal **user2\@contoso.com** pour une clé spécifique nommée **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Lister les attributions de rôles existantes

Utilisez `az keyvault role assignment list` pour lister les attributions de rôles.

Toutes les attributions de rôles au niveau de l’étendue / (valeur par défaut si aucune étendue n’est spécifiée) pour tous les utilisateurs (valeur par défaut quand aucun destinataire n’est spécifié)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Toutes les attributions de rôles au niveau du HSM pour un utilisateur spécifique **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Quand la valeur de l’étendue est / (ou /keys), la commande list liste uniquement toutes les attributions de rôles au niveau supérieur et n’affiche pas les attributions de rôles au niveau des clés individuelles.

Toutes les attributions de rôles pour un utilisateur spécifique **user2@contoso.com** et une clé spécifique **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Une attribution de rôle spécifique pour le rôle **Responsable du chiffrement du HSM managé** pour un utilisateur spécifique **user2@contoso.com** et une clé spécifique **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Supprimer une attribution de rôle

Utilisez la commande `az keyvault role assignment delete` pour supprimer un rôle **Responsable du chiffrement du HSM managé** attribué à l’utilisateur **user2\@contoso.com** pour la clé **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Lister toutes les définitions de rôles disponibles

Utilisez la commande `az keyvault role definition list` pour lister toutes les définitions de rôles.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="create-a-new-role-definition"></a>Créer une définition de rôle

Le module HSM managé a plusieurs rôles intégrés (prédéfinis) qui sont utiles pour les scénarios d’utilisation les plus courants. Vous pouvez définir votre propre rôle avec une liste d’actions spécifiques que celui-ci est autorisé à effectuer. Vous pouvez ensuite attribuer ce rôle à des principaux pour leur accorder l’autorisation sur les actions spécifiées. 

Utilisez la commande `az keyvault role definition create` pour créer un rôle nommé **My Custom Role** avec une chaîne JSON.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
    "roleName": "My Custom Role",
    "description": "The description of the custom rule.",
    "actions": [],
    "notActions": [],
    "dataActions": [
        "Microsoft.KeyVault/managedHsm/keys/read/action"
    ],
    "notDataActions": []
}'
```

Utilisez la commande `az keyvault role definition create` pour créer un rôle à partir d’un fichier nommé **my-custom-role-definition.json** contenant la chaîne JSON d’une définition de rôle. Consultez l'exemple ci-dessus.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition @my-custom-role-definition.json
```

## <a name="show-details-of-a-role-definition"></a>Afficher les détails d’une définition de rôle

Utilisez la commande `az keyvault role definition show` pour afficher les détails d’une définition de rôle spécifique avec un nom (GUID).

```azurecli-interactive
az keyvault role definition show --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="update-a-custom-role-definition"></a>Mettre à jour une définition de rôle personnalisé

Utilisez la commande `az keyvault role definition update` pour mettre à jour un rôle nommé **My Custom Role** avec une chaîne JSON.
```azurecli-interactive
az keyvault role definition create --hsm-name ContosoMHSM --role-definition '{
            "roleName": "My Custom Role",
            "name": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "id": "Microsoft.KeyVault/providers/Microsoft.Authorization/roleDefinitions/xxxxxxxx-
        xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "description": "The description of the custom rule.",
            "actions": [],
            "notActions": [],
            "dataActions": [
                "Microsoft.KeyVault/managedHsm/keys/read/action",
                "Microsoft.KeyVault/managedHsm/keys/write/action",
                "Microsoft.KeyVault/managedHsm/keys/backup/action",
                "Microsoft.KeyVault/managedHsm/keys/create"
            ],
            "notDataActions": []
        }'
```

## <a name="delete-custom-role-definition"></a>Supprimer la définition de rôle personnalisée

Utilisez la commande `az keyvault role definition delete` pour voir les détails d’une définition de rôle spécifique avec un nom (GUID). 
```azurecli-interactive
az keyvault role definition delete --hsm-name ContosoMHSM --name xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

> [!NOTE]
> Les rôles intégrés ne peuvent pas être supprimés. Quand des rôles personnalisés sont supprimés, toutes les attributions de rôles utilisant ce rôle personnalisé deviennent obsolètes.


## <a name="next-steps"></a>Étapes suivantes

- Lisez la présentation du [Contrôle d’accès en fonction du rôle (Azure RBAC)](../../role-based-access-control/overview.md).
- Suivre un tutoriel sur la [gestion du rôle HSM managé](role-management.md)
- Découvrir le [modèle de contrôle d’accès HSM managé](access-control.md)
- Voir tous les [rôles intégrés pour le RBAC local HSM managé](built-in-roles.md)
