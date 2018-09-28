---
title: Vue d’ensemble d’Azure Blueprint
description: Azure Blueprint est un service Azure qui vous permet de créer, de définir et de déployer des artefacts dans votre environnement Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: c7ffbe86407bc776870890e5b4151556f572832e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957503"
---
# <a name="what-is-azure-blueprints"></a>Qu’est-ce qu’Azure Blueprint ?

À l’instar d’un plan, « blueprint » en anglais, qui permet aux ingénieurs et architectes de décrire les paramètres de conception d’un projet, Azure Blueprint permet aux architectes cloud et aux membres de l’informatique centrale de définir un ensemble reproductible de ressources Azure qui implémentent et respectent les normes, modèles et exigences d’une organisation. Azure Blueprint permet aux équipes de développement de provisionner et de mettre en place rapidement de nouveaux environnements conformes aux exigences de l’organisation et intégrant un ensemble de composants, comme la mise en réseau, visant à accélérer le développement et la livraison.

Les blueprints sont un moyen déclaratif d’orchestrer le déploiement de plusieurs modèles de ressources et d’autres artefacts, notamment ceux-ci :

- Affectations de rôles
- Affectations de stratégies
- Modèles Microsoft Azure Resource Manager
- Groupes de ressources

## <a name="how-it-is-different-from-resource-manager-templates"></a>Différences par rapport aux modèles Resource Manager

Les blueprints sont conçus pour faciliter la _configuration de l’environnement_, qui se compose souvent d’un ensemble de groupes de ressources, de stratégies et d’attributions de rôles, en plus des déploiements de modèle Resource Manager. Un blueprint est un package qui vous permet de regrouper ces types _d’artefact_. Vous pouvez composer ce package et gérer ses versions, notamment par le biais d’un pipeline CI/CD. Au final, chaque blueprint est affecté à un abonnement dans une opération unique qui peut faire l’objet d’un audit et d’un suivi.

Vous pouvez utiliser un modèle Resource Manager pour accomplir la plupart des choses que vous souhaitez ajouter au déploiement dans Azure Blueprint. Toutefois, un modèle Resource Manager est un document qui n’existe pas en mode natif dans Azure et qui est stocké localement ou dans le contrôle de code source. Le modèle peut servir aux déploiements d’une ou plusieurs ressources Azure, mais une fois ces ressources déployées, la connexion et la relation au modèle utilisé sont perdues.

Avec Azure Blueprint, la relation entre la définition du blueprint (_ce qui doit être_ déployé) et l’affectation du blueprint (_ce qui a été_ déployé) est conservée. Cette connexion permet d’améliorer le suivi et l’audit des déploiements, de mettre à niveau plusieurs abonnements à la fois s’ils sont régis par le même blueprint, etc.

Il est inutile de choisir entre un modèle Resource Manager et un blueprint. Chaque blueprint peut comprendre zéro ou plusieurs _artefacts_ d’un modèle Resource Manager. Vous pouvez ainsi tirer parti des précédents efforts visant à développer et à maintenir une bibliothèque de modèles Resource Manager dans Azure Blueprint.

## <a name="how-it-is-different-from-azure-policy"></a>Différences par rapport à Azure Policy

Un blueprint est un package ou un conteneur qui permet de composer des ensembles spécifiques de normes, modèles et exigences en rapport avec l’implémentation de services cloud Azure, de stratégies de sécurité et de conceptions réutilisables à des fins de cohérence et de conformité.

Une [stratégie](../policy/overview.md) est un système d’autorisations par défaut et de refus explicites qui s’applique aux propriétés des ressources durant le déploiement et aux ressources existantes. Pour prendre en charge la gouvernance informatique, une stratégie veille au respect des normes et des exigences au sein d’un abonnement.

L’inclusion d’une stratégie dans un blueprint permet non seulement de créer une conception ou un modèle adapté durant l’affectation du blueprint, mais elle garantit aussi que l’environnement fait uniquement l’objet de changements approuvés ou attendus pour assurer la conformité continue à l’intention du blueprint.

Une stratégie peut constituer l’un des nombreux _artefacts_ d’une définition de blueprint. Les blueprints prennent également en charge l’utilisation de paramètres avec des stratégies et des initiatives.

## <a name="blueprint-definition"></a>Définition de blueprint

Un blueprint est composé _d’artefacts_. Les blueprints prennent actuellement en charge les ressources suivantes comme artefacts :

|Ressource  | Options de hiérarchie| Description  |
|---------|---------|---------|
|Groupes de ressources     | Abonnement | Crée un groupe de ressources pour une utilisation par d’autres artefacts dans le blueprint.  Ces groupes de ressources réservés vous permettent d’organiser les ressources en totale conformité avec la structure souhaitée. Ils fournissent aussi un limiteur d’étendue pour les artefacts de stratégie et d’attribution de rôle inclus, ainsi que des modèles Azure Resource Manager.         |
|Modèle Azure Resource Manager      | Groupe de ressources | Ces modèles peuvent être utilisés pour composer des environnements complexes, notamment une batterie de serveurs SharePoint, une configuration de l’état Azure Automation ou un espace de travail Log Analytics. |
|Affectation de rôle     | Abonnement, groupe de ressources | Permet l’affectation d’une stratégie ou d’une initiative au groupe d’administration ou à l’abonnement auquel le blueprint est affecté. La stratégie ou l’initiative doit être dans l’étendue du blueprint (dans le groupe d’administration du blueprint ou en dessous). Si la stratégie ou l’initiative a des paramètres, ceux-ci peuvent être affectés au moment de la création du blueprint ou durant son affectation.       |
|Attribution de rôle   | Abonnement, groupe de ressources | Ajoute un utilisateur ou un groupe existant à un rôle intégré pour faire en sorte que les personnes adéquates disposent d’un accès approprié à vos ressources. Vous pouvez définir des attributions de rôle pour l’ensemble de l’abonnement ou les imbriquer dans un groupe de ressources spécifique inclus dans le blueprint. |

