---
title: Gérer les rôles dans un espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment accéder à un espace de travail Azure Machine Learning à l’aide du contrôle d’accès en fonction du rôle (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: d36c0ab78f9f96a051e6cb0a53b756c7409ca142
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90893410"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gérer l'accès à un espace de travail Azure Machine Learning

Dans cet article, vous allez apprendre à gérer l’accès à un espace de travail Azure Machine Learning. Le [contrôle d'accès en fonction du rôle Azure (Azure RBAC)](/azure/role-based-access-control/overview) vous permet de gérer l'accès aux ressources Azure. Les utilisateurs de votre instance Azure Active Directory se voient attribuer des rôles spécifiques qui leur permettent d'accéder aux ressources. Azure propose des rôles intégrés et permet de créer des rôles personnalisés.

## <a name="default-roles"></a>Rôles par défaut

Un espace de travail Azure Machine Learning est une ressource Azure. Comme toute autre ressource Azure, la création d'un espace de travail Azure Machine Learning s'accompagne de trois rôles par défaut. Vous pouvez ajouter des utilisateurs à l’espace de travail et leur attribuer un de ces rôles intégrés.

| Role | Niveau d’accès |
| --- | --- |
| **Lecteur** | Actions en lecture seule dans l’espace de travail. Les lecteurs peuvent lister et visualiser des ressources , y compris les informations d'identification du [magasin de données](how-to-access-data.md), dans un espace de travail. Les lecteurs ne peuvent pas créer ou mettre à jour ces ressources. |
| **Contributeur** | Afficher, créer, modifier ou supprimer (le cas échéant) des ressources dans un espace de travail. À titre d'exemple, les contributeurs peuvent créer une expérience, créer ou joindre un cluster de calcul, envoyer une exécution et déployer un service web. |
| **Propriétaire** | Accès total à l’espace de travail, avec possibilité d’afficher, de créer, de modifier ou de supprimer (le cas échéant) des ressources dans un espace de travail. Vous pouvez modifier les attributions de rôles. |
| **Rôle personnalisé** | Vous permet de personnaliser l’accès à des opérations de contrôle ou de plan de données spécifiques au sein d’un espace de travail. Par exemple, l’envoi d’une exécution, la création d’un calcul, le déploiement d’un modèle ou l’inscription d’un jeu de données. |

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

Si vous avez installé l'[interface CLI Azure Machine Learning](reference-azure-machine-learning-cli.md), vous pouvez utiliser des commandes CLI pour attribuer des rôles aux utilisateurs :

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

Azure Machine Learning a des actions intégrées pour de nombreuses opérations et tâches. Pour obtenir une liste complète, consultez [Opérations de fournisseur de ressources Azure](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Créer un rôle personnalisé

Si les rôles intégrés ne suffisent pas, vous pouvez créer des rôles personnalisés. Les rôles personnalisés peuvent disposer d'autorisations en lecture, écriture, suppression et calcul dans cet espace de travail. Vous pouvez rendre le rôle disponible au niveau d’un espace de travail spécifique, d’un groupe de ressources spécifique ou d’un abonnement spécifique.

> [!NOTE]
> Vous devez être propriétaire de la ressource à ce niveau pour créer des rôles personnalisés au sein de cette ressource.

Pour créer un rôle personnalisé, commencez par créer un fichier de définition de rôle JSON spécifiant l’autorisation et l’étendue du rôle. L’exemple suivant définit un rôle personnalisé appelé « Data Scientist Custom » étendu à un niveau spécifique de l’espace de travail :

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
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
> Le rôle personnalisé ci-dessus n’est qu’un exemple ; consultez quelques suggestions de [rôles personnalisés pour le service Azure Machine Learning](#customroles).

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

Pour plus d’informations sur les rôles personnalisés, consultez [Rôles personnalisés Azure](/azure/role-based-access-control/custom-roles). Pour plus d’informations sur les opérations (actions et autres) utilisables avec des rôles personnalisés, consultez [Opérations de fournisseur de ressources](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="frequently-asked-questions"></a>Forum aux questions


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>Q. Quelles sont les autorisations nécessaires pour effectuer certains scénarios courants dans le service Azure Machine Learning ?

Le tableau suivant résume les activités Azure Machine Learning et les autorisations requises pour les exécuter à l’étendue la plus restreinte. Par exemple, si une activité peut être exécutée avec une étendue d’espace de travail (colonne 4), elle fonctionnera également automatiquement pour toutes les étendues supérieures dotées de cette autorisation :

> [!IMPORTANT]
> Tous les chemins d’accès de ce tableau qui commencent par `/` sont des **chemin d'accès relatifs** à `Microsoft.MachineLearningServices/` :

| Activité | Étendue de niveau abonnement | Étendue de niveau groupe de ressources | Étendue de niveau espace de travail |
| ----- | ----- | ----- | ----- |
| Créer un espace de travail | Non requis | Propriétaire ou contributeur | s.o. (devient Propriétaire ou hérite d’un rôle d’étendue supérieur après la création) |
| Demander un quota Amlcompute au niveau de l’abonnement ou définir le quota au niveau de l’espace de travail | Propriétaire, contributeur ou rôle personnalisé </br>autorisant `/locations/updateQuotas/action`</br> dans l’étendue de l’abonnement | Non autorisé | Non autorisé |
| Créer un cluster de calcul | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `/workspaces/computes/write` |
| Créer une instance de calcul | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `/workspaces/computes/write` |
| Envoi de tout type d’exécution | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publication d’un point de terminaison de pipeline | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Déploiement d’un modèle inscrit sur une ressource AKS/ACI | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Scoring par rapport à un point de terminaison AKS déployé | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (lorsque vous n’utilisez pas l’authentification Azure Active Directory) OU `"/workspaces/read"` (lorsque vous utilisez l’authentification par jeton) |
| Accès au stockage à l’aide de notebooks interactifs | Non requis | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| Créer un nouveau rôle personnalisé | Propriétaire, contributeur ou rôle personnalisé autorisant `Microsoft.Authorization/roleDefinitions/write` | Non requis | Propriétaire, contributeur ou rôle personnalisé autorisant : `/workspaces/computes/write` |

> [!TIP]
> Si vous recevez un message d’erreur lors de la tentative de création d’un espace de travail pour la première fois, assurez-vous que votre rôle autorise `Microsoft.MachineLearningServices/register/action`. Cette action vous permet d’inscrire le fournisseur de ressources Azure Machine Learning auprès de votre abonnement Azure.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>Q. Publiez-nous des rôles intégrés Azure pour le service Machine Learning ?

Nous ne publions pas actuellement de [rôles intégrés Azure](/azure/role-based-access-control/built-in-roles) pour le service Machine Learning. Une fois publié, un rôle intégré ne peut pas être mis à jour, et nous confirmons toujours les définitions de rôle en fonction des scénarios et des commentaires des clients. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>Q. Existe-t-il des modèles de rôles personnalisés pour les scénarios les plus courants dans le service Machine Learning ?

Oui. Voici quelques scénarios courants avec des définitions de rôle proposées personnalisées que vous pouvez utiliser comme base pour définir vos propres rôles personnalisés :

* __Data Scientist Custom__ : Permet à un scientifique des données d’effectuer toutes les opérations à l’intérieur d’un espace de travail, **sauf** :

    * Création de calculs
    * Déploiement de modèles sur un cluster AKS de production
    * Déploiement d’un point de terminaison de pipeline en production

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Data Scientist Restricted Custom__ : Définition de rôle plus restreinte sans caractères génériques dans les actions autorisées. Peut effectuer toutes les opérations à l’intérieur d’un espace de travail, **sauf** :

    * Création de calculs
    * Déploiement de modèles sur un cluster AKS de production
    * Déploiement d’un point de terminaison de pipeline en production

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __MLOps Custom__ : Vous permet d’attribuer un rôle à un principal de service et de l’utiliser pour automatiser vos pipelines MLOps. Par exemple, pour envoyer des exécutions sur un pipeline déjà publié :

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Administrateur de l’espace de service__ : Vous permet d’effectuer toutes les opérations dans l’étendue d’un espace de travail, **sauf** :

    * Créer un espace de travail
    * Attribution de quotas au niveau d’un d’abonnement ou d’un espace de travail

    L’administrateur de l’espace de travail ne peut pas non plus créer un nouveau rôle. Il peut uniquement attribuer des rôles intégrés ou personnalisés existants au sein de l’étendue de son espace de travail :

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler Custom__ : Vous permet de définir un rôle dédié uniquement à l’étiquetage des données :

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>Q. Comment répertorier tous les rôles personnalisés dans mon abonnement ?

Dans Azure CLI, exécutez la commande suivante.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>Q. Comment identifier les opérations prises en charge par le service Machine Learning ?

Dans Azure CLI, exécutez la commande suivante.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Elles se trouvent également dans la liste des [opérations du fournisseur de ressources](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>Q. Quels sont les problèmes les plus courants concernant l’utilisation d’Azure RBAC ?

Voici quelques éléments à prendre en compte lorsque vous utilisez le contrôle d’accès en fonction du rôle Azure (Azure RBAC) :

- Lorsque vous créez une ressource dans Azure, par exemple un espace de travail, vous n’êtes pas directement le propriétaire de cet espace de travail. Votre rôle est hérité du rôle étendue le plus élevé auquel vous êtes autorisé dans cet abonnement. Par exemple, si vous êtes un administrateur réseau et disposez des autorisations nécessaires pour créer un espace de travail Machine Learning, le rôle Administrateur réseau sera attribué à cet espace de travail, et non au rôle Propriétaire.
- Si deux rôles ont été attribués au même utilisateur Azure Active Directory avec des sections conflictuelles de type Actions/NotActions, vos opérations répertoriées dans NotActions pour un rôle risquent de ne pas s’appliquer si elles apparaissent également en tant que Actions dans un autre rôle. Pour en savoir plus sur la façon dont Azure analyse les attributions de rôles, lisez [Comment le contrôle RBAC Azure détermine si un utilisateur a accès à une ressource](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- Pour déployer vos ressources de calcul à l’intérieur d’un réseau virtuel, vous devez disposer d’autorisations explicites pour les actions suivantes :
    - « Microsoft.Network/virtualNetworks/join/action » sur la ressource de réseau virtuel.
    - « Microsoft.Network/virtualNetworks/subnet/join/action » sur la ressource de sous-réseau virtuel.
    
    Pour plus d’informations sur RBAC avec la mise en réseau, consultez [Rôles intégrés pour la mise en réseau](/azure/role-based-access-control/built-in-roles#networking).

- Il peut s’écouler parfois jusqu’à 1 heure avant que vos nouvelles attributions de rôles ne soient appliquées auxx autorisations mises en cache dans la pile.

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>Q. De quelles autorisations ai-je besoin pour utiliser une identité managée affectée par l’utilisateur avec mes clusters Amlcompute ?

Pour attribuer une identité affectée à l’utilisateur sur des clusters Amlcompute, vous devez disposer d’autorisations en écriture pour créer des calculs et d’un [rôle d’opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Pour plus d’informations sur RBAC avec des identités managées, consultez [Comment gérer une identité attribuée par l’utilisateur](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>Q. Le contrôle d’accès en fonction du rôle est-il pris en charge sur le portail Studio ?

Azure Machine Learning Studio prend en charge le contrôle d’accès en fonction du rôle Azure (Azure RBAC). 

> [!IMPORTANT]
> Une fois que vous avez attribué un rôle personnalisé avec des autorisations spécifiques à un scientifique de données dans votre espace de travail, les actions correspondantes (telles que l’ajout d’un bouton de calcul) sont automatiquement masquées pour les utilisateurs. Le masquage de ces éléments empêche toute confusion pouvant survenir si des contrôles renvoient une notification d’accès non autorisée à partir du service lorsqu’ils sont utilisés.

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


### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>Q. Quelles sont les autorisations nécessaires pour effectuer des opérations de quota dans un espace de travail ? 

Vous avez besoin d’autorisations au niveau de l’abonnement pour effectuer une opération liée aux quotas dans l’espace de travail. Cela signifie que le paramétrage d’un quota au niveau de l’abonnement ou au niveau de l’espace de travail pour vos ressources de calcul managées ne peut se faire que si vous disposez d’autorisations d’écriture dans l’étendue de l’abonnement. 


## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de la sécurité d'entreprise](concept-enterprise-security.md)
- [Vue d’ensemble de l’isolement et de la confidentialité des réseaux virtuels](how-to-network-security-overview.md)
- [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
- [Opérations de fournisseur de ressources](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
