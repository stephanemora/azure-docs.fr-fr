---
title: Gérer votre espace de sauvegarde à l’aide du Centre de sauvegarde
description: Apprenez à gérer votre environnement Azure pour vous assurer que toutes vos ressources sont conformes du point de vue de la sauvegarde avec le Centre de sauvegarde.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90992983"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Gérer votre espace de sauvegarde à l’aide du Centre de sauvegarde

Le Centre de sauvegarde vous aide à gérer votre environnement Azure pour vous assurer que toutes vos ressources sont conformes du point de vue de la sauvegarde. Voici quelques-unes des fonctionnalités de gouvernance du Centre de sauvegarde :

* Afficher et affecter des stratégies Azure pour la sauvegarde

* Affichez toutes les sources de source qui n’ont pas été configurées pour la sauvegarde.

## <a name="supported-scenarios"></a>Scénarios pris en charge

* Pour obtenir une liste détaillée des scénarios pris en charge et non pris en charge, consultez la [Matrice de prise en charge](backup-center-support-matrix.md).

## <a name="azure-policies-for-backup"></a>Stratégies Azure pour la sauvegarde

Pour afficher toutes les [stratégies Azure](https://docs.microsoft.com/azure/governance/policy/overview) disponibles pour la sauvegarde, sélectionnez l’élément de menu **Stratégies Azure pour la sauvegarde**. Cela a pour effet d’afficher toutes les [définitions de stratégie Azure pour la sauvegarde](policy-reference.md), tant intégrées que personnalisées, disponibles pour attribution à vos abonnements et groupes de ressources.

La sélection de l’une des définitions vous permet d’[affecter la stratégie](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) à une étendue.

![Sélectionner des définitions Azure Policy](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>Sources de données protégeables

La sélection de l’élément de menu **Sources de données protégeables** vous permet d’afficher toutes les sources de source qui n’ont pas été configurées pour la sauvegarde. Vous pouvez filtrer la liste par abonnement, groupe de ressources, emplacement, type et balises de source de données. Une fois que vous avez identifié une source de données nécessitant une sauvegarde, vous pouvez cliquer avec le bouton droit sur l’élément de grille correspondant et sélectionner **Sauvegarder** pour configurer la sauvegarde pour la ressource.

![Menu des sources de données protégeables](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller et exécuter des sauvegardes](backup-center-monitor-operate.md)
* [Effectuer des actions à l’aide du Centre de sauvegarde](backup-center-actions.md)
* [Obtenir des insights sur vos sauvegardes](backup-center-obtain-insights.md)
