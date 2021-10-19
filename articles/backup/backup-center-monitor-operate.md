---
title: Surveiller et utiliser les sauvegardes avec le Centre de sauvegarde
description: Cet article explique comment surveiller et utiliser les sauvegardes à grande échelle à l’aide du Centre de sauvegarde
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: cab9e710cfe4bf43b0d225d64e8f64b16c09e3a6
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659846"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Superviser et utiliser des sauvegardes avec le Centre de sauvegarde

En tant qu’administrateur de sauvegarde, vous pouvez utiliser le Centre de sauvegarde comme volet unique pour superviser vos travaux et l’inventaire des sauvegardes quotidiennement. Vous pouvez également utiliser le Centre de sauvegarde pour effectuer vos opérations courantes, telles que la réponse à des demandes de sauvegarde à la demande, la restauration des sauvegardes, la création de stratégies de sauvegarde, etc.

## <a name="supported-scenarios"></a>Scénarios pris en charge

* Le Centre de sauvegarde est actuellement pris en charge pour la sauvegarde des machines virtuelles Azure, la sauvegarde des machines virtuelles SQL dans Azure, la sauvegarde des machines virtuelles SAP HANA dans Azure, la sauvegarde Azure Files, la sauvegarde des blobs Azure, la sauvegarde des disques managés Azure et la sauvegarde des serveurs Azure Database pour PostgreSQL.
* Reportez-vous à la [matrice de prise en charge ](backup-center-support-matrix.md) pour obtenir une liste détaillée des scénarios pris en charge et non pris en charge.

## <a name="backup-instances"></a>Instances de sauvegarde

Le Centre de sauvegarde facilite la recherche et la découvertibilité des instances de sauvegarde dans l’ensemble de votre espace de sauvegarde.

La sélection de l’onglet **Instances de sauvegarde** du Centre de sauvegarde vous permet d’afficher les détails relatifs à toutes les instances de sauvegarde auxquelles vous avez accès.

 Vous pouvez afficher les informations suivantes pour chacune de vos instances de sauvegarde :

* Abonnement de la source de données
* Groupe de ressources de la source de données
* Dernier point de récupération
* Coffre associé à l’instance de sauvegarde
* État de la protection

 Vous pouvez également filtrer la liste des instances de sauvegarde sur les paramètres suivants :

* Abonnement de la source de données
* Groupe de ressources de la source de données
* Emplacement de la source de données
* Type de source de données
* Coffre
* État de la protection
* Balises de source de données

Cliquez avec le bouton droit sur l’un des éléments de la grille pour effectuer des actions sur l’instance de sauvegarde donnée, comme naviguer jusqu’à la ressource, déclencher des sauvegardes et des restaurations à la demande, ou arrêter la sauvegarde.

![Centre de sauvegarde - Instances](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Travaux de sauvegarde

Le Centre de sauvegarde vous permet d’afficher des informations détaillées sur toutes les tâches créées dans votre espace de sauvegarde et de prendre les mesures qui s’imposent pour les travaux qui ont échoué.

Dans le Centre de sauvegarde, sélectionnez l’élément de menu **Travaux de sauvegarde** pour consulter tous vos travaux. Chaque travail contient les informations suivantes :

* Instance de sauvegarde associée au travail
* Abonnement de la source de données
* Groupe de ressources de la source de données
* Emplacement de la source de données
* Opération de travail
* Statut de tâche
* Heure de début de la tâche
* Durée du travail

Sélectionnez un élément de la grille pour afficher plus de détails sur le travail donné. Cliquez avec le bouton droit sur un élément pour accéder à la ressource et prendre les mesures qui s’imposent.

![Centre de sauvegarde - Travaux](./media/backup-center-monitor-operate/backup-center-jobs.png)

L’onglet **Travaux de sauvegarde** vous permet de consulter les travaux des sept derniers jours. Pour afficher les travaux plus anciens, utilisez [Rapports de sauvegarde](backup-center-obtain-insights.md).

## <a name="vaults"></a>Coffres

Dans le Centre de sauvegarde, sélectionnez l’élément de menu **Coffres** pour consulter la liste de tous les [coffres Recovery Services](backup-azure-recovery-services-vault-overview.md) et [coffres Sauvegarde](backup-vault-overview.md) auxquels vous avez accès. Vous pouvez filtrer la liste à l'aide des paramètres suivants :

* Abonnement du coffre
* Groupe de ressources du coffre
* Nom du coffre
* Type de source de données associé à la stratégie

Sélectionnez un élément de la liste pour accéder à un coffre donné.

![Centre de sauvegarde - Coffres](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Stratégies de sauvegarde

Le Centre de sauvegarde vous permet d’afficher et de modifier les informations sur les clés de vos stratégies de sauvegarde.

Sélectionnez l’élément de menu **Stratégies de sauvegarde** pour consulter toutes les stratégies que vous avez créées dans votre espace de sauvegarde. Vous pouvez filtrer la liste par abonnement de coffre, groupe de ressources, emplacement, type de source de données et coffre. Cliquez avec le bouton droit sur un élément de la grille pour afficher les éléments associés à cette stratégie, modifier la stratégie, voire la supprimer si nécessaire.

![Centre de sauvegarde - Stratégies](./media/backup-center-monitor-operate/backup-center-policies.png)


## <a name="resource-centric-views"></a>Vues centrées sur les ressources

Si votre organisation sauvegarde plusieurs ressources dans un coffre commun, et que chaque propriétaire de ressource souhaite uniquement consulter les informations de sauvegarde des ressources dont il est propriétaire, vous pouvez utiliser la vue centrée sur les ressources dans le centre de sauvegarde. Pour utiliser la vue centrée sur les ressources, activez la case à cocher « Afficher uniquement les informations sur les sources de données auxquelles j’ai accès ». Cette option est actuellement prise en charge pour les onglets suivants : **Vue d’ensemble**, **Instances de sauvegarde**, **Travaux**, **Alertes**. Les charges de travail prises en charge sont les machines virtuelles Azure, les machines virtuelles SQL dans Azure, les machines virtuelles SAP HANA dans Azure, les blobs Azure et les disques Azure.

> [!NOTE]
> Les utilisateurs devront toujours disposer des autorisations RBAC nécessaires sur le coffre, même s’ils utilisent la vue centrée sur les ressources. L’objectif de cette vue est de permettre aux utilisateurs individuels d’éviter d’afficher des informations pour des ressources (par exemple, des machines virtuelles) qu’ils ne possèdent pas.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer votre espace de sauvegarde](backup-center-govern-environment.md)
* [Effectuer des actions à l’aide du Centre de sauvegarde](backup-center-actions.md)
* [Obtenir des insights sur vos sauvegardes](backup-center-obtain-insights.md)
