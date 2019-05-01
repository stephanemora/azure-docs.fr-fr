---
title: Surveiller le serveur de processus Azure Site Recovery
description: Cet article décrit comment surveiller le serveur de processus Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: rayne
ms.openlocfilehash: 5fac369f15edb3ef0be31d3dc7d7434104c18dfe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928166"
---
# <a name="monitor-the-process-server"></a>Surveiller le serveur de traitement.

Cet article explique comment surveiller la [Site Recovery](site-recovery-overview.md) serveur de processus.

- Le serveur de processus est utilisé lorsque vous configurez la récupération d’urgence de machines virtuelles VMware locales sur des serveurs physiques vers Azure.
- Par défaut, le serveur de processus s’exécute sur le serveur de configuration. Il est installé par défaut lorsque vous déployez le serveur de configuration.
- Si vous le souhaitez, à l’échelle et gérer plus grand nombre de machines répliquées et plus grands volumes de trafic de réplication, vous pouvez déployer des serveurs de traitement supplémentaires, la montée en puissance.

[En savoir plus](vmware-physical-azure-config-process-server-overview.md) sur le rôle et le déploiement de serveurs de traitement.

## <a name="monitoring-overview"></a>Vue d’ensemble de la surveillance

Étant donné que le serveur de processus a de nombreux rôles, en particulier dans la mise en cache des données répliquées, la compression et le transfert vers Azure, il est important de surveiller l’intégrité du serveur de processus de manière continue.

Il existe un certain nombre de situations qui couramment affectent les performances de serveur de processus. Problèmes qui affectent les performances aura un effet en cascade sur l’intégrité de la machine virtuelle, par la suite en exécutant un push du serveur de processus et ses machines répliquées dans un état critique. Situations sont les suivantes :

- Grand nombre de machines virtuelles utilisent un serveur de processus, approchant ou dépassant les limites recommandées.
- Machines virtuelles en utilisant le serveur de processus ont un taux de variation élevé.
- Débit du réseau entre les machines virtuelles et le serveur de processus n’est pas suffisant pour charger des données de réplication sur le serveur de processus.
- Débit du réseau entre le serveur de processus et Azure n’est pas suffisant pour charger des données de réplication à partir du serveur de processus vers Azure.

Tous ces problèmes peuvent affecter l’objectif de point de récupération (RPO) de machines virtuelles. 

**Pourquoi ?** Étant donné que la génération d’un point de récupération pour une machine virtuelle nécessite tous les disques sur la machine virtuelle d’un point commun. Si un disque a un taux de variation élevé, la réplication est lente ou le serveur de processus n’est pas optimal, il a un impact sur l’efficacité des points de récupération sont créés.

## <a name="monitor-proactively"></a>Surveiller de manière proactive

Pour éviter des problèmes avec le serveur de processus, il est important de :

- Comprendre les exigences spécifiques pour les serveurs de processus à l’aide de [capacité et des conseils de dimensionnement](site-recovery-plan-capacity-vmware.md#capacity-considerations)et assurez-vous que les serveurs de processus sont déployés et en cours d’exécution en fonction des recommandations.
- Surveiller les alertes et résoudre les problèmes qu’ils se produisent, à vos serveurs de processus en cours d’exécution efficace.


## <a name="process-server-alerts"></a>Alertes du serveur de processus

Le serveur de traitement génère un nombre d’alertes d’intégrité, résumées dans le tableau suivant.

**Type d’alerte** | **Détails**
--- | ---
![Healthy][green] | Serveur de processus est connecté et sain.
![Avertissement][yellow] | Processeur utilisation > 80 % pour au cours des 15 dernières minutes.
![Avertissement][yellow] | Utilisation de mémoire > 80 % au cours des 15 dernières minutes.
![Avertissement][yellow] | Cache dossier espace libre < 30 % au cours des 15 dernières minutes.
![Avertissement][yellow] | Services de serveur de processus ne sont pas en cours d’exécution pour les cours des 15 dernières minutes
![Critique][red] | > 95 % de l’utilisation du processeur pendant au cours des 15 dernières minutes
![Critique][red] | Utilisation de mémoire > 95 % des 15 dernières minutes
![Critique][red] | Cache dossier espace libre < 25 % au cours des 15 dernières minutes.
![Critique][red] | Aucune pulsation du serveur de traitement pendant 15 minutes.

![Clé de table](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> L’état de santé global du serveur de processus repose sur l’alerte pire générée.



## <a name="monitor-process-server-health"></a>Surveiller l’intégrité de serveur de processus

Vous pouvez surveiller l’état d’intégrité de vos serveurs de processus comme suit : 

1. Pour surveiller l’intégrité de la réplication et l’état d’un ordinateur répliqué et de son serveur de processus, dans le coffre > **éléments répliqués**, cliquez sur l’ordinateur que vous souhaitez analyser.
2. Dans **intégrité de la réplication**, vous pouvez surveiller l’état d’intégrité de machine virtuelle. Cliquez sur l’état pour afficher les détails de l’erreur.

    ![Intégrité du serveur de processus dans le tableau de bord de machine virtuelle](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Dans **l’intégrité du serveur de processus**, vous pouvez surveiller l’état du serveur de processus. Afficher les détails correspondants.

    ![Détails du serveur de processus dans le tableau de bord de machine virtuelle](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Contrôle d’intégrité peut aussi être surveillée à l’aide de la représentation sous forme graphique sur la page de la machine virtuelle.
    - Un serveur de traitement de montée en puissance sera mis en surbrillance en orange s’il existe des avertissements associés à elle et rouge si elle a des problèmes critiques. 
    - Si le serveur de processus est en cours d’exécution sur le serveur de configuration dans le déploiement par défaut, puis le serveur de configuration est mises en surbrillance en conséquence.
    - Pour Explorer, cliquez sur le serveur de configuration ou d’un serveur de processus. Notez les problèmes et des recommandations de mise à jour.

Vous pouvez également surveiller les serveurs dans le coffre sous de processus **Infrastructure Site Recovery**. Dans **gérer votre infrastructure Site Recovery**, cliquez sur **serveurs de Configuration**. Sélectionnez le serveur de configuration associé avec le serveur de processus et l’exploration vers le bas dans les détails du serveur de processus.


## <a name="next-steps"></a>Étapes suivantes

- Le cas échéant traiter les problèmes de serveurs, suivez notre [conseils de dépannage](vmware-physical-azure-troubleshoot-process-server.md)
- Si vous avez besoin d’aide, publiez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
