---
title: Présentation de la stratégie Azure
description: Azure Policy est un service dans Azure, que vous utilisez pour créer, affecter et gérer les définitions de stratégie dans votre environnement Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937830"
---
# <a name="what-is-azure-policy"></a>Présentation d’Azure Policy

La gouvernance valide le fait que votre organisation peut atteindre ses objectifs via une utilisation efficace de l’informatique. Elle répond à ce besoin en clarifiant les objectifs métier et les projets informatiques.

Votre société rencontre un nombre important de problèmes informatiques qui ne semblent jamais résolus ? Une bonne gouvernance informatique implique la planification de vos initiatives et la définition de priorités à un niveau stratégique. De cette manière, vous pouvez gérer et éviter les problèmes de manière plus efficace. C’est pour répondre à ce besoin stratégique qu’Azure Policy entre en jeu,

Azure Policy est un service d’Azure que vous utilisez pour créer, affecter et gérer des stratégies. Ces stratégies appliquent différentes règles et effets sur vos ressources, qui restent donc conformes aux normes et aux contrats de niveau de service de l’entreprise. en évaluant vos ressources pour vérifier leur conformité par rapport aux stratégies affectées. Toutes les données stockées par Azure Policy sont chiffrées au repos.

Par exemple, vous pouvez disposer d’une stratégie qui n’autorise qu’une certaine taille de référence SKU de machines virtuelles dans votre environnement. Une fois que cette stratégie est implémentée, les ressources nouvelles et existantes sont évaluées en termes de conformité. Avec le bon type de stratégie, les ressources existantes peuvent être mises en conformité. Plus tard dans ce document, nous allons parler plus en détail de la manière de créer et d’implémenter des stratégies avec Azure Policy.

> [!IMPORTANT]
> L’évaluation de la conformité Azure Policy est désormais fournie pour toutes les affectations, quel que soit le niveau de tarification. Si vos affectations n’affichent pas les données de conformité, vérifiez que l’abonnement est inscrit auprès du fournisseur de ressources Microsoft.PolicyInsights.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Quelle est la différence avec RBAC ?

Il existe quelques différences importantes entre Azure Policy et le contrôle d’accès en fonction du rôle (RBAC). Le contrôle RBAC porte sur les actions des utilisateurs dans différentes étendues. Vous pouvez être ajouté au rôle de contributeur pour un groupe de ressources, ce qui vous permet d’apporter des modifications à ce groupe de ressources. Azure Policy se focalise sur les propriétés des ressources pendant le déploiement et sur les ressources existantes. Azure Policy contrôle les propriétés telles que les types ou emplacements des ressources. Contrairement à RBAC, Azure Policy est un système explicite d’autorisation et de refus par défaut.

### <a name="rbac-permissions-in-azure-policy"></a>Autorisations RBAC dans Azure Policy

Azure Policy dispose d’autorisations, aussi appelées opérations, dans deux fournisseurs de ressources :

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Plusieurs rôles intégrés accordent des autorisations aux ressources Azure Policy. Le rôle **Contributeur de stratégie de ressource** inclut la plupart des opérations d’Azure Policy. Quant au rôle **Propriétaire**, il dispose de tous les droits. Les rôles **Contributeur** et **Lecteur** peuvent utiliser toutes les opérations en lecture d’Azure Policy, mais le rôle **Contributeur** peut aussi les corriger.

