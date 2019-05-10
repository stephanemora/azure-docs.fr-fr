---
title: 'Sauvegarde Azure : Surveiller la sauvegarde Azure des charges de travail protégées'
description: Surveiller les charges de travail de sauvegarde Azure à l’aide du portail Azure
services: backup
author: pvrk
manager: shivamg
keywords: Sauvegarde Azure ; Alertes ;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ab7d2c0af4bc71733a7995b7e781f0facbfbb29f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236433"
---
# <a name="monitoring-azure-backup-workloads"></a>Surveillance des charges de travail de sauvegarde Azure

Sauvegarde Azure fournit plusieurs solutions de sauvegarde basées sur la topologie exigence et infrastructure de sauvegarde (On-premises vs Azure). N’importe quel utilisateur de sauvegarde ou un administrateur doit s’afficher ce qui se passait dans toutes les solutions et prévu être informé dans les scénarios importants. Cet article décrit en détail les fonctionnalités de surveillance et de notification fournies par le service de sauvegarde Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Travaux de sauvegarde dans le coffre Recovery Services

Azure Backup offre intégrée de surveillance et de fonctionnalités pour les charges de travail protégées par Azure Backup d’alerte. Dans le coffre Recovery Services, paramètres de la **surveillance** section fournit des travaux intégrée et les alertes.

![Surveillance intégrée du coffre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Les tâches sont générées lorsque les opérations telles que la configuration de sauvegarde, sauvegarde, restauration, supprimer la sauvegarde et ainsi de suite, sont effectuées.

Travaux à partir de solutions de sauvegarde Azure suivantes est indiquées ici :

  - Sauvegarde des machines virtuelles Azure
  - Sauvegarde de fichiers Azure
  - Sauvegarde de charges de travail Azure, tels que SQL
  - Agent de sauvegarde Azure (ACM)

Travaux à partir de System Center Data Protection Manager (SC-DPM), serveur de sauvegarde Microsoft Azure (MABS) n’est pas affichés.

> [!NOTE]
> Charges de travail Azure telles que les sauvegardes SQL au sein de machines virtuelles Azure ont un très grand nombre de travaux de sauvegarde. Par exemple, les sauvegardes de journal peuvent exécuter pour toutes les 15 minutes. Par conséquent, pour ces charges de travail de base de données, les opérations de l’utilisateur uniquement déclenchée sont affichées. Les opérations de sauvegarde planifiées ne sont pas affichées.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertes de sauvegarde dans le coffre Recovery Services

Les alertes sont principalement les scénarios où les utilisateurs sont avertis afin qu’ils peuvent prendre les mesures nécessaires. Le **alertes de sauvegarde** section affiche les alertes générées par le service de sauvegarde Azure. Ces alertes sont définies par le service et l’utilisateur ne peut pas personnalisé créer toutes les alertes.

### <a name="alert-scenarios"></a>Scénarios d’alerte
Les scénarios suivants sont définis par le service en tant que scénarios alertables.

  - Échecs de sauvegarde/restauration
  - Sauvegarde réussie avec des avertissements pour l’Agent de sauvegarde Azure (ACM)
  - Conserver les arrêtez la protection des données/arrêter la protection avec suppression des données

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceptions quand une alerte n’est pas déclenchée.
Il existe quelques exceptions près, quand une alerte n’est pas déclenchée suite à un échec, ils sont :

  - L’utilisateur a explicitement annulé le travail en cours d’exécution
  - L’échec du travail, car un autre travail de sauvegarde est en cours d’exécution (rien d’agir ici dans la mesure où il nous faut simplement attendre la tâche précédente se termine)
  - Le travail de sauvegarde de machine virtuelle échoue parce que la machine virtuelle de sauvegarde Azure n’existe plus

Les exceptions ci-dessus sont conçues à partir de la compréhension le résultat de ces opérations (principalement utilisateur déclenchée) s’affiche immédiatement sur les clients du portail/PS/CLI. Par conséquent, l’utilisateur est immédiatement prenant en charge et n’a pas besoin une notification.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Alertes des solutions de sauvegarde Azure suivantes sont affichés ici :

  - Sauvegardes de machines virtuelles Azure
  - Sauvegardes de fichiers Azure
  - Sauvegardes de charge de travail Azure, tels que SQL
  - Agent de sauvegarde Azure (ACM)

> [!NOTE]
> Alertes de System Center Data Protection Manager (SC-DPM), serveur de sauvegarde Microsoft Azure (MABS) ne sont pas affichés ici.

### <a name="alert-types"></a>Types d’alertes
Selon la gravité d’alerte, les alertes peuvent être définies dans les trois types :

  - **Critique** : En principe, toute sauvegarde ou de récupération échec (planifiés ou déclenchés de l’utilisateur) conduirait à la génération d’une alerte et sont affiché en tant qu’une alerte critique et également des opérations destructrices telles que supprimer sauvegarde.
  - **Avertissement** : Si l’opération de sauvegarde réussit, mais avec quelques avertissements, ils sont répertoriés sous forme d’alertes d’avertissement.
  - **D’information**: À ce jour, aucune alerte d’information n’est générée par le service de sauvegarde Azure.

## <a name="notification-for-backup-alerts"></a>Notification des alertes de sauvegarde

> [!NOTE]
> Configuration de notification peut être effectuée uniquement via le portail Azure. Prise en charge de l’interface CLI/PS/REST API/Azure Resource Manager modèle n’est pas pris en charge.

Une fois qu’une alerte est déclenchée, les utilisateurs sont avertis. Sauvegarde Azure fournit un mécanisme intégré de notification par courrier électronique. Il peut s’agir des adresses e-mail individuelles ou des listes de distribution pour être averti lorsqu’une alerte est générée. Vous pouvez également choisir pour être averti chaque alerte ou à les regrouper dans une synthèse horaire et puis recevez une notification.

![Notification par e-mail le coffre de Recovery Services intégré](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Lors de la notification est configurée, vous recevrez un e-mail de bienvenue ou de base. Cela confirme que sauvegarde Azure peut envoyer des e-mails à ces adresses lorsqu’une alerte est déclenchée.<br>

Si la fréquence était définie sur une synthèse horaire et une alerte a été déclenchée et résolue en une heure, il ne sera pas une partie de la synthèse horaire à venir.

> [!NOTE]
>
> * Si une opération destructive comme **arrêter la protection avec suppression des données** est effectuée, une alerte est déclenchée et un e-mail est envoyé aux propriétaires d’abonnements, les administrateurs et coadministrateurs, même si les notifications ne sont pas configurées pour le Service de récupération coffre.
> * Pour configurer la notification pour utilisent des travaux réussis [Analytique de journal](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Étapes suivantes

[Surveiller les charges de travail de sauvegarde Azure à l’aide d’Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
