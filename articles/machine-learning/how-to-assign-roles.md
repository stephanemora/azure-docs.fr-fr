---
title: Gérer les rôles dans un espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment accéder à un espace de travail Azure Machine Learning à l’aide du contrôle d’accès en fonction du rôle (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: f289be1b3432d9c62b4841c513088afa16e0e447
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609246"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gérer l'accès à un espace de travail Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à gérer l’accès à un espace de travail Azure Machine Learning. Le [contrôle d'accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview) vous permet de gérer l'accès aux ressources Azure. Les utilisateurs de votre instance Azure Active Directory se voient attribuer des rôles spécifiques qui leur permettent d'accéder aux ressources. Azure propose des rôles intégrés et permet de créer des rôles personnalisés.

## <a name="default-roles"></a>Rôles par défaut

Un espace de travail Azure Machine Learning est une ressource Azure. Comme toute autre ressource Azure, la création d'un espace de travail Azure Machine Learning s'accompagne de trois rôles par défaut. Vous pouvez ajouter des utilisateurs à l’espace de travail et leur attribuer un de ces rôles intégrés.

| Role | Niveau d’accès |
| --- | --- |
| **Lecteur** | Actions en lecture seule dans l’espace de travail. Les lecteurs peuvent lister et visualiser des ressources (y compris [magasin de données](how-to-access-data.md)) dans un espace de travail, mais pas créer ou mettre à jour ces ressources. |
| **Contributeur** | Afficher, créer, modifier ou supprimer (le cas échéant) des ressources dans un espace de travail. À titre d'exemple, les contributeurs peuvent créer une expérience, créer ou joindre un cluster de calcul, envoyer une exécution et déployer un service web. |
| **Propriétaire** | Accès total à l’espace de travail, avec possibilité d’afficher, de créer, de modifier ou de supprimer (le cas échéant) des ressources dans un espace de travail. Vous pouvez modifier les attributions de rôles. |

> [!IMPORTANT]
> L'accès en fonction du rôle peut être limité à plusieurs niveaux dans Azure. Par exemple, un utilisateur disposant d’un accès propriétaire à un espace de travail peut ne pas disposer d'un accès propriétaire à un groupe de ressources contenu dans cet espace de travail. Pour plus d’informations, consultez [Fonctionnement du le contrôle d’accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview#how-rbac-works).

Pour plus d’informations sur les rôles intégrés spécifiques, consultez [Rôles intégrés pour Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gérer les accès à l’espace de travail

En tant que propriétaire d’un espace de travail, vous pouvez ajouter et supprimer des rôles pour cet espace de travail. Vous pouvez également attribuer des rôles aux utilisateurs. Utilisez les liens suivants pour savoir comment gérer les accès :
- [Interface utilisateur du portail Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Modèles Microsoft Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Si vous avez installé l'[interface CLI Azure Machine Learning](reference-azure-machine-learning-cli.md), vous pouvez également utiliser une commande CLI pour attribuer des rôles aux utilisateurs.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Le champ `user` correspond à l’adresse e-mail d’un utilisateur présent dans l’instance Azure Active Directory où se trouve l’abonnement parent de l'espace de travail. L’exemple suivant montre comment utiliser cette commande :

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> La commande « az ml workspace share » ne fonctionne pas pour un compte fédéré par Azure Active Directory B2B. Utilisez le portail de l’interface utilisateur Azure à la place de la commande.


## <a name="azure-machine-learning-operations"></a>Opérations d’Azure Machine Learning

Azure Machine Learning a des actions intégrées pour de nombreuses opérations et tâches. Pour obtenir une liste complète, consultez [Opérations des fournisseurs de ressources Azure](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Créer un rôle personnalisé

Si les rôles intégrés ne suffisent pas, vous pouvez créer des rôles personnalisés. Les rôles personnalisés peuvent disposer d'autorisations en lecture, écriture, suppression et calcul dans cet espace de travail. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique.

> [!NOTE]
> Vous devez être propriétaire de la ressource à ce niveau pour créer des rôles personnalisés au sein de cette ressource.

Pour créer un rôle personnalisé, commencez par créer un fichier de définition de rôle JSON spécifiant l’autorisation et l’étendue du rôle. L’exemple suivant définit un rôle personnalisé appelé « Scientifique des données » étendu à un niveau spécifique de l’espace de travail :

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Vous pouvez modifier le champ `AssignableScopes` pour définir l’étendue de ce rôle personnalisé au niveau de l’abonnement, du groupe de ressources ou d'un espace de travail spécifique.

Ce rôle personnalisé peut tout faire dans l'espace de travail, à l'exception de ce qui suit :

- Il ne peut pas créer ou mettre à jour une ressource de calcul.
- Il ne peut pas supprimer une ressource de calcul.
- Il ne peut pas ajouter, supprimer ou modifier des attributions de rôles.
- Il ne peut pas supprimer l’espace de travail.

Pour déployer ce rôle personnalisé, exécutez la commande Azure CLI suivante :

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Une fois déployé, ce rôle est disponible dans l’espace de travail spécifié. Vous pouvez à présent ajouter et attribuer ce rôle dans le portail Azure. Vous pouvez également attribuer ce rôle à un utilisateur à l’aide de la commande CLI `az ml workspace share` suivante :

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Pour en savoir plus sur les rôles personnalisés, voir [Rôles personnalisés pour les ressources Azure](/azure/role-based-access-control/custom-roles).

## <a name="frequently-asked-questions"></a>Forum aux questions


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>Q. Quelles sont les autorisations nécessaires pour effectuer diverses actions dans l’Azure Machine Learning service ?

Le tableau suivant résume les activités Azure Machine Learning et les autorisations requises pour les exécuter à l’étendue la plus restreinte. Par exemple, si une activité peut être exécutée avec une étendue d’espace de travail (colonne 4), elle fonctionnera également automatiquement pour toutes les étendues supérieures dotées de cette autorisation. Tous les chemins d’accès de ce tableau sont des **chemins d’accès relatifs** à `Microsoft.MachineLearningServices/`.

| Activité | Étendue de niveau abonnement | Étendue de niveau groupe de ressources | Étendue de niveau espace de travail |
|---|---|---|---|
| Créer un espace de travail | Non requis | Propriétaire ou contributeur | s.o. (devient Propriétaire ou hérite d’un rôle d’étendue supérieur après la création) |
| Créer un cluster de calcul | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `workspaces/computes/write` |
| Créer une machine virtuelle Notebook | Non requis | Propriétaire ou contributeur | Impossible |
| Créer une instance de calcul | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `workspaces/computes/write` |
| Activité de plan de données comme l’envoi d’une exécution, l’accès aux données, le déploiement d’un modèle ou la publication d’un pipeline | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `workspaces/*/write` <br/> Vous avez également besoin d’un magasin de données inscrit dans l’espace de travail pour autoriser MSI à accéder aux données de votre compte de stockage. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Comment répertorier tous les rôles personnalisés dans mon abonnement ?

Dans Azure CLI, exécutez la commande suivante.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>Q. Comment rechercher la définition d’un rôle dans mon abonnement ?

Dans Azure CLI, exécutez la commande suivante. `<role-name>` doit être au même format que celui retourné par la commande ci-dessus.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>Q. Comment mettre à jour une définition de rôle ?

Dans Azure CLI, exécutez la commande suivante.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Vous devez disposer d’autorisations sur l’ensemble de l’étendue de votre nouvelle définition de rôle. Par exemple, si ce nouveau rôle a une étendue sur trois abonnements, vous devez disposer d’autorisations sur les trois abonnements. 

> [!NOTE]
> Les mises à jour de rôle peuvent prendre entre 15 minutes et 1 heure pour s’appliquer à toutes les attributions de rôles de cette étendue.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>Q. Puis-je définir un rôle qui empêche la mise à jour de l’édition de l’espace de travail ? 

Oui, vous pouvez définir un rôle qui empêche la mise à jour de l’édition de l’espace de travail. Étant donné que la mise à jour de l’espace de travail est un appel PATCH sur l’objet de l’espace de travail, vous effectuez cette opération en plaçant l’action suivante dans le tableau `"NotActions"` de votre définition JSON : 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quelles sont les autorisations nécessaires pour effectuer des opérations de quota dans un espace de travail ? 

Vous avez besoin d’autorisations au niveau de l’abonnement pour effectuer une opération liée aux quotas dans l’espace de travail. Cela signifie que le paramétrage d’un quota au niveau de l’abonnement ou au niveau de l’espace de travail pour vos ressources de calcul managées ne peut se faire que si vous disposez d’autorisations d’écriture dans l’étendue de l’abonnement. 


## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sécurité d'entreprise](concept-enterprise-security.md)
- [Exécuter en toute sécurité des expériences et des inférences/scores dans un réseau virtuel](how-to-enable-virtual-network.md)
- [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
- [Opérations de fournisseur de ressources](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)