Si aucun des rôles intégrés ne dispose d’autorisations, créez un [rôle personnalisé](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Définition de stratégie

Pour créer et implémenter une stratégie dans Azure Policy, il faut commencer par créer une définition de stratégie. Chaque définition de stratégie présente des conditions dans lesquelles elle est appliquée. Si les conditions sont remplies, un effet défini se produit.

Dans Azure Policy, nous proposons plusieurs stratégies intégrées qui sont disponibles par défaut. Par exemple :

- **Références SKU de compte de stockage autorisées** : Détermine si un compte de stockage en cours de déploiement se trouve dans un ensemble de tailles de référence SKU. Son effet consiste à refuser tous les comptes de stockage dont la taille ne fait pas partie de l’ensemble de tailles de référence SKU définies.
- **Type de ressource autorisé** : Définit les types de ressources que vous pouvez déployer. Son effet consiste à refuser toutes les ressources qui ne font pas partie de cette liste définie.
- **Emplacements autorisés** : Restreint les emplacements disponibles pour les nouvelles ressources. Son effet permet d’appliquer vos exigences de conformité géographique.
- **Références SKU de machine virtuelle autorisées** : Spécifie un ensemble de références SKU de machine virtuelle que vous pouvez déployer.
- **Ajouter une étiquette aux ressources** : Applique une balise requise et sa valeur par défaut si elle n’est pas spécifiée par la requête de déploiement.
- **Appliquer la balise et sa valeur** : Applique une balise requise et sa valeur à une ressource.
- **Types de ressources non autorisés** : Empêche une liste de types de ressources d’être déployés.

Pour implémenter ces définitions de stratégie (définitions intégrées et personnalisées), vous devez les affecter. Vous pouvez affecter l’une de ces stratégies par le biais du portail Azure, de PowerShell ou d’Azure CLI.

Une évaluation de la stratégie a lieu avec plusieurs actions différentes comme l’affectation de stratégie ou les mises à jour de stratégie. Pour obtenir la liste complète, consultez [Policy evaluation triggers](./how-to/get-compliance-data.md#evaluation-triggers) (Déclencheurs d’évaluation de stratégie).

Pour plus d’informations sur les structures des définitions de stratégie, consultez [Structure des définitions de stratégie](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Affectation de rôle

Une affectation de stratégie est une définition de stratégie qui a été affectée avec une étendue spécifique. Cette étendue peut aller d’un [groupe d’administration](../management-groups/overview.md) à une ressource individuelle. Le terme *étendue* désigne l’ensemble des ressources, groupes de ressources, abonnements ou groupes d’administration auxquels la définition de stratégie est affectée. Toutes les ressources enfants héritent des affectations de stratégie. Grâce à cette structure, si une stratégie est appliquée à un groupe de ressources, elle est également appliquée à toutes les ressources de ce groupe de ressources. Toutefois, vous pouvez exclure une sous-étendue de l’affectation de stratégie.

Par exemple, dans l’étendue de l’abonnement, vous pouvez affecter une stratégie qui empêche la création de ressources réseau. Vous pouvez exclure un groupe de ressources au sein de cet abonnement qui est destiné à l’infrastructure réseau. Vous accordez ensuite l’accès à ce groupe de ressources réseau aux utilisateurs auxquels vous faites confiance avec la création des ressources réseau.

Dans un autre exemple, vous souhaiterez peut-être affecter une stratégie de liste d’autorisation de type de ressource au niveau du groupe d’administration. Affectez ensuite une stratégie plus permissive (autorisant plus de types de ressources) à un groupe d’administration enfant ou même directement aux abonnements. Toutefois, cet exemple ne fonctionnera pas, car la stratégie est un système de refus explicite. Au lieu de cela, vous devez exclure le groupe d’administration enfant ou l’abonnement de l’attribution de stratégie au niveau du groupe d’administration. Affectez ensuite la stratégie plus permissive au niveau du groupe d’administration enfant ou de l’abonnement. Si une stratégie se traduit par le refus d’une ressource, alors la seule façon d’autoriser la ressource est de modifier la stratégie de refus.

Pour plus d’informations sur les définitions de stratégie et les affectations par le biais du portail, consultez [Créer une affectation de stratégie pour identifier les ressources non conformes dans votre environnement Azure](assign-policy-portal.md). Les étapes pour [PowerShell](assign-policy-powershell.md) et [Azure CLI](assign-policy-azurecli.md) sont également disponibles.

## <a name="policy-parameters"></a>Paramètres de stratégie

Les paramètres de stratégie permettent de simplifier la gestion des stratégies en réduisant le nombre de définitions de stratégies que vous devez créer. Vous pouvez définir des paramètres lors de la création d’une définition de stratégie, pour la rendre plus générique. Vous pouvez ensuite réutiliser cette définition de stratégie pour différents scénarios. Pour ce faire, transmettez différentes valeurs lors de l’affectation de la définition de stratégie. Par exemple, spécifiez un ensemble d’emplacements pour un abonnement.

Les paramètres sont définis lors de la création d’une définition de stratégie. Quand un paramètre est défini, un nom lui est affecté et éventuellement une valeur. Par exemple, vous pouvez définir un paramètre pour une stratégie intitulée *Emplacement*. Vous pouvez ensuite lui attribuer différentes valeurs comme *EastUS* ou *WestUS* lors de l’affectation d’une stratégie.

Pour plus d’informations sur les paramètres de stratégie, consultez [Definition structure - Parameters](./concepts/definition-structure.md#parameters) (Structure de la définition - Paramètres).

## <a name="initiative-definition"></a>Définition d’initiative

Une définition d’initiative est une collection de définitions de stratégie qui sont spécialement conçues pour atteindre un objectif global particulier. Les définitions d’initiative simplifient la gestion et l’affectation des définitions de stratégie. Elles simplifient ces procédures en regroupant un ensemble de stratégies en un seul élément. Par exemple, vous pouvez créer une initiative intitulée **Activer la surveillance dans Azure Security Center**, avec comme objectif de surveiller toutes les recommandations de sécurité disponibles dans votre centre Azure Security Center.

> [!NOTE]
> Le SDK, et notamment Azure CLI et Azure PowerShell, utilisent des propriétés et des paramètres nommés **PolicySet** pour référencer les initiatives.

Dans cette initiative, vous avez par exemple des définitions de stratégie comme celles-ci :

- **Surveiller les bases de données non chiffrées dans Security Center** : pour surveiller les bases de données et les serveurs SQL Server non chiffrés.
- **Surveiller les vulnérabilités du système d’exploitation dans Security Center** : pour surveiller les serveurs qui ne répondent pas à la ligne de base configurée.
- **Surveiller l’absence d’Endpoint Protection dans Security Center** : pour surveiller les serveurs où un agent Endpoint Protection n’est pas installé.

## <a name="initiative-assignment"></a>Affectation d’initiative

Comme une affectation de stratégie, une affectation d’initiative est une définition d’initiative affectée à une étendue spécifique. Les affectations d’initiative réduisent la nécessité de créer plusieurs définitions d’initiative pour chaque étendue. Cette étendue peut aussi aller d’un groupe d’administration à une ressource individuelle.

Chaque initiative est affectable à différentes étendues. Une initiative peut être affectée à **subscriptionA** et **subscriptionB**.

## <a name="initiative-parameters"></a>Paramètres d’initiative

Comme les paramètres de stratégie, les paramètres d’initiative permettent de simplifier la gestion en réduisant la redondance. Les paramètres d’initiative sont les paramètres utilisés par les définitions de stratégie dans l’initiative.

Par exemple, imaginons un scénario où vous avez une définition d’initiative (**initiativeC**) avec les définitions de stratégie **policyA** et **policyB**, et chacune des définitions de stratégie attend un type de paramètre différent :

| Stratégie | Nom de paramètre |Type de paramètre  |Remarque |
|---|---|---|---|
| policyA | allowedLocations | tableau  |Ce paramètre attend une liste de chaînes pour une valeur, le type de paramètre ayant été défini comme tableau |
| policyB | allowedSingleLocation |string |Ce paramètre attend un mot pour une valeur, le type de paramètre ayant été défini comme chaîne |

Dans ce scénario, quand vous définissez les paramètres d’initiative pour **initiativeC**, vous avec trois options :

- Utilisez les paramètres des définitions de stratégie dans cette initiative : Dans cet exemple, *allowedLocations* et *allowedSingleLocation* deviennent des paramètres d’initiative pour **initiativeC**.
- Fournir des valeurs pour les paramètres des définitions de stratégie dans la définition de cette initiative. Dans cet exemple, vous pouvez fournir une liste d’emplacements au **paramètre de policyA, allowedLocations** et au **paramètre de policyB, allowedSingleLocation**. Vous pouvez également fournir des valeurs lors de l’affectation de cette initiative.
- Fournir une liste d’options de *valeurs* qui peuvent être utilisées lors de l’affectation de cette initiative. Lorsque vous affectez cette initiative, les paramètres hérités des définitions de stratégie dans l’initiative peuvent avoir seulement des valeurs provenant de cette liste fournie.

Lorsque vous créez des options de valeur dans une définition d’initiative, vous ne pouvez pas entrer de valeur différente lors de l’affectation d’initiative, car elle ne fait pas partie de la liste.

## <a name="maximum-count-of-azure-policy-objects"></a>Nombre maximal d’objets Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recommandations pour la gestion des stratégies

Voici quelques conseils et astuces à garder à l’esprit :

- Commencez avec un effet d’audit plutôt qu’un effet de refus pour suivre l’impact de la définition de votre stratégie sur votre environnement. Si vous avez déjà des scripts en place pour mettre automatiquement à l’échelle vos applications, la définition d’un effet de refus peut entraver ces tâches d’automatisation déjà en place.

- Tenez compte des hiérarchies de l’organisation lors de la création de définitions et d’affectations. Nous vous recommandons de créer des définitions à des niveaux supérieurs, comme au niveau de l’abonnement ou du groupe d’administration. Ensuite, créez l’affectation au niveau enfant suivant. Si vous créez une définition au niveau d’un groupe d’administration, l’affectation peut être limitée à un abonnement ou groupe de ressources au sein de ce groupe d’administration.

- Nous vous recommandons de créer et d’affecter des définitions d’initiative même pour une définition de stratégie unique.
  Par exemple, vous avez la définition de stratégie *policyDefA* et la créez sous la définition d’initiative *initiativeDefC*. Si vous créez une autre définition de stratégie ultérieurement pour *policyDefB* avec des objectifs similaires à *policyDefA*, vous pouvez l’ajouter sous *initiativeDefC* et les suivre ensemble.

- Une fois que vous avez créé une affectation d’initiative, les définitions de stratégie ajoutées à l’initiative font également partie des affectations d’initiatives.

- Lors de l’évaluation d’une affectation d’initiative, toutes les stratégies dans l’initiative sont également évaluées.
  Si vous devez évaluer une stratégie individuellement, il est préférable de ne pas l’inclure dans une initiative.

## <a name="video-overview"></a>Présentation vidéo

La présentation suivante d’Azure Policy est à partir de la Build 2018. Pour le téléchargement des diapositives ou de la vidéo, accédez à [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Gouvernance de votre environnement Azure à l’aide d’Azure Policy) sur Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une vue d’ensemble d’Azure Policy et des autres concepts clés, voici les étapes suivantes que nous suggérons :

- [Affecter une définition de stratégie à l’aide du portail](./assign-policy-portal.md).
- [Affecter une définition de stratégie avec Azure CLI](./assign-policy-azurecli.md).
- [Affecter une définition de stratégie avec PowerShell](./assign-policy-powershell.md).
