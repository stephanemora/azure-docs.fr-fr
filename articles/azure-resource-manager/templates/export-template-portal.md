---
title: Exporter le modèle dans le Portail Azure
description: Utilisez le portail Azure pour exporter un modèle Azure Resource Manager à partir de ressources de votre abonnement.
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: c6987f95f2ccb953977244d8ff70b2f925f35f2e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436244"
---
# <a name="use-azure-portal-to-export-a-template"></a>Utiliser le portail Azure pour exporter un modèle

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

Cet article explique comment exporter des modèles via le **portail**. Pour d’autres options, voir :

* [Exporter un modèle avec Azure CLI](export-template-cli.md)
* [Exporter un modèle avec Azure PowerShell](export-template-powershell.md)
* [Exportation API REST du groupe de ressources](/rest/api/resources/resourcegroups/exporttemplate) et [exportation API REST de l’historique de déploiement](/rest/api/resources/deployments/export-template).

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources

Pour exporter une ou plusieurs ressources à partir d’un groupe de ressources :

1. Sélectionnez le groupe de ressources qui contient les ressources que vous souhaitez exporter.

1. Sélectionnez une ou plusieurs ressources en cochant les cases correspondantes.  Pour sélectionner tout, cochez la case située à gauche de **Nom**. L’élément de menu **Exporter le modèle** devient actif seulement uniquement après la sélection d’au moins une ressource.

   ![Exporter toutes les ressources](./media/export-template-portal/select-all-resources.png)

    Dans la capture d’écran, seul le compte de stockage est sélectionné.
1. Sélectionnez **Exporter le modèle**.

1. Le modèle exporté s’affiche et est disponible au téléchargement et au déploiement.

   ![Afficher le modèle](./media/export-template-portal/show-template.png)

   **Inclure des paramètres** est sélectionné par défaut.  Une fois sélectionnés, tous les exemples de paramètres seront inclus après génération du modèle. Si vous souhaitez créer vos propres paramètres, cochez cette case pour ne pas les inclure.

## <a name="export-template-from-a-resource"></a>Exportation du modèle à partir d’une ressource

Pour exporter une ressource :

1. Sélectionnez le groupe de ressources qui contient la ressource que vous souhaitez exporter.

1. Sélectionnez la ressource que vous souhaitez exporter pour ouvrir la ressource.

1. Pour cette ressource, sélectionnez **Exporter le modèle** dans le volet de gauche.

   ![Exporter une ressource](./media/export-template-portal/export-single-resource.png)

1. Le modèle exporté s’affiche et est disponible au téléchargement et au déploiement. Le modèle contient uniquement la ressource unique. **Inclure des paramètres** est sélectionné par défaut.  Une fois sélectionnés, tous les exemples de paramètres seront inclus après génération du modèle. Si vous souhaitez créer vos propres paramètres, cochez cette case pour ne pas les inclure.

## <a name="download-template-before-deployment"></a>Télécharger un modèle avant le déploiement

Le portail a la possibilité de télécharger un modèle avant de le déployer. Cette option n’est pas disponible via PowerShell ou Azure CLI.

1. Sélectionnez le service Azure que vous souhaitez déployer.

1. Renseignez les valeurs pour le nouveau service.

1. Après la validation, mais avant de commencer le déploiement, sélectionnez **Télécharger un modèle pour l’automatisation**.

   ![Télécharger un modèle](./media/export-template-portal/download-before-deployment.png)

1. Le modèle s’affiche et est disponible au téléchargement et au déploiement.

## <a name="export-template-after-deployment"></a>Exporter un modèle après le déploiement

Vous pouvez exporter le modèle qui a été utilisé pour déployer les ressources existantes. Le modèle que vous obtenez est exactement celui qui a été utilisé pour le déploiement.

1. Sélectionnez le groupe de ressources que vous souhaitez exporter.

1. Sélectionnez le lien sous **Déploiements**.

   ![Sélectionner l’historique des déploiements](./media/export-template-portal/select-deployment-history.png)

1. Sélectionnez un des déploiements dans l’historique des déploiements.

   ![Sélectionner un déploiement](./media/export-template-portal/select-details.png)

1. Sélectionner **Modèle**. Le modèle utilisé pour ce déploiement s’affiche et est disponible au téléchargement.

   ![Sélectionner un modèle](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment exporter des modèles avec [Azure CLI](export-template-cli.md), [Azure PowerShell](export-template-powershell.md) ou l'[API REST](/rest/api/resources/resourcegroups/exporttemplate).
- Pour vous familiariser avec la syntaxe des modèles Resource Manager, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](./syntax.md).
- Pour apprendre à développer des modèles, consultez les [tutoriels pas à pas](../index.yml).