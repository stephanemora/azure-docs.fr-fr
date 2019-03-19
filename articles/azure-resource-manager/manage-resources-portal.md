---
title: Gérer les ressources Azure à l’aide du portail Azure | Microsoft Docs
description: Utilisez le portail Azure et Azure Resource Manager pour gérer vos ressources.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56824984"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Gérer les ressources Azure à l’aide du portail Azure

Découvrez comment utiliser le [Azure portal](https://portal.azure.com) avec [Azure Resource Manager](resource-group-overview.md) pour gérer vos ressources Azure. Pour la gestion des groupes de ressources, consultez [des groupes de ressources Azure de gérer à l’aide du portail Azure](./manage-resource-groups-portal.md).

Autres articles sur la gestion des ressources :

- [Gérer les ressources Azure à l’aide d’Azure CLI](./manage-resources-cli.md)
- [Gérer les ressources Azure à l’aide d’Azure PowerShell](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>Déployer des ressources dans un groupe de ressources

Une fois que vous avez créé un modèle Resource Manager, vous pouvez utiliser le portail Azure pour déployer vos ressources Azure. Pour créer un modèle, consultez [Guide de démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Pour déployer un modèle à l’aide du portail, consultez [déployer des ressources avec des modèles Resource Manager et portail Azure](resource-group-template-deploy-portal.md).

## <a name="open-resources"></a>Ressources ouvertes

Ressources Azure sont organisées par les services Azure et groupes de ressources. Les procédures ci-dessous montre comment ouvrir un compte de stockage nommé **mystorage0207**. La machine virtuelle réside dans un groupe de ressources appelé **mystorage0207rg**.

Pour ouvrir une ressource par le type de service :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le volet gauche, sélectionnez le service Azure. Dans ce cas, **comptes de stockage**.  Si vous ne voyez pas le service répertorié, sélectionnez **tous les services**, puis sélectionnez le type de service.

    ![Ouvrez la ressource azure dans le portail](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. Sélectionnez la ressource que vous souhaitez ouvrir.

    ![Ouvrez la ressource azure dans le portail](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    Ressemble à un compte de stockage :

    ![Ouvrez la ressource azure dans le portail](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

Pour ouvrir un groupe de ressources par ressource :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le volet gauche, sélectionnez **groupes de ressources** pour répertorier les ressources au sein du groupe.
3. Sélectionnez la ressource que vous souhaitez ouvrir. 

## <a name="manage-resources"></a>Gestion des ressources

Lorsque vous visualisez une ressource dans le portail, les options de gestion de cette ressource particulière s’affichent.

![gérer les ressources Azure](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

La capture d’écran montre les options de gestion pour une machine virtuelle Azure. Vous pouvez effectuer des opérations telles que démarrage, le redémarrage et l’arrêt d’une machine virtuelle.

## <a name="delete-resources"></a>Supprimer des ressources

1. Ouvrez la ressource dans le portail. Pour connaître les étapes, consultez [ouvrir ressources](#open-resources).
2. Sélectionnez **Supprimer**. La capture d’écran suivante montre les options de gestion d’une machine virtuelle.

    ![supprimer les Ressources azure](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. Tapez le nom de la ressource pour confirmer la suppression, puis sélectionnez **supprimer**.

Pour plus d’informations sur la façon dont Azure Resource Manager trie la suppression des ressources, consultez [suppression du groupe de ressources Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Déplacer des ressources

1. Ouvrez la ressource dans le portail. Pour connaître les étapes, consultez [ouvrir ressources](#open-resources).
2. Sélectionnez **Déplacer**. La capture d’écran suivante montre les options de gestion pour un compte de stockage.

    ![déplacer des Ressources azure](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. Sélectionnez **déplacer vers un autre groupe de ressources** ou **Moeve vers un autre abonnement** selon vos besoins.

Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](resource-group-move-resources.md).

## <a name="lock-resources"></a>Verrouiller des ressources

Le verrouillage empêche les autres utilisateurs de votre organisation de supprimer ou modifier des ressources critiques, telles que l’abonnement Azure, groupe de ressources ou ressource accidentellement. 

1. Ouvrez la ressource dans le portail. Pour connaître les étapes, consultez [ouvrir ressources](#open-resources).
2. Sélectionnez **verrouille**. La capture d’écran suivante montre les options de gestion pour un compte de stockage.

    ![ressource de verrou azure](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. Sélectionnez **ajouter**, puis spécifiez les propriétés de verrous.

Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Baliser des ressources

Balisage vous aide à organiser votre groupe de ressources et les ressources logiquement. 

1. Ouvrez la ressource dans le portail. Pour connaître les étapes, consultez [ouvrir ressources](#open-resources).
2. Sélectionnez **balises**. La capture d’écran suivante montre les options de gestion pour un compte de stockage.

    ![ressources de balise azure](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. Spécifiez les propriétés de la balise, puis sélectionnez **enregistrer**.

Pour plus d’informations, consultez [à l’aide de balises pour organiser vos ressources Azure](./resource-group-using-tags.md#portal).

## <a name="monitor-resources"></a>Surveiller les ressources

Lorsque vous ouvrez une ressource, le portail présente les graphiques par défaut et les tables pour la surveillance de ce type de ressource. La capture d’écran suivante montre les graphiques pour une machine virtuelle :

![ressources d’analyse azure](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

Vous pouvez sélectionner l’icône d’épingle dans le coin supérieur droit des graphiques pour épingler le graphique au tableau de bord. Pour plus d’informations sur l’utilisation des tableaux de bord, consultez [Création et partage de tableaux de bord sur le Portail Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="manage-access-to-resources"></a>Gérer l'accès aux ressources

Le [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/overview.md) est la façon dont vous gérez l’accès aux ressources dans Azure. Pour plus d’informations, consultez [Gérer l’accès à l’aide de RBAC et du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’Azure Resource Manager, consultez [présentation d’Azure Resource Manager](./resource-group-overview.md).
- Pour découvrir la syntaxe du modèle Resource Manager, consultez [comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment développer des modèles, consultez le [didacticiels pas à pas](/azure/azure-resource-manager/).
- Pour afficher les schémas de modèle Azure Resource Manager, consultez [référence de modèle](/azure/templates/).