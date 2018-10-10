---
title: Présentation du cycle de vie d’un blueprint Azure Blueprint
description: Découvrez le cycle de vie que traverse un blueprint, ainsi que les détails de chaque phase.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991537"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>Présentation du cycle de vie d’un blueprint Azure Blueprint

Comme de nombreuses ressources dans Azure, les blueprints dans Azure Blueprint ont un cycle de vie normal et naturel. Ils sont créés, déployés puis supprimés quand ils ne sont plus nécessaires ou pertinents.
Les blueprints prennent en charge des opérations de cycle de vie traditionnelles CRUD (création/lecture/mise à jour/suppression), mais les étendent également afin de fournir des niveaux supplémentaires d’état prenant en charge les pipelines d’intégration continue et de déploiement continu (CI/CD) utilisables par ceux qui gèrent leur infrastructure par le biais du code (un élément clé dans DevOps appelé Infrastructure en tant que code (IaC)).

Pour bien comprendre ce qu’est un blueprint et quelles sont les étapes de son cycle de vie, nous allons décrire un cycle de vie standard :

> [!div class="checklist"]
> - Création et modification d’un blueprint
> - Publication du blueprint
> - Création et modification d’une nouvelle version du blueprint
> - Publication d’une nouvelle version du blueprint
> - Suppression d’une version spécifique du blueprint
> - Suppression du blueprint

## <a name="creating-and-editing-a-blueprint"></a>Création et modification d’un blueprint

Quand vous créez un blueprint, vous y ajoutez des artefacts, vous l’enregistrez dans un groupe d’administration, et vous spécifiez un nom unique et une version unique. À ce stade, le blueprint est en mode **Brouillon** et ne peut pas encore être affecté. En revanche, pendant qu’il est en mode **Brouillon**, il peut continuer à être mis à jour et modifié.

Un blueprint en mode **Brouillon** qui n’a jamais été publié est affecté dans la page **Définitions de blueprint** d’une icône différente de ceux qui ont été **publiés**. Le champ **Dernière version** indique également **Brouillon** pour les blueprints qui n’ont jamais été publiés.

