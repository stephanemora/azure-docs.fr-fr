---
title: Comprendre les accès et les autorisations
description: Cet article offre une vue d’ensemble des autorisations, du contrôle d’accès et des collections dans Azure Purview. Le contrôle d’accès en fonction du rôle (RBAC) est géré dans Azure Purview lui-même. Ce guide couvre donc les bases pour sécuriser vos informations.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 8e6f9840f89d3e4c180da366f45548ef54a678dc
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042652"
---
# <a name="access-control-in-azure-purview"></a>Contrôle d’accès dans Azure Purview

Azure Purview utilise des **collections** pour organiser et gérer l’accès à ses sources, ressources et autres artefacts. Cet article décrit les collections et la gestion des accès dans votre compte Azure Purview.

> [!NOTE]
> À ce stade, ces informations s’appliquent uniquement aux comptes Azure Purview créés **à partir du 18 août 2021**. Les instances créées avant le 18 août peuvent créer des collections, mais elles ne gèrent pas les autorisations par le biais de ces collections. Pour plus d’informations sur le contrôle d’accès pour une instance Purview créée avant le 18 août, consultez notre [**guide des autorisations héritées**](#legacy-permission-guide) en bas de page.
>
> Tous les comptes hérités seront automatiquement mis à niveau dans les semaines à venir. Vous recevrez une notification par e-mail lors de la mise à niveau de votre compte Purview. Pour plus d’informations sur ce qui va changer une fois votre compte mis à niveau, consultez notre [Guide des comptes mis à niveau](concept-account-upgrade.md).

## <a name="collections"></a>Collections

Une collection est un outil utilisé par Azure Purview pour regrouper des ressources, des sources et d’autres artefacts dans une hiérarchie afin de faciliter la détection et de gérer le contrôle d’accès. Tous les accès aux ressources de Purview sont gérés à partir de collections dans le compte Purview lui-même.

## <a name="roles"></a>Rôles

Azure Purview utilise un ensemble de rôles prédéfinis pour contrôler qui peut accéder à quoi dans le compte. Ces rôles sont actuellement les suivants :

- **Administrateurs de collection** : rôle pour les utilisateurs qui doivent attribuer des rôles à d’autres utilisateurs dans Azure Purview ou gérer des collections. Les administrateurs de collection peuvent ajouter des utilisateurs à des rôles sur des collections dont ils sont administrateurs. Ils peuvent également modifier des collections et leurs détails, et ajouter des sous-groupes.
- **Conservateurs de données** : rôle qui fournit l’accès au catalogue de données pour gérer les ressources, configurer des classifications personnalisées, définir des termes de glossaire et afficher les insights. Les conservateurs de données peuvent créer des ressources, les lire, les modifier, les déplacer et les supprimer. Ils peuvent également ajouter des annotations aux ressources.
- **Lecteurs de données** : rôle qui fournit un accès en lecture seule aux ressources de données, aux classifications, aux règles de classification, aux collections, aux termes du glossaire et aux insights.
- **Administrateurs de source de données** : rôle qui autorise un utilisateur à gérer les analyses et sources de données. Un utilisateur ayant le rôle d’administrateur de sources de données n’a pas accès à Azure Purview Studio. La combinaison de ce rôle avec les rôles Lecteur de données ou Conservateur de données dans n’importe quelle étendue de collection fournit l’accès à Azure Purview Studio.

## <a name="who-should-be-assigned-to-what-role"></a>Qui doit être affecté à quelle rôle ?

|Scénario d'utilisateur|Rôle(s) approprié(s)|
|-------------|-----------------|
|J’ai juste besoin de trouver des ressources, je ne veux rien modifier|Lecteur de données|
|J’ai besoin de modifier les informations sur les ressources, d’attribuer des classifications, de les associer à des entrées de glossaire, etc.|Conservateur de données|
|Je dois modifier le glossaire ou configurer de nouvelles définitions de classification|Conservateur de données|
|Le principal du service de mon application doit transmettre (push) des données à Azure Purview|Conservateur de données|
|Je dois configurer des analyses via Purview Studio|Administrateur de sources de données, plus au moins Lecteur de données **ou** Conservateur de données sur la collection où la source est inscrite|
|Je dois permettre à un principal de service ou à un groupe de configurer et de surveiller des analyses dans Azure Purview sans leur permettre d’accéder aux informations du catalogue |Administrateur de sources de données|
|Je dois placer les utilisateurs dans des rôles dans Azure Purview | Administrateur de collections |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="Graphique présentant les rôles Purview" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Comprendre comment utiliser les rôles et les collections d’Azure Purview

Tout le contrôle d’accès est géré dans les collections de Purview. Les collections de Purview se trouvent dans le [studio Purview](https://web.purview.azure.com/resource/). Ouvrez votre compte Purview dans le [portail Azure](https://portal.azure.com) et sélectionnez la vignette Purview Studio sur la page Vue d’ensemble. De là, accédez à la carte de données dans le menu de gauche, puis sélectionnez l’onglet « Collections ».

Lorsqu’un compte Azure Purview est créé, il commence par une collection racine qui porte le même nom que le compte Purview lui-même. Le créateur du compte Purview est automatiquement ajouté en tant qu’Administrateur de collections, Administrateur de sources de données, Conservateur de données et Lecteur de données sur cette collection racine, et il peut modifier et gérer cette collection.

Des sources, des ressources et des objets peuvent être ajoutés directement à cette collection racine, mais aussi à d’autres collections. L’ajout de collections vous permettra de mieux contrôler qui a accès aux données dans votre compte Purview.

Tous les autres utilisateurs ne peuvent accéder aux informations du compte Azure Purview que si eux-mêmes, ou un groupe dont ils font partie, se voient attribuer l’un des rôles ci-dessus. Cela signifie que, lorsqu’un compte Azure Purview est créé, personne d’autre que le créateur ne peut accéder à ses API ou les utiliser tant qu’il n’a pas été [ajouté à un ou plusieurs des rôles ci-dessus dans une collection](how-to-create-and-manage-collections.md#add-role-assignments).

Les utilisateurs peuvent uniquement être ajoutés à une collection par l’intermédiaire d’un administrateur de collection ou par le bais d’un héritage d’autorisations. Les autorisations d’une collection parent sont automatiquement héritées par ses sous-collections. Cependant, vous pouvez choisir de [restreindre l’héritage d’autorisations](how-to-create-and-manage-collections.md#restrict-inheritance) sur n’importe quelle collection. Dans ce cas, ses sous-collections n’hériteront plus des autorisations du parent et devront être ajoutées directement, bien que les administrateurs de collections qui sont automatiquement hérités d’une collection parent ne puissent pas être supprimés.

## <a name="assign-permissions-to-your-users"></a>Attribuer des autorisations à vos utilisateurs

Après la création d’un compte Azure Purview, la première chose à faire est de créer des collections et d’attribuer des rôles aux utilisateurs dans ces collections.

### <a name="create-collections"></a>Créer des regroupements

Les collections peuvent être personnalisées pour la structure des sources dans votre compte Purview, et elles peuvent agir comme des classes de stockage organisées pour ces ressources. Lorsque vous réfléchissez aux collections dont vous pourriez avoir besoin, pensez à la manière dont vos utilisateurs vont accéder aux informations ou les découvrir. Vos sources sont-elles réparties par services ? Existe-t-il des groupes spécialisés au sein de ces services qui auront seulement besoin de découvrir certaines ressources ? Certaines sources doivent-elles être accessibles à tous vos utilisateurs ?

Cela vous permettra de déterminer les collections et sous-collections dont vous aurez besoin pour organiser le plus efficacement possible votre carte de données.

Les nouvelles collections peuvent être ajoutées directement à la carte des données, où vous pouvez choisir leur collection parent dans une liste déroulante, ou elles peuvent être ajoutées à partir de la collection parent en tant que sous-collection. Dans la vue de la carte des données, vous pouvez voir toutes vos sources et ressources classées par collections et, dans la liste, la collection de la source est répertoriée.

Pour obtenir des instructions et des informations supplémentaires, vous pouvez suivre notre [guide de création et de gestion des collections](how-to-create-and-manage-collections.md).

#### <a name="a-collections-example"></a>Exemple de collections

Maintenant que nous avons une compréhension de base des collections, des autorisations et de leur fonctionnement, voyons un exemple.

:::image type="content" source="./media/catalog-permissions/collection-example.png" alt-text="Graphique montrant un exemple de hiérarchie de collections, répartie par région et par service." border="true":::

Voici une façon dont une entreprise pourrait structurer ses données : à partir de la collection racine (Contoso, dans cet exemple), les collections sont organisées en régions, puis en services et sous-services. Des sources de données et des ressources peuvent être ajoutées à l’une de ces collections afin d’organiser les ressources de données par région et par service et de gérer le contrôle d’accès en conséquence. Un sous-service, Chiffre d’affaires, est soumis à des directives d’accès strictes, de sorte que les autorisations doivent être gérées de manière rigoureuse.

Le [rôle Lecteur de données](#roles) peut accéder aux informations du catalogue, mais pas les gérer ni les modifier. Ainsi, dans notre exemple ci-dessus, l’ajout de l’autorisation Lecteur de données à un groupe de la collection racine et l’autorisation de l’héritage donneront à tous les utilisateurs de ce groupe des autorisations de lecture sur les sources et les ressources Purview. Cela rend ces ressources accessibles, mais non modifiables, par tous les membres de ce groupe. [Restreindre l’héritage](how-to-create-and-manage-collections.md#restrict-inheritance) sur le groupe Chiffre d’affaires permet de contrôler l’accès à ces ressources. Les utilisateurs qui ont besoin d’accéder aux informations relatives au chiffre d’affaire peuvent être ajoutés séparément à la collection Chiffre d’affaires.
De même, avec les rôles Conservateur de données et Administrateur de sources de données, les autorisations pour ces groupes commencent dans la collection à laquelle elles sont attribuées et descendent jusqu’aux sous-collections qui n’ont pas restreint l’héritage. Ci-dessous, nous avons attribué des autorisations à plusieurs groupes au niveau des collections dans la sous-collection Amériques.

:::image type="content" source="./media/catalog-permissions/collection-permissions-example.png" alt-text="Graphique montrant un exemple de hiérarchie de collections répartie par région et par service montrant la distribution des autorisations." border="true":::

### <a name="add-users-to-roles"></a>Ajouter des utilisateurs aux rôles

L’attribution de rôle est gérée par les collections. Seul un utilisateur ayant le [rôle d’administrateur de collections](#roles) peut accorder des autorisations aux autres utilisateurs de cette collection. Lorsque de nouvelles autorisations doivent être ajoutées, un administrateur de collection accède à [Purview Studio](https://web.purview.azure.com/resource/), à la carte de données, puis à l’onglet Collections, et sélectionne la collection dans laquelle un utilisateur doit être ajouté. Dans l’onglet Attributions de rôle, il pourra ajouter et gérer les utilisateurs qui ont besoin d’autorisations.

Pour obtenir des instructions complètes, consultez notre [guide pratique sur l’ajout d’attributions de rôle](how-to-create-and-manage-collections.md#add-role-assignments).

## <a name="legacy-permission-guide"></a>Guide des autorisations héritées

> [!NOTE]
> Ce guide des autorisations héritées concerne uniquement les comptes Purview créés avant le 18 août 2021. Les instances créées après cette date doivent suivre le guide ci-dessus.

Cet article décrit la manière dont le contrôle d’accès en fonction du rôle (RBAC) est implémenté dans le [plan de données](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane) d’Azure Purview pour les ressources Purview créées avant le 18 août.

> [!IMPORTANT]
> Le principal qui a créé un compte Purview reçoit automatiquement toutes les autorisations de plan de données, quels que soient les rôles de plan de données dans lesquels ils peuvent ou non se trouver. Pour qu’un autre utilisateur fasse quoi que ce soit dans Azure Purview, il doit se trouver dans au moins l’un des rôles de plan de données prédéfinis.

### <a name="azure-purviews-pre-defined-legacy-data-plane-roles"></a>Rôles de plan de données hérités prédéfinis d’Azure Purview

Azure Purview définit un ensemble de rôles de plan de données prédéfinis qui peuvent être utilisés pour contrôler qui peut accéder à quoi dans Azure Purview. Ces rôles sont les suivants :

* **Rôle de lecteur de données Purview** : peut accéder au portail Purview et lire tout le contenu dans Azure Purview à l’exception des liaisons d’analyse.
* **Rôle de conservateur de données Purview** : peut accéder au portail Purview et lire tout le contenu dans Azure Purview, à l’exception des liaisons d’analyse, peut modifier les informations sur les ressources, peut modifier les définitions de classification et les termes de glossaire, et peut appliquer des classifications et des termes de glossaire aux ressources.
* **Rôle d’administrateur de source de données Purview** : n’a pas accès au portail Purview (l’utilisateur doit également se trouver dans les rôles Lecteur de données ou Conservateur de données) et peut gérer tous les aspects de l’analyse des données dans Azure Purview, mais ne dispose pas d’un accès en lecture ou en écriture au contenu dans Azure Purview en plus de ceux liés à l’analyse.

### <a name="understand-how-to-use-azure-purviews-legacy-data-plane-roles"></a>Comprendre comment utiliser les rôles de plan de données hérités d’Azure Purview

Lors de la création d’un compte Azure Purview, le créateur sera traité comme s’il appartient aux deux rôles Conservateur de données Purview et Administrateur de source de données Purview. Cependant, le créateur du compte n’est pas affecté à ces rôles dans le magasin de rôles. Azure Purview reconnaît que le principal est le créateur du compte et étend ces fonctionnalités à celui-ci en fonction de son identité.

Tous les autres utilisateurs peuvent utiliser le compte Azure Purview seulement s’ils figurent dans au moins un de ces rôles. Cela signifie que lors de la création d’un compte Azure Purview, seul le créateur peut accéder au compte ou utiliser ses API jusqu’à ce qu’il soit placé dans un ou plusieurs des rôles prédéfinis.

Notez que le rôle Administrateur de source de données Purview a deux scénarios pris en charge. Le premier scénario concerne les utilisateurs qui sont des lecteurs de données ou des conservateurs de données Purview qui doivent également être en mesure de créer des analyses. Ces utilisateurs doivent avoir les deux rôles, au moins Lecteur de données Purview ou Conservateur de données Purview, ainsi qu’être affectés au rôle Administrateur de source de données Purview.

L’autre scénario pour Administrateur de source de données Purview concerne les processus de programmation, tels que les principaux du service, qui doivent être en mesure de configurer et de surveiller les analyses, mais ne doivent avoir accès à aucune des données du catalogue.

Ce scénario peut être implémenté en plaçant le principal du service dans le rôle Administrateur de source de données Purview sans être placé dans l’un des deux autres rôles. Le principal n’a pas accès au portail Purview, mais ce n’est pas un problème parce qu’il s’agit d’un principal de programmation et qu’il communique uniquement via des API.

### <a name="putting-users-into-legacy-roles"></a>Placement d’utilisateurs dans des rôles hérités

La première chose à faire après la création d’un compte Azure Purview consiste donc à affecter des personnes à ces rôles.

L’attribution de rôle est gérée via [RBAC d’Azure](../role-based-access-control/overview.md).

Seuls deux rôles de plan de contrôle intégrés dans Azure peuvent affecter des rôles d’utilisateurs, ceux-ci sont soit des propriétaires, soit des administrateurs d’accès utilisateur. Par conséquent, pour placer des personnes dans ces rôles pour Azure Purview, vous devez trouver une personne qui est propriétaire ou administrateur de l’accès utilisateur ou qui le devient.

#### <a name="an-example-of-assigning-someone-to-a-legacy-role"></a>Exemple d’affectation d’une personne à un rôle hérité

1. Accédez à https://portal.azure.com et accédez à votre compte Azure Purview.
1. Sur le côté gauche, sélectionnez **Contrôle d’accès (IAM)**
1. Suivez ensuite les instructions générales fournies [ici](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group).

### <a name="legacy-role-definitions-and-actions"></a>Définitions et actions des rôles hérités

Un rôle est défini comme une collection d’actions. Reportez-vous [ici](../role-based-access-control/role-definitions.md) pour plus d’informations sur la façon dont les rôles sont définis. Et [ici](../role-based-access-control/built-in-roles.md) pour les définitions de rôle pour les rôles d’Azure Purview.

### <a name="getting-added-to-a-legacy-data-plane-role-in-an-azure-purview-account"></a>Ajout à un rôle de plan de données hérité dans un compte Azure Purview

Si vous souhaitez avoir accès à un compte Azure Purview pour pouvoir utiliser son studio ou appeler ses API, vous devez l’ajouter à un rôle de plan de données Azure Purview. Les seules personnes habilitées sont celles qui sont propriétaires ou administrateurs de l’accès utilisateur sur le compte Azure Purview. Pour la plupart des utilisateurs, l’étape suivante consiste à trouver un administrateur local qui peut aider à trouver les personnes permettant d’avoir accès.

En ce qui concerne les utilisateurs qui ont accès au [Portail Azure](https://portal.azure.com) de leur entreprise, ils peuvent rechercher le compte Azure Purview particulier qu’ils souhaitent rejoindre, sélectionner son onglet **Contrôle d’accès (IAM)** et découvrir qui sont les propriétaires ou les administrateurs de l’accès utilisateur (UAA). Toutefois, notez que dans certains cas, les groupes Azure Active Directory ou les principaux de service peuvent être utilisés comme propriétaires ou UAA, auquel cas il n’est pas possible de les contacter directement. Au lieu de cela, il faut trouver un administrateur pouvant fournir une aide.

### <a name="legacy---who-should-be-assigned-to-what-role"></a>Héritage : qui doit être affecté à quel rôle ?

|Scénario d'utilisateur|Rôle(s) approprié(s)|
|-------------|-----------------|
|J’ai juste besoin de trouver des ressources, je ne veux rien modifier|Rôle de lecteur de données Purview|
|Je dois modifier les informations sur les ressources, y placer des classifications, les associer à des entrées de glossaire, etc.|Rôle de conservateur de données Purview|
|Je dois modifier le glossaire ou configurer de nouvelles définitions de classification|Rôle de conservateur de données Purview|
|Le principal du service de mon application doit transmettre (push) des données à Azure Purview|Rôle de conservateur de données Purview|
|Je dois configurer des analyses via Purview Studio|Rôle d’administrateur de source de données Purview plus au moins un rôle de lecteur de données Purview ou un rôle de conservateur de données Purview|
|Je dois activer un principal de service ou une autre identité de programmation pour configurer et surveiller les analyses dans Azure Purview sans autoriser l’identité de programmation à accéder aux informations du catalogue |Rôle d’administrateur de source de données Purview|
|Je dois placer les utilisateurs dans des rôles dans Azure Purview | Propriétaire ou Administrateur de l’accès utilisateur |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles-legacy.png" alt-text="Graphique présentant les rôles hérités Purview" lightbox="./media/catalog-permissions/collection-permissions-roles-legacy.png":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une compréhension de base des collections et du contrôle d’accès, suivez les guides ci-dessous pour créer et gérer ces collections, ou commencez à inscrire des sources dans votre ressource Purview.

- [Comment créer et gérer des collections](how-to-create-and-manage-collections.md)
- [Sources de données prises en charge par Purview](purview-connector-overview.md)
