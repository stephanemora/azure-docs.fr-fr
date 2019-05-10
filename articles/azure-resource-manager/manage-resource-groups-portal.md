---
title: Gérer des groupes d’Azure Resource Manager à l’aide du portail Azure | Microsoft Docs
description: Utilisez le portail Azure pour gérer vos groupes d’Azure Resource Manager.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 559c1874c119eabef2c35a954961c1e669df3c06
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507224"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Gérer des groupes de ressources Azure Resource Manager à l’aide du portail Azure

Découvrez comment utiliser le [Azure portal](https://portal.azure.com) avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos groupes de ressources Azure. Pour la gestion des ressources Azure, consultez [gérer les ressources Azure à l’aide du portail Azure](./manage-resources-portal.md).

Autres articles sur la gestion des groupes de ressources :

- [Gérer des groupes de ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md)
- [Gérer des groupes de ressources Azure à l’aide d’Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>Qu'est-ce qu'un groupe de ressources

Un groupe de ressources est un conteneur qui inclut les ressources associées à une solution Azure. Le groupe de ressources peut inclure toutes les ressources de la solution, ou uniquement celles que vous souhaitez gérer en tant que groupe. Pour déterminer comment allouer des ressources aux groupes de ressources, choisissez l’approche la plus pertinente pour votre organisation. En règle générale, il convient d’ajouter des ressources qui partagent le même cycle de vie dans un même groupe de ressources afin de pouvoir facilement les déployer, les mettre à jour et les supprimer en tant que groupe.

Le groupe de ressources stocke des métadonnées sur les ressources. Par conséquent, lorsque vous spécifiez un emplacement pour le groupe de ressources, vous indiquez où stocker ces métadonnées. Pour des raisons de conformité, vous devrez peut-être vous assurer que vos données sont stockées dans une région particulière.

Le groupe de ressources stocke des métadonnées sur les ressources. Lorsque vous spécifiez un emplacement pour le groupe de ressources, vous spécifiez où se trouve que les métadonnées.

## <a name="create-resource-groups"></a>Créer des groupes de ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **groupes de ressources**

    ![ajouter un groupe de ressources](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. Sélectionnez **Ajouter**.
4. Saisissez les valeurs suivantes :

   - **Abonnement**: Sélectionnez votre abonnement Azure. 
   - **Groupe de ressources** : Entrez un nouveau nom de groupe de ressources. 
   - **Région** : Sélectionnez un emplacement Azure, tel que **centre des États-Unis**.

     ![Créer un groupe de ressources](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. Sélectionnez **vérifier + créer**
6. Sélectionnez **Créer**. Il faut quelques secondes pour créer un groupe de ressources.
7. Sélectionnez **Actualiser** dans le menu supérieur pour actualiser la liste de groupes de ressources, puis sélectionnez le groupe de ressources nouvellement créé pour l’ouvrir. Ou sélectionnez **Notification**(l’icône représentant une cloche) dans le haut, puis sélectionnez **accédez au groupe de ressources** pour ouvrir le groupe de ressources nouvellement créé

    ![Accédez au groupe de ressources](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>Répertorier les groupes de ressources

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Pour répertorier les groupes de ressources, sélectionnez **groupes de ressources**

    ![parcourir les groupes de ressources](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. Pour personnaliser les informations affichées pour les groupes de ressources, sélectionnez **modifier les colonnes**. La capture d’écran suivante montre les colonnes de plus que vous pouvez ajouter à l’affichage :

## <a name="open-resource-groups"></a>Groupes de ressources Open

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Sélectionnez **Groupes de ressources**.
3. Sélectionnez le groupe de ressources que vous souhaitez ouvrir.

## <a name="delete-resource-groups"></a>Supprimer des groupes de ressources

1. Ouvrez le groupe de ressources que vous souhaitez supprimer.  Consultez [ouvrir des groupes de ressources](#open-resource-groups).
2. Sélectionnez **Supprimer le groupe de ressources**.

    ![delete azure resource group](./media/manage-resource-groups-portal/delete-group.png)

Pour plus d’informations sur la façon dont Azure Resource Manager trie la suppression des ressources, consultez [suppression du groupe de ressources Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-a-resource-group"></a>Déployer des ressources dans un groupe de ressources

Une fois que vous avez créé un modèle Resource Manager, vous pouvez utiliser le portail Azure pour déployer vos ressources Azure. Pour créer un modèle, consultez [Guide de démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Pour déployer un modèle à l’aide du portail, consultez [déployer des ressources avec des modèles Resource Manager et portail Azure](resource-group-template-deploy-portal.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Déplacer vers un autre groupe de ressources ou abonnement

Vous pouvez déplacer les ressources dans le groupe vers un autre groupe de ressources. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Groupes de ressources de verrouillage

Le verrouillage empêche les autres utilisateurs de votre organisation de supprimer ou modifier des ressources critiques, telles que l’abonnement Azure, groupe de ressources ou ressource accidentellement. 

1. Ouvrez le groupe de ressources que vous souhaitez supprimer.  Consultez [ouvrir des groupes de ressources](#open-resource-groups).
2. Dans le volet gauche, sélectionnez **verrouille**.
3. Pour ajouter un verrou au groupe de ressources, sélectionnez **ajouter**.
4. Entrez **nom du verrou**, **type de verrou**, et **Notes**. Incluent les types de verrou **en lecture seule**, et **supprimer**.

    ![groupe de Ressources azure de verrou](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

Pour plus d’informations, consultez [verrouiller les ressources pour empêcher les modifications inattendues](./resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Baliser des groupes de ressources

Vous pouvez appliquer des balises à des groupes de ressources pour organiser logiquement vos ressources. Pour plus d’informations, consultez [à l’aide de balises pour organiser vos ressources Azure](./resource-group-using-tags.md#portal).

## <a name="export-resource-groups-to-templates"></a>Exporter les groupes de ressources à des modèles

Pour plus d’informations sur l’exportation de modèles, consultez [exportation unique et plusieurs ressource de modèle - portail](export-template-portal.md).

### <a name="fix-export-issues"></a>Résoudre les problèmes d’exportation

Tous les types de ressources prennent en charge la fonction de modèle d’exportation. Vous rencontrerez des problèmes d’exportation uniquement lors de l’exportation à partir d’un groupe de ressources et non à partir de votre historique de déploiement. Si votre dernier déploiement représente précisément l’état actuel du groupe de ressources, vous devez exporter le modèle à partir de l’historique de déploiement, plutôt que depuis le groupe de ressources. Procédez à une exportation à partir d’un groupe de ressources lorsque vous avez apporté des modifications au groupe de ressources qui ne sont pas définies dans un modèle unique.

Pour contourner ces problèmes d’exportation, ajoutez manuellement les ressources manquantes à votre modèle. Le message d’erreur inclut les types de ressources qui ne peuvent pas être exportés. Recherchez le type de ressource dans [Référence de modèle](/azure/templates/). Par exemple, pour ajouter manuellement une passerelle de réseau virtuel, consultez [Référence de modèle Microsoft.Network/virtualNetworkGateways](/azure/templates/microsoft.network/virtualnetworkgateways). La référence de modèle vous donne le JSON permettant d’ajouter la ressource à votre modèle.

Une fois le format JSON obtenu pour la ressource, vous devez récupérer les valeurs de la ressource. Vous pouvez voir les valeurs de la ressource à l’aide de l’opération dGET de l’API REST pour le type de ressource. Par exemple, pour obtenir les valeurs de votre passerelle de réseau virtuel, consultez [Passerelles de réseau virtuel - Obtenir](/rest/api/network-gateway/virtualnetworkgateways/get).

## <a name="manage-access-to-resource-groups"></a>Gérer l’accès aux groupes de ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide de RBAC et du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’Azure Resource Manager, consultez [présentation d’Azure Resource Manager](./resource-group-overview.md).
- Pour découvrir la syntaxe du modèle Resource Manager, consultez [comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment développer des modèles, consultez le [didacticiels pas à pas](/azure/azure-resource-manager/).
- Pour afficher les schémas de modèle Azure Resource Manager, consultez [référence de modèle](/azure/templates/).