Vous créez et modifiez un blueprint avec le [portail Azure](../create-blueprint-portal.md#create-a-blueprint) ou l’[API REST](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Publication d’un blueprint

Une fois que tous les changements souhaités ont été apportés à un blueprint en mode **Brouillon**, vous pouvez le **publier** et l’affecter. La version **publiée** du blueprint ne peut pas être modifiée.
Une fois **publié**, le blueprint est affecté d’une icône différente des blueprints en mode **Brouillon**, et le numéro de version fourni est affiché dans la colonne **Dernière version**.

Vous publiez un blueprint avec le [portail Azure](../create-blueprint-portal.md#publish-a-blueprint) ou l’[API REST](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Création et modification d’une nouvelle version du blueprint

Bien qu’une version **publiée** d’un blueprint ne puisse pas être modifiée, une nouvelle version du blueprint peut être ajoutée au blueprint existant et modifiée en fonction des besoins. Pour cela, vous devez apporter des changements à un blueprint existant. Si le blueprint a déjà été **publié**, quand ces changements sont enregistrés ils sont affichés comme **Changements non publiés** dans la liste des définitions de blueprint. Si vous enregistrez les changements, une version **Brouillon** du blueprint est enregistrée.

Vous modifiez un blueprint avec le [portail Azure](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Publication d’une nouvelle version du blueprint

Tout comme la première version d’un blueprint a été **publiée** pour pouvoir être affectée, chaque version suivante de ce même blueprint doit être **publiée** avant de pouvoir être affectée. Quand des **Changements non publiés** ont été apportés à un blueprint mais n’ont pas encore été **publiés**, le bouton **Publier le blueprint** est disponible dans la page Modifier le blueprint. Si le bouton n’est pas visible, cela signifie que le blueprint a déjà été **publié**, mais qu’il ne comporte aucun **Changement non publié**.

> [!NOTE]
> Un même blueprint peut avoir plusieurs versions **publiées** pouvant chacune être affectées à des abonnements.

Les étapes nécessaires pour publier un blueprint avec des **Changements non publiés** en tant que nouvelle version d’un blueprint existant sont identiques à celles nécessaires pour publier un nouveau blueprint.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Suppression d’une version spécifique du blueprint

Chaque version d’un blueprint est un objet unique qui peut être **publié** individuellement. Cela signifie également que chaque version d’un blueprint peut être supprimée. La suppression d’une version d’un blueprint n’a aucun impact sur les autres versions de ce blueprint.

> [!NOTE]
> Il n’est pas possible de supprimer un blueprint ayant des affectations actives. Supprimez d’abord les affectations, puis supprimez la version du blueprint.

1. Lancez le service Azure Blueprint dans le portail Azure. Pour cela, cliquez sur **Tous les services**, puis recherchez et sélectionnez **Stratégie** dans le volet gauche. Dans la page **Stratégie**, cliquez sur **Blueprints**.

1. Sélectionnez **Définitions de blueprint** dans la page de gauche et utilisez les options de filtre pour trouver le blueprint dont vous souhaitez supprimer une version. Cliquez dessus pour ouvrir la page de modification.

1. Cliquez sur l’onglet **Versions publiées** et recherchez la version que vous souhaitez supprimer.

1. Cliquez avec le bouton droit sur la version à supprimer, puis sélectionnez **Supprimer cette version**.

## <a name="deleting-the-blueprint"></a>Suppression du blueprint

Le blueprint de base peut également être supprimé. La suppression du blueprint de base entraîne la suppression de toutes les versions de ce blueprint, qu’elles soient à l’état **Brouillon** ou **Publié**. Comme avec la suppression d’une version d’un blueprint, la suppression du blueprint de base ne supprime pas les affectations existantes des versions de blueprint.

> [!NOTE]
> Il n’est pas possible de supprimer un blueprint ayant des affectations actives. Supprimez d’abord les affectations, puis supprimez la version du blueprint.

Vous supprimez un blueprint avec le [portail Azure](../create-blueprint-portal.md#delete-a-blueprint) ou l’[API REST](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Affectations

Il existe plusieurs points pendant le cycle de vie au cours desquels un blueprint peut être affecté à un abonnement.
Chaque fois que le mode d’une version du blueprint est **Publié**, cette version peut être affectée à un abonnement. Même quand il existe une version **Brouillon** du blueprint, s’il y a une ou plusieurs versions de blueprint en mode **Publié**, alors chacune de ces versions **publiées** peut être affectée. Cela permet d’utiliser et d’affecter activement des versions d’un blueprint pendant qu’une version plus récente est en cours de développement.

Lors de l’affectation des versions de blueprints, il est important de comprendre où elles sont affectées et avec quels paramètres elles ont été affectées. Les paramètres peuvent être statiques ou dynamiques. Pour plus d’informations, consultez [Paramètres statiques et dynamiques](parameters.md).

### <a name="updating-assignments"></a>Mise à jour des affectations

Quand un blueprint est affecté, l’affectation peut être mise à jour. Plusieurs raisons peuvent motiver la mise à jour d’une affectation existante, notamment quand il s’agit de :

- Ajouter ou supprimer un [verrouillage de ressource](resource-locking.md)
- Modifier la valeur de [paramètres dynamiques](parameters.md#dynamic-parameters)
- Mettre à niveau l’affectation vers une version **publiée** plus récente du blueprint

Pour découvrir comment procéder, consultez [Mettre à jour des affectations existantes](../how-to/update-existing-assignments.md)

## <a name="next-steps"></a>Étapes suivantes

- Découvrir comment utiliser les [paramètres statiques et dynamiques](parameters.md)
- Apprendre à personnaliser [l’ordre de séquencement des blueprints](sequencing-order.md)
- Découvrir comment utiliser le [verrouillage des ressources de blueprint](resource-locking.md)
- Découvrir comment [mettre à jour des affectations existantes](../how-to/update-existing-assignments.md)
- Résoudre les problèmes durant l’affectation d’un blueprint en suivant les étapes de [dépannage général](../troubleshoot/general.md)