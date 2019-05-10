---
title: Exporter le modèle Azure Resource Manager à l’aide du portail Azure
description: Utilisez le portail Azure pour exporter un modèle Azure Resource Manager à partir des ressources dans votre abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: tomfitz
ms.openlocfilehash: ea9499da3dac67635a48704f439f6592c6ed467e
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515383"
---
# <a name="single-and-multi-resource-export-to-template-in-azure-portal"></a>Exportation unique et plusieurs ressource pour le modèle dans le portail Azure

Pour aider à créer des modèles Azure Resource Manager, vous pouvez exporter un modèle à partir de ressources existantes. Le modèle exporté vous aide à comprendre la syntaxe JSON et les propriétés qui déploiement vos ressources. Pour automatiser les déploiements futurs, commencez avec le modèle exporté et modifiez-la en fonction de votre scénario.

Resource Manager vous permet de choisir une ou plusieurs ressources pour l’exportation vers un modèle. Vous pouvez vous concentrer sur exactement les ressources que vous avez besoin dans le modèle.

## <a name="choose-the-right-export-option"></a>Choisissez l’option d’exportation de droite

Il existe deux façons d’exporter un modèle :

* **Exporter à partir de la ressource ou groupe de ressources**. Cette option génère un nouveau modèle à partir de ressources existantes. Le modèle exporté est un « instantané » de l’état actuel du groupe de ressources. Vous pouvez exporter un groupe de ressources complet ou des ressources spécifiques au sein de ce groupe de ressources.

* **Exporter avant le déploiement ou à partir de l’historique**. Cette option récupère une copie exacte d’un modèle utilisé pour le déploiement.

Selon l’option choisie, les modèles exportés ont différentes qualités.

| À partir de la ressource ou groupe de ressources | Avant le déploiement ou de l’historique |
| --------------------- | ----------------- |
| Modèle agit de l’instantané de l’état actuel de ressources. Il inclut toutes les modifications manuelles apportées après le déploiement. | Modèle affiche uniquement l’état des ressources au moment du déploiement. Toutes les modifications manuelles apportées après le déploiement ne sont pas inclus. |
| Vous pouvez sélectionner les ressources à partir d’un groupe de ressources à exporter. | Toutes les ressources pour un déploiement spécifique sont inclus. Vous ne pouvez pas choisir un sous-ensemble de ces ressources ou ajouter des ressources qui ont été ajoutés à un autre moment. |
| Modèle inclut toutes les propriétés pour les ressources, y compris certaines propriétés que vous n’aurait normalement définir pendant le déploiement. Vous souhaiterez peut-être supprimer ou nettoyer ces propriétés avant de réutiliser le modèle. | Modèle inclut uniquement les propriétés nécessaires au déploiement. Le modèle est prêt à l’emploi. |
| Modèle probablement n’inclut pas tous les paramètres que vous avez besoin pour une réutilisation. La plupart des valeurs de propriété sont codées en dur dans le modèle. Pour redéployer le modèle dans d’autres environnements, vous devez ajouter des paramètres qui augmentent la possibilité de configurer les ressources. | Modèle inclut des paramètres qui permettent de facilement redéployer dans différents environnements. |

Exporter le modèle à partir d’un groupe de ressources ou une ressource, lorsque :

* Vous devez capturer les modifications apportées aux ressources qui ont été passées après le déploiement d’origine.
* Vous devez sélectionner les ressources qui sont exportés.

Exporter le modèle avant le déploiement ou de l’historique, lorsque :

* Vous souhaitez un modèle facile à réutiliser.
* Vous n’avez pas besoin d’inclure des modifications apportées après le déploiement d’origine.

## <a name="export-template-from-resource-group"></a>Exportation du modèle à partir d’un groupe de ressources

Pour exporter une ou plusieurs ressources à partir d’un groupe de ressources :

1. Sélectionnez le groupe de ressources qui contient les ressources que vous voulez exporter.

1. Pour exporter toutes les ressources dans le groupe de ressources, sélectionnez tous, puis **exporter le modèle**. Le **exporter le modèle** option devient active seulement une fois que vous avez sélectionné au moins une ressource.

   ![Exporter toutes les ressources](./media/export-template-portal/select-all-resources.png)

1. Pour récupérer des ressources spécifiques pour l’exportation, sélectionnez les cases à cocher en regard de ces ressources. Ensuite, sélectionnez **exporter le modèle**.

   ![Sélectionnez les ressources à exporter](./media/export-template-portal/select-resources.png)

1. Le modèle exporté s’affiche et est disponible au téléchargement.

   ![Afficher le modèle](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Exporter le modèle à partir de la ressource

Pour exporter une ressource :

1. Sélectionnez le groupe de ressources contenant la ressource que vous voulez exporter.

1. Sélectionnez la ressource à exporter.

   ![Sélectionner une ressource](./media/export-template-portal/select-link-resource.png)

1. Pour cette ressource, sélectionnez **exporter le modèle** dans le volet gauche.

   ![Exporter la ressource](./media/export-template-portal/export-single-resource.png)

1. Le modèle exporté s’affiche et est disponible au téléchargement. Le modèle contient uniquement la ressource unique.

## <a name="export-template-before-deployment"></a>Exporter le modèle avant le déploiement

1. Sélectionnez le service Azure que vous souhaitez déployer.

1. Renseignez les valeurs pour le nouveau service.

1. Après la transmission de validation, mais avant de commencer le déploiement, sélectionnez **télécharger un modèle pour automation**.

   ![Télécharger un modèle](./media/export-template-portal/download-before-deployment.png)

1. Le modèle s’affiche et est disponible au téléchargement.

   ![Afficher le modèle](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Exporter le modèle après le déploiement

Vous pouvez exporter le modèle qui a été utilisé pour déployer des ressources existantes. Le modèle que vous obtenez est exactement celle qui a été utilisée pour le déploiement.

1. Sélectionnez le groupe de ressources que vous voulez exporter.

1. Sélectionnez le lien situé sous **déploiements**.

   ![Sélectionnez l’historique de déploiement](./media/export-template-portal/select-deployment-history.png)

1. Sélectionnez un des déploiements à partir de l’historique de déploiement.

   ![Sélectionnez le déploiement](./media/export-template-portal/select-details.png)

1. Sélectionnez **modèle**. Le modèle utilisé pour ce déploiement s’affiche et est disponible au téléchargement.

   ![Sélectionner un modèle](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’Azure Resource Manager, consultez [présentation d’Azure Resource Manager](./resource-group-overview.md).
- Pour découvrir la syntaxe du modèle Resource Manager, consultez [comprendre la structure et la syntaxe des modèles Azure Resource Manager](./resource-group-authoring-templates.md).
- Pour savoir comment développer des modèles, consultez le [didacticiels pas à pas](/azure/azure-resource-manager/).
- Pour afficher les schémas de modèle Azure Resource Manager, consultez [référence de modèle](/azure/templates/).