### <a name="blueprints-and-management-groups"></a>Blueprints et groupes d’administration

Quand vous créez une définition de blueprint, vous définissez l’emplacement d’enregistrement du blueprint. À l’heure actuelle, les blueprints peuvent être uniquement enregistrés dans un [groupe d’administration](../management-groups/overview.md) auquel vous avez accès en tant que **Contributeur**. Le blueprint peut ensuite être affecté à n’importe quel enfant (abonnement ou groupe d’administration) de ce groupe d’administration.

> [!IMPORTANT]
> Si vous n’avez accès à aucun groupe d’administration ou si aucun groupe d’administration n’est configuré, vous êtes notifié de ce problème quand vous chargez la liste des définitions de blueprint. De même, le fait de cliquer sur **Étendue** ouvre une fenêtre avec un avertissement concernant la récupération des groupes d’administration. Pour résoudre ce problème, vérifiez qu’un abonnement auquel vous avez un accès approprié fait partie d’un [groupe d’administration](../management-groups/overview.md).

### <a name="blueprint-parameters"></a>Paramètres de blueprint

Les blueprints peuvent passer des paramètres à une stratégie/initiative ou à un modèle Azure Resource Manager.
Quand l’un de ces _artefacts_ est ajouté à un blueprint, l’auteur peut soit fournir une valeur définie pour chaque affectation de blueprint, soit autoriser chaque affectation de blueprint à fournir une valeur au moment de l’affectation. Cette flexibilité permet de définir une valeur prédéterminée pour toutes les utilisations du blueprint ou de repousser cette décision au moment de l’affectation.

> [!NOTE]
> Un blueprint peut avoir ses propres paramètres, mais leur création n’est actuellement possible que si le blueprint est généré à partir de l’API REST (impossible si le blueprint est généré par le biais du portail).

Pour plus d’informations, consultez [Paramètres de blueprint](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publication de blueprint

Quand vous créez un blueprint, celui-ci est initialement en mode **Brouillon**. Quand il est prêt à être affecté, vous devez le **publier**. La publication nécessite la définition d’une **Version** (chaîne composée de lettres, de chiffres et de traits d’union d’une longueur maximale de 20 caractères) avec, en option, des **Notes de changement**.
La **Version** permet de différencier le blueprint si des changements sont apportés par la suite et d’affecter des versions différentes. Vous pouvez donc affecter différentes **Versions** du même blueprint au même abonnement. Quand des changements sont apportés au blueprint, la **Version** **Publiée** est toujours présente, mais vous avez aussi des **Changements non publiés**. Une fois les changements finalisés, le blueprint mis à jour est **publié** avec une nouvelle **Version** unique et peut être affecté.

## <a name="blueprint-assignment"></a>Affectation de blueprint

Chaque **Version** **Publiée** d’un blueprint peut être affectée à un abonnement existant. Dans le portail, le blueprint par défaut correspond à la dernière **Version** **Publiée**. Si des paramètres d’artefact (ou des paramètres de blueprint) sont présents, ils sont définis durant le processus d’affectation.

## <a name="permissions-in-azure-blueprints"></a>Autorisations dans Azure Blueprint

Pour utiliser des blueprints, vous devez disposer d’autorisations accordées par le biais du [contrôle d’accès en fonction du rôle](../../role-based-access-control/overview.md) (RBAC). Pour créer des blueprints, votre compte doit avoir les autorisations suivantes :

- `Microsoft.Blueprint/blueprints/write` - Créer une définition de blueprint
- `Microsoft.Blueprint/blueprints/artifacts/write` - Créer des artefacts sur une définition de blueprint
- `Microsoft.Blueprint/blueprints/versions/write` - Publier un blueprint

Pour supprimer des blueprints, votre compte doit avoir les autorisations suivantes :

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Comme les définitions de blueprint sont créées au niveau d’un groupe d’administration, les autorisations de définition de blueprint doivent être accordées sur une étendue de groupe d’administration ou être héritées dans une étendue de groupe d’administration.

Pour affecter ou annuler l’affectation d’un blueprint, votre compte doit avoir les autorisations suivantes :

- `Microsoft.Blueprint/blueprintAssignments/write` - Affecter un blueprint
- `Microsoft.Blueprint/blueprintAssignments/delete` - Annuler l’affectation d’un blueprint

> [!NOTE]
> Comme les affectations de blueprint sont créées sur un abonnement, les autorisations d’affectation de blueprint et d’annulation d’affectation de blueprint doivent être accordées sur une étendue d’abonnement ou être héritées dans une étendue d’abonnement.

Ces autorisations sont incluses dans le rôle **Propriétaire** et, à l’exception des autorisations d’affectation de blueprint, dans les rôles **Contributeur**. Si ces rôles intégrés ne répondent pas à vos besoins de sécurité, songez à créer un [rôle personnalisé](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Le principal de service pour Azure Blueprint nécessite le rôle **Propriétaire** sur l’abonnement affecté pour pouvoir activer le déploiement. Si vous utilisez le portail, ce rôle est automatiquement accordé et révoqué pour le déploiement. Si vous utilisez l’API REST, ce rôle doit être accordé manuellement, mais il est toujours automatiquement révoqué une fois le déploiement terminé.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un blueprint : portail](create-blueprint-portal.md)
- [Créer un blueprint : API REST](create-blueprint-rest-api.md)