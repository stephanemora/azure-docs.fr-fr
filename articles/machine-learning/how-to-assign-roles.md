---
title: Gérer les rôles dans un espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment accéder à un espace de travail Azure Machine Learning à l’aide du contrôle d’accès en fonction du rôle (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: aba613911328b1272ebb07eeae633932cb4a442f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935352"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gérer l'accès à un espace de travail Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à gérer l’accès à un espace de travail Azure Machine Learning. Le [contrôle d'accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview) vous permet de gérer l'accès aux ressources Azure. Les utilisateurs de votre instance Azure Active Directory se voient attribuer des rôles spécifiques qui leur permettent d'accéder aux ressources. Azure propose des rôles intégrés et permet de créer des rôles personnalisés.

## <a name="default-roles"></a>Rôles par défaut

Un espace de travail Azure Machine Learning est une ressource Azure. Comme toute autre ressource Azure, la création d'un espace de travail Azure Machine Learning s'accompagne de trois rôles par défaut. Vous pouvez ajouter des utilisateurs à l’espace de travail et leur attribuer un de ces rôles intégrés.

| Role | Niveau d’accès |
| --- | --- |
| **Lecteur** | Actions en lecture seule dans l’espace de travail. Les lecteurs peuvent répertorier et afficher des ressources dans un espace de travail, mais pas créer ou mettre à jour ces ressources. |
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

Vous pouvez modifier le champ `AssignableScopes` pour définir l’étendue de ce rôle personnalisé au niveau de l’abonnement, du groupe de ressources ou d'un espace de travail spécifique.

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

Pour plus d’informations sur les opérations (actions) utilisables avec des rôles personnalisés, consultez [Opérations de fournisseur de ressources](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sécurité d'entreprise](concept-enterprise-security.md)
- [Exécuter en toute sécurité des expériences et des inférences/scores dans un réseau virtuel](how-to-enable-virtual-network.md)
- [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
- [Opérations de fournisseur de ressources](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)