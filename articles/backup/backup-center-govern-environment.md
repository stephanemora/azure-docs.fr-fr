---
title: Gérer votre espace de sauvegarde à l’aide du Centre de sauvegarde
description: Apprenez à gérer votre environnement Azure pour vous assurer que toutes vos ressources sont conformes du point de vue de la sauvegarde avec le Centre de sauvegarde.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 283c99c4b17683850f71b25fb2006784e43f3b8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506204"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Gérer votre espace de sauvegarde à l’aide du Centre de sauvegarde

Le Centre de sauvegarde vous aide à gérer votre environnement Azure en vue de garantir que toutes vos ressources sont conformes du point de vue de la sauvegarde. Voici quelques-unes des fonctionnalités de gouvernance du Centre de sauvegarde :

* Afficher et affecter des stratégies Azure pour la sauvegarde

* Afficher la conformité de vos ressources sur toutes les stratégies Azure intégrées pour la sauvegarde.

* Affichez toutes les sources de source qui n’ont pas été configurées pour la sauvegarde.

## <a name="supported-scenarios"></a>Scénarios pris en charge

* Pour obtenir une liste détaillée des scénarios pris en charge et non pris en charge, consultez la [Matrice de prise en charge](backup-center-support-matrix.md).

## <a name="azure-policies-for-backup"></a>Stratégies Azure pour la sauvegarde

Pour afficher toutes les [stratégies Azure](../governance/policy/overview.md) disponibles pour la sauvegarde, sélectionnez l’élément de menu **Stratégies Azure pour la sauvegarde**. Cela a pour effet d’afficher toutes les [définitions de stratégie Azure pour la sauvegarde](policy-reference.md), tant intégrées que personnalisées, disponibles pour attribution à vos abonnements et groupes de ressources.

La sélection de l’une des définitions vous permet d’[affecter la stratégie](../governance/policy/tutorials/create-and-manage.md#assign-a-policy) à une étendue.

![Sélectionner des définitions Azure Policy](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>Conformité de la sauvegarde

Le fait de cliquer sur l’élément de menu Conformité de la sauvegarde vous permet d’afficher la [conformité](../governance/policy/how-to/get-compliance-data.md) de vos ressources en fonction des différentes stratégies intégrées que vous avez affectées à votre environnement Azure. Vous pouvez afficher le pourcentage des ressources qui sont conformes sur toutes les stratégies, ainsi que les stratégies qui ont une ou plusieurs ressources non conformes.

![Afficher la conformité de la sauvegarde](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>Sources de données protégeables

La sélection de l’élément de menu **Sources de données protégeables** vous permet d’afficher toutes les sources de source qui n’ont pas été configurées pour la sauvegarde. Vous pouvez filtrer la liste par abonnement, groupe de ressources, emplacement, type et balises de source de données. Une fois que vous avez identifié une source de données nécessitant une sauvegarde, vous pouvez cliquer avec le bouton droit sur l’élément de grille correspondant et sélectionner **Sauvegarder** pour configurer la sauvegarde pour la ressource.

![Menu des sources de données protégeables](./media/backup-center-govern-environment/protectable-datasources.png)

> [!NOTE]
> Si vous sélectionnez **SQL dans une machine virtuelle Azure** comme type de source de données, la vue **Sources de données protégeables** affiche la liste de toutes les machines virtuelles de la galerie qui n’ont pas de bases de données SQL configurées pour la sauvegarde.
> Si vous sélectionnez **Stockage Azure (Azure Files)** comme type de source de données, la vue **Sources de données protégeables** affiche la liste de tous les comptes de stockage (prenant en charge les partages de fichiers) qui n’ont pas de partages de fichiers configurés pour la sauvegarde.


## <a name="next-steps"></a>Étapes suivantes

* [Surveiller et exécuter des sauvegardes](backup-center-monitor-operate.md)
* [Effectuer des actions à l’aide du Centre de sauvegarde](backup-center-actions.md)
* [Obtenir des insights sur vos sauvegardes](backup-center-obtain-insights.md)