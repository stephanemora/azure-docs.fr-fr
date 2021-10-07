---
title: Utilisation de modèles Resource Manager liés
description: Découvrez comment utiliser des modèles Resource Manager liés avec l’intégration continue et la livraison continue dans les pipelines Azure Data Factory.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2df62471818bbca069802f3c60993ce79bb50dd
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219585"
---
# <a name="linked-resource-manager-templates-with-cicd"></a>Modèles Resource Manager liés avec CI/CD

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Si vous avez configuré l’intégration continue et la livraison continue (CI/CD) pour vos fabriques de données, vous risquez de dépasser les limites des modèles Azure Resource Manager au fur et à mesure de l’augmentation de la taille des fabriques. Par exemple, le nombre maximal de ressources dans un modèle Resource Manager est une limite. Pour prendre en compte des fabriques de grande taille tout en générant le modèle Resource Manager complet pour une fabrique, Data Factory génère désormais des modèles Resource Manager liés. Avec cette fonctionnalité, la charge utile de fabrique entière est divisée en plusieurs fichiers, pour que vous ne soyez pas contraint par les limites.

## <a name="finding-the-linked-templates"></a>Recherche des modèles liés

Si vous avez configuré Git, les modèles liés sont générés et enregistrés en même temps que les modèles Resource Manager complets, dans la branche adf_publish, dans un nouveau dossier nommé linkedTemplates :

:::image type="content" source="media/continuous-integration-delivery/linked-resource-manager-templates.png" alt-text="Dossier de modèles Resource Manager liés":::

Les modèles Resource Manager liés sont généralement composés d’un modèle maître et d’un ensemble de modèles enfants liés au maître. Le modèle parent est appelé ArmTemplate_master.json et les modèles enfants sont nommés selon le modèle ArmTemplate_0.json, ArmTemplate_1.json, etc. 

## <a name="using-linked-templates"></a>Utilisation des modèles liés
Pour utiliser des modèles liés à la place du modèle Resource Manager complet, mettez à jour votre tâche CI/CD de manière à pointer vers ArmTemplate_master.json plutôt que vers ArmTemplateForFactory.json (modèle Resource Manager complet). Resource Manager exige également que vous chargiez les modèles liés dans un compte de stockage pour qu’Azure puisse y accéder pendant le déploiement. Pour plus d’informations, consultez [Deploying linked Resource Manager templates with VSTS](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts) (Déploiement de modèles Resource Manager liés avec VSTS).

N’oubliez pas d’ajouter les scripts Data Factory dans votre pipeline CI/CD avant et après la tâche de déploiement.

Si vous n’avez pas configuré Git, vous pouvez accéder aux modèles liés via **Exporter le modèle ARM** dans la liste **Modèle ARM**.

Lorsque vous déployez vos ressources, vous spécifiez que le déploiement est soit une mise à jour incrémentielle, soit une mise à jour complète. La différence entre ces deux modes réside dans la manière dont le Gestionnaire des ressources gère les ressources existantes dans le groupe de ressources qui ne se trouvent pas dans le modèle. Consultez les [Modes de déploiement](../azure-resource-manager/templates/deployment-modes.md).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’intégration et de la livraison continues](continuous-integration-delivery.md)
- [Automatiser l’intégration continue à l’aide des versions d’Azure Pipelines](continuous-integration-delivery-automate-azure-pipelines.md)
- [Promouvoir manuellement un modèle Resource Manager pour chaque environnement](continuous-integration-delivery-manual-promotion.md)
- [Utiliser des paramètres personnalisés avec un modèle Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Utilisation d’un environnement de production de correctifs logiciels](continuous-integration-delivery-hotfix-environment.md)
- [Exemple de script de prédéploiement et de postdéploiement](continuous-integration-delivery-sample-script.md)