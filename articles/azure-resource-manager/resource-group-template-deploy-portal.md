---
title: Utiliser le Portail Azure pour déployer des ressources Azure | Microsoft Docs
description: Utilisez le Portail Azure et Azure Resource Manager pour déployer vos ressources.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 7b28129a3afe9f78d0ef749fa0c7759082c5f758
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652448"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Déployer des ressources à l’aide de modèles Resource Manager et du Portail Azure

Cet article montre comment utiliser le [Portail Azure](https://portal.azure.com) avec [Azure Resource Manager](resource-group-overview.md) pour déployer vos ressources Azure. Pour en savoir plus sur la gestion de vos ressources, consultez [gérer les ressources Azure à l’aide du portail Azure](manage-resources-portal.md).

## <a name="create-resource-group"></a>Créer un groupe de ressources

1. Pour créer un groupe de ressources vide, sélectionnez **Groupes de ressources**.

   ![Sélectionner des groupes de ressources](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. Dans Groupes de ressources, sélectionnez **Ajouter**.

   ![Ajouter un groupe de ressource](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Spécifiez un nom et un emplacement, puis, si nécessaire, sélectionnez un abonnement. Vous devez fournir un emplacement pour le groupe de ressources, car celui-ci stocke des métadonnées sur les ressources. Pour des raisons de conformité, vous souhaiterez peut-être indiquer où sont stockées métadonnées. En règle générale, nous vous recommandons de spécifier l’emplacement où réside la plupart de vos ressources. L’utilisation du même emplacement permet de simplifier votre modèle.

   ![Définir les valeurs d’un groupe](./media/resource-group-template-deploy-portal/set-group-properties.png)

   Lorsque vous avez terminé la définition des propriétés, sélectionnez **Créer**.

1. Pour voir votre nouveau groupe de ressources, cliquez sur **Actualiser**.

   ![Actualiser des groupes de ressources](./media/resource-group-template-deploy-portal/refresh-resource-groups.png)

## <a name="deploy-resources-from-marketplace"></a>Déployer des ressources à partir de Marketplace

Une fois votre groupe de ressources créé, vous pouvez y déployer des ressources à partir de Marketplace. Marketplace fournit des solutions prédéfinies pour les scénarios courants.

1. Pour démarrer un déploiement, sélectionnez **Créer une ressource**.

   ![Nouvelle ressource](./media/resource-group-template-deploy-portal/new-resources.png)

1. Recherchez le type de ressource que vous souhaitez déployer.

   ![sélectionner le type de ressource](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Si vous ne voyez pas la solution que vous souhaitez déployer, vous pouvez lancer une recherche sur la Place de marché. Par exemple, pour rechercher une solution Wordpress, commencez à entrer **Wordpress** et sélectionnez l’option de votre choix.

   ![Rechercher sur le marketplace](./media/resource-group-template-deploy-portal/search-resource.png)

1. Selon le type de ressource que vous avez sélectionné, vous devrez définir une collection de propriétés pertinentes avant le déploiement. Pour tous les types, vous devez sélectionner un groupe de ressources de destination. L’illustration suivante montre comment créer une application web et la déployer dans le groupe de ressources que vous avez créé.

   ![Créer un groupe de ressources](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Vous pouvez également décider de créer un groupe de ressources lors du déploiement de vos ressources. Sélectionnez **Créer** et donnez un nom au groupe de ressources.

   ![Créer un groupe de ressources](./media/resource-group-template-deploy-portal/select-new-group.png)

1. Votre déploiement se met en route. Ce déploiement peut prendre quelques minutes. Vous recevez une notification une fois le déploiement terminé.

   ![Afficher une notification](./media/resource-group-template-deploy-portal/view-notification.png)

1. Après avoir déployé vos ressources, vous pouvez ajouter d’autres ressources au groupe de ressources en cliquant sur **Ajouter**.

   ![Ajouter une ressource](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Déployer des ressources à partir d’un modèle personnalisé

Si vous souhaitez effectuer un déploiement sans utiliser l’un des modèles de Marketplace, vous pouvez créer un modèle personnalisé qui définit l’infrastructure de votre solution. Pour apprendre à créer des modèles, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> L’interface du portail ne prend pas en charge le référencement à un [secret d’un coffre de clés](resource-manager-keyvault-parameter.md). Utilisez plutôt [PowerShell](resource-group-template-deploy.md) ou [Azure CLI](resource-group-template-deploy-cli.md) pour déployer votre modèle localement ou à partir d’un URI externe.

1. Pour déployer un modèle personnalisé grâce au portail, sélectionnez **Créer une ressource**, puis recherchez le **Déploiement de modèle** jusqu’à ce que vous puissiez le sélectionner dans les options.

   ![Rechercher un déploiement de modèle](./media/resource-group-template-deploy-portal/search-template.png)

1. Sélectionnez **Créer**.

   ![Sélectionner Créer](./media/resource-group-template-deploy-portal/show-template-option.png)

1. Vous voyez plusieurs options pour créer un modèle. Sélectionnez **Générer votre propre modèle dans l’éditeur**.

   ![Options d’affichage](./media/resource-group-template-deploy-portal/see-options.png)

1. Vous disposez d’un modèle vide personnalisable.

   ![Créer un modèle](./media/resource-group-template-deploy-portal/blank-template.png)

1. Vous pouvez modifier la syntaxe JSON manuellement, ou sélectionner un modèle prédéfini à partir de la [Galerie de modèles de démarrage rapide](https://azure.microsoft.com/resources/templates/). Toutefois, pour cet article, vous utilisez l’option **Ajouter une ressource**.

   ![Modifier un modèle](./media/resource-group-template-deploy-portal/select-add-resource.png)

1. Entrez un nom pour votre **Compte de stockage**. Lorsque vous avez fourni les valeurs, sélectionnez **OK**.

   ![Sélectionner le compte de stockage](./media/resource-group-template-deploy-portal/add-storage-account.png)

1. L’éditeur ajoute automatiquement JSON comme type de ressource. Notez qu’il inclut un paramètre pour définir le type de compte de stockage. Sélectionnez **Enregistrer**.

   ![Afficher le modèle](./media/resource-group-template-deploy-portal/show-json.png)

1. À présent, vous avez une option pour déployer les ressources définies dans le modèle. Pour effectuer le déploiement, acceptez les conditions générales, puis sélectionnez **Acheter**.

   ![Déployer un modèle](./media/resource-group-template-deploy-portal/provide-custom-template-values.png)

## <a name="deploy-resources-from-a-template-saved-to-your-account"></a>Déployer des ressources à partir d’un modèle enregistré dans votre compte

Le portail vous permet d’enregistrer un modèle dans votre compte Azure et de le redéployer ultérieurement. Pour plus d’informations sur les modèles, consultez [Créer et déployer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md).

1. Pour rechercher vos modèles enregistrés, sélectionnez **Autres services**.

   ![Plus de services](./media/resource-group-template-deploy-portal/more-services.png)

1. Recherchez les **modèles** et sélectionnez cette option.

   ![Rechercher les modèles](./media/resource-group-template-deploy-portal/find-templates.png)

1. Dans la liste des modèles enregistrés dans votre compte, sélectionnez celui sur lequel vous souhaitez travailler.

   ![Modèles enregistrés](./media/resource-group-template-deploy-portal/saved-templates.png)

1. Sélectionnez **Déployer** pour redéployer ce modèle enregistré.

   ![Déployer un modèle enregistré](./media/resource-group-template-deploy-portal/deploy-saved-template.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour visualiser les journaux d’audit, voir [Opérations d’audit avec Resource Manager](./resource-group-audit.md).
- Pour résoudre les erreurs de déploiement, consultez [Voir les opérations de déploiement](./resource-manager-deployment-operations.md).
- Pour exporter un modèle à partir d’un déploiement ou d’un groupe de ressources, consultez [modèles exporter de Azure Resource Manager](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Pour déployer votre service sur plusieurs régions de façon sécurisée, consultez [Azure Deployment Manager](./deployment-manager-overview.md).
