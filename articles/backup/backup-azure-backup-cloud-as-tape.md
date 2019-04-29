---
title: Utilisation d’Azure Backup pour remplacer votre infrastructure sur bande
description: Découvrez comment Azure Backup fournit une sémantique de type bande qui permet de sauvegarder et de restaurer des données dans Azure.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 1/10/2017
ms.author: saurse
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59236774f98af927082c78f4b75a1f5880a7cac4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646916"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Déplacement de votre stockage à long terme de la bande vers le cloud Azure
Les clients Azure Backup et System Center Data Protection Manager peuvent effectuer les actions suivantes :

* sauvegarder leurs données selon des planifications qui correspondent le mieux aux besoins de l’organisation ;
* conserver les données sauvegardées pendant plus longtemps ;
* intégrer Azure à leurs besoins de rétention à long terme (à la place d’une bande).

Cet article explique comment les clients peuvent mettre en place des stratégies de sauvegarde et de rétention. Les clients qui utilisent des bandes pour répondre à leurs besoins de rétention à long terme disposent désormais d’une alternative puissante et viable grâce à cette fonctionnalité. La fonctionnalité est activée dans la dernière version d’Azure Backup (disponible [ici](https://aka.ms/azurebackup_agent)). Les clients System Center DPM doivent passer, au minimum, à DPM 2012 R2 UR5 avant d’utiliser DPM avec le service de Sauvegarde Azure.

## <a name="what-is-the-backup-schedule"></a>Qu’est-ce que la planification de sauvegarde ?
La planification de sauvegarde indique la fréquence de l'opération de sauvegarde. Par exemple, les paramètres dans l’écran suivant indiquent que les sauvegardes sont effectuées tous les jours à 18 h 00 et à minuit.

![Planification quotidienne](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Les clients peuvent également planifier une sauvegarde hebdomadaire. Par exemple, les paramètres de l’écran suivant indiquent que les sauvegardes sont effectuées le dimanche et le mercredi à 9 h 30 et à 1 h 00.

![Planification hebdomadaire](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Qu’est-ce que la stratégie de rétention ?
La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une même stratégie pour tous les points de sauvegarde, les clients peuvent spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Par exemple, le point de sauvegarde effectué quotidiennement, qui sert de point de récupération opérationnel, est conservé pendant 90 jours. Le point de sauvegarde effectué à la fin de chaque trimestre à des fins d’audit est conservé pendant une durée plus longue.

![Stratégie de rétention](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Le nombre total de « points de rétention » spécifié dans cette stratégie est 90 (points quotidiens) + 40 (un par trimestre pendant 10 ans) = 130.

## <a name="example--putting-both-together"></a>Exemple – Combinaison des deux
![Exemple d’écran](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Stratégie de rétention quotidienne**: Les sauvegardes effectuées quotidiennement sont stockées pendant sept jours.
2. **Stratégie de rétention hebdomadaire**: Les sauvegardes effectuées tous les jours à minuit et à 18 h 00 samedi sont conservées pendant quatre semaines
3. **Stratégie de rétention mensuelle**: Sauvegardes effectuées à minuit et à 18 h 00 le dernier samedi de chaque mois sont conservées pendant 12 mois
4. **Stratégie de rétention annuelle**: Sauvegardes effectuées à minuit le dernier samedi de chaque mois de mars sont conservées pendant 10 ans

Le nombre total de « points de rétention » (points à partir duquel un client peut restaurer des données) dans le schéma précédent est calculé comme suit :

* deux points par jour pendant sept jours = 14 points de récupération
* deux points par semaine pendant quatre semaines = huit points de récupération
* deux points par mois pendant 12 mois = 24 points de récupération
* un point par an pendant 10 ans = 10 points de récupération

Le nombre total de points de récupération est 56.

> [!NOTE]
> La sauvegarde Azure n'impose aucune restriction sur le nombre de points de récupération.
>
>

## <a name="advanced-configuration"></a>Configuration avancée
En cliquant sur **Modifier** dans l’écran précédent, les clients peuvent spécifier les planifications de rétention de manière plus flexible.

![Modifier](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la Sauvegarde Azure, consultez :

* [Présentation d’Azure Backup](backup-introduction-to-azure-backup.md)
* [Test d’Azure Backup](backup-try-azure-backup-in-10-mins.md)
