---
title: Gérer les rôles dans un espace de travail Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Découvrez comment accéder à un espace de travail de service Azure Machine Learning à l’aide du contrôle d’accès en fonction du rôle (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 02/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2410787c3307136f827ea7fc34df9a545a59cbf4
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850902"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gérer l’accès à un espace de travail Azure Machine Learning

Dans cet article, vous allez apprendre à gérer l’accès à un espace de travail Azure Machine Learning. [Contrôle d’accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview) est utilisé pour gérer l’accès aux ressources Azure. Les utilisateurs dans votre Azure Active Directory sont affectés des rôles spécifiques, qui octroient l’accès aux ressources. Azure fournit des rôles intégrés et la possibilité de créer des rôles personnalisés.

## <a name="default-roles"></a>Rôles par défaut

Un espace de travail Azure Machine Learning est une ressource Azure. Comme les autres ressources Azure, lorsqu’un nouvel espace de travail Azure Machine Learning est créé, il est fourni avec trois rôles par défaut. Vous pouvez ajouter des utilisateurs à l’espace de travail et les affecter à un de ces rôles prédéfinis.

| Rôle | Niveau d'accès |
| --- | --- |
| **Lecteur** | Actions en lecture seule dans l’espace de travail. Lecteurs peuvent répertorier et afficher les ressources dans un espace de travail, mais ne peut pas créer ou mettre à jour de ces ressources. |
| **Contributeur** | Afficher, créer, modifier ou supprimer (le cas échéant) des ressources dans un espace de travail. Par exemple, contributeurs peuvent créer une expérience, créer ou joindre un cluster de calcul, envoyer une exécution et déployer un service web. |
| **Propriétaire** | Accès complet à l’espace de travail, y compris la possibilité d’afficher, créer, modifier ou supprimer (le cas échéant) des ressources dans un espace de travail. En outre, vous pouvez modifier les attributions de rôles. |

> [!IMPORTANT]
> Accès de rôle peut être limité à plusieurs niveaux dans Azure. Par exemple, une personne disposant d’un accès propriétaire à un espace de travail peut-être pas accès propriétaire à un groupe de ressources qui contient l’espace de travail. Pour plus d’informations, consultez [RBAC comment fonctionne](/azure/role-based-access-control/overview#how-rbac-works).

Pour plus d’informations sur les rôles intégrés spécifiques, consultez [rôles intégrés pour Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gérer l’accès de l’espace de travail

Si vous êtes propriétaire d’un espace de travail, vous pouvez ajouter et supprimer des rôles pour l’espace de travail. Vous pouvez également affecter des rôles aux utilisateurs. Utilisez les liens suivants pour découvrir comment gérer l’accès :
- [Interface utilisateur du portail Azure](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interface de ligne de commande Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modèles Microsoft Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Si vous avez installé le [CLI Azure Machine Learning](reference-azure-machine-learning-cli.md), vous pouvez également utiliser une commande CLI pour affecter des rôles aux utilisateurs.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Le `user` champ est l’adresse de messagerie d’un utilisateur existant dans l’instance d’Azure Active Directory où se trouve l’abonnement de parent d’espace de travail. Voici un exemple montrant comment utiliser cette commande :

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Créer un rôle personnalisé

Si les rôles intégrés sont insuffisantes, vous pouvez créer des rôles personnalisés. Rôles personnalisés peut-être lire, écrire, supprimer et les autorisations de ressources dans cet espace de travail de calcul. Vous pouvez rendre disponible à un niveau de l’espace de travail spécifique, un niveau de groupe de ressources spécifique ou un niveau d’abonnement spécifique.

> [!NOTE]
> Vous devez être propriétaire de la ressource à ce niveau pour créer des rôles personnalisés au sein de cette ressource.

Pour créer un rôle personnalisé, tout d’abord construire un fichier JSON de définition de rôle qui spécifie l’autorisation et l’étendue pour le rôle. L’exemple suivant définit un rôle personnalisé appelé « Scientifique des données « étendue à un niveau de l’espace de travail spécifique :

`data_scientist_role.json` :
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

Vous pouvez modifier le `AssignableScopes` champ pour définir l’étendue de ce rôle personnalisé au niveau de l’abonnement, le niveau de groupe de ressources ou un niveau de l’espace de travail spécifique.

Ce rôle personnalisé peut tout faire de l’espace de travail à l’exception des actions suivantes :

- Il ne peut pas créer ou mettre à jour une ressource de calcul.
- Il ne peut pas supprimer une ressource de calcul.
- Il ne peut pas ajouter, supprimer ou modifier des attributions de rôles.
- Il ne peut pas supprimer l’espace de travail.

Pour déployer ce rôle personnalisé, utilisez la commande d’Azure CLI suivante :

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Après le déploiement, ce rôle devient disponible dans l’espace de travail spécifié. Vous pouvez désormais ajouter et attribuer ce rôle dans le portail Azure. Ou, vous pouvez attribuer ce rôle à un utilisateur à l’aide de la `az ml workspace share` commande CLI :

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Pour plus d’informations, consultez [rôles personnalisés pour les ressources Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sécurité Enterprise](concept-enterprise-security.md)
- [Exécuter en toute sécurité des expériences et inférence/score à l’intérieur d’un réseau virtuel](how-to-enable-virtual-network.md)
- [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
