---
title: Configurer des sauvegardes basées sur les stratégies pour les fichiers NetApp Azure | Microsoft Docs
description: Décrit comment configurer des sauvegardes (planifiées) basées sur une stratégie pour des volumes Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/13/2021
ms.author: b-juche
ms.openlocfilehash: 3ecb9926a03efe792f4a62e7a1cf5477e1e62269
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224177"
---
# <a name="configure-policy-based-backups-for-azure-netapp-files"></a>Configurer des sauvegardes basées sur les stratégies pour les fichiers NetApp Azure 

La sauvegarde Azure NetApp Files prend en charge les sauvegardes *basées sur des stratégies* (planifiées) et les sauvegardes *manuelles* (à la demande) au niveau du volume. Vous pouvez utiliser les deux types de sauvegarde dans le même volume. Pendant le processus de configuration, vous allez activer la fonctionnalité de sauvegarde pour un volume Azure NetApp Files avant de pouvoir effectuer des sauvegardes manuelles ou des sauvegardes basées sur des stratégies. 

Cet article explique comment configurer des sauvegardes basées sur des stratégies.  Pour la configuration de la sauvegarde manuelle, consultez [Configurer les sauvegardes manuelles](backup-configure-manual.md).  

> [!IMPORTANT]
> La fonctionnalité de sauvegarde d’Azure NetApp Files est actuellement en préversion. Vous devez envoyer une demande de liste d’attente pour accéder à la fonctionnalité via la page **[Préversion publique de la sauvegarde Azure NetApp Files](https://aka.ms/anfbackuppreviewsignup)** . Attendez de recevoir un e-mail de confirmation officiel de l’équipe Azure NetApp Files avant d’utiliser la fonctionnalité de sauvegarde Azure NetApp Files.

## <a name="about-policy-based-backups"></a>À propos des sauvegardes basées sur des stratégies  

Pour que la fonctionnalité de sauvegarde basée sur des stratégies fonctionne, un volume doit disposer des deux types de stratégie suivants :  

* Une *stratégie d’instantanés* qui contrôle la création d’instantanés pour le volume.
* Une *stratégie de sauvegarde* qui contrôle les instantanés à utiliser pour la sauvegarde sur le stockage Azure.

Une stratégie d’instantané gère la création d’instantanés sur le volume. Elle est utilisée par la fonctionnalité de sauvegarde pour sauvegarder les instantanés dans le Stockage Azure.   

Les sauvegardes sont des opérations de longue durée. Le système planifie les sauvegardes en fonction de la charge de travail principale (qui reçoit une priorité plus élevée) et exécute les sauvegardes en arrière-plan. En fonction de la taille du volume en cours de sauvegarde, une sauvegarde peut s’exécuter en arrière-plan pendant des heures. Il n’existe aucune option permettant de sélectionner l’heure de début des sauvegardes. Le service effectue les sauvegardes en fonction de la logique interne de planification et d’optimisation. 

L’attribution d’une stratégie crée un instantané de ligne de base qui est l’état actuel du volume et transfère l’instantané vers le Stockage Azure. L’instantané de ligne de base est créé avec un nom commençant par `snapmirror`. Cet instantané de référence est automatiquement supprimé lorsque la première sauvegarde planifiée est terminée (en fonction de la stratégie). Si la stratégie de sauvegarde est attachée à un volume, la liste de sauvegarde est vide jusqu’à ce que l’instantané de ligne de base soit transféré. Une fois la sauvegarde terminée, l’entrée de sauvegarde de base apparaît dans la liste des sauvegardes du volume. Après le transfert de ligne de base, la liste est mise à jour quotidiennement en fonction de la stratégie. Une liste vide de sauvegardes indique que la sauvegarde de base est en cours. Si un volume contient déjà des sauvegardes manuelles existantes avant d’affecter une stratégie de sauvegarde, l’instantané de base n’est pas créé. Un instantané de base est créé uniquement lorsque le volume n’a pas de sauvegardes antérieures.

## <a name="configure-and-apply-a-snapshot-policy"></a>Configurer et appliquer une stratégie d’instantané  

Vous devez créer une stratégie d’instantané et associer la stratégie d’instantané au volume que vous souhaitez sauvegarder. Une même stratégie d’instantané peut être attachée à plusieurs volumes. Les modifications apportées à la stratégie d’instantané peuvent avoir une incidence sur le fonctionnement de la sauvegarde d’un volume. 

1. Connectez-vous au portail Azure et accédez à **Azure NetApp Files**.    
2. Sélectionnez votre compte Azure NetApp Files.   
3. Sélectionnez **Stratégie d’instantané**.   

    ![Capture d’écran montrant comment accéder à l’option Stratégie d’instantané.](../media/azure-netapp-files/backup-navigate-snapshot-policy.png)   

4.  Cliquez sur **Ajouter une stratégie d’instantané**.
5.  Dans la page Stratégie d’instantané qui s’affiche, spécifiez le nombre d’instantanés à conserver et la planification de la création d’instantanés pour le volume. Cliquez sur **Enregistrer**.  

    Actuellement, la fonctionnalité de sauvegarde ne peut sauvegarder que des instantanés quotidiens, hebdomadaires et mensuels. (Les sauvegardes toutes les heures ne sont pas prises en charge).   

    * Pour une configuration d’instantané quotidienne, spécifiez l’heure de la journée à laquelle vous souhaitez créer l’instantané. 
    * Pour une configuration d’instantané hebdomadaire, spécifiez le jour de la semaine et l’heure de la journée où vous souhaitez créer l’instantané. 
    * Pour une configuration d’instantané mensuelle, spécifiez le jour du mois et l’heure de la journée où vous souhaitez créer l’instantané. 
    * Pour chaque configuration d’instantané, spécifiez le nombre d’instantanés que vous souhaitez conserver.

    Par exemple, si vous souhaitez effectuer des sauvegardes quotidiennes, vous devez configurer une stratégie d’instantané avec un nombre d’instantanés et une planification d’instantané quotidienne, puis appliquer cette stratégie d’instantané quotidienne au volume. Si vous modifiez la stratégie d’instantané ou supprimez la configuration d’instantané quotidienne, les nouveaux instantanés quotidiens ne seront pas créés ; les sauvegardes quotidiennes ne sont alors pas effectuées. Le même processus et le même comportement s’appliquent aux sauvegardes hebdomadaires et mensuelles.  

    Vérifiez que chaque instantané a une configuration de planification d’instantané unique. Par défaut, Azure NetApp Files vous empêche de supprimer la dernière sauvegarde. Si plusieurs instantanés ont la même heure (par exemple, la même configuration de planification quotidienne et hebdomadaire), Azure NetApp Files les considère comme les instantanés les plus récents, et la suppression de ces sauvegardes est empêchée.  

    L’exemple suivant présente une configuration de la stratégie d’instantané quotidienne : 

    ![Capture d’écran montrant la configuration de la stratégie d’instantané quotidienne.](../media/azure-netapp-files/backup-daily-snapshot-policy.png)

6.  Appliquez la stratégie d’instantané au volume que vous souhaitez sauvegarder :  

    1. Accédez à la page **Volumes**, cliquez avec le bouton droit sur le volume auquel vous souhaitez appliquer la stratégie d’instantané, puis sélectionnez **Modifier**.   
        ![Capture d’écran montrant le menu d’édition du volume.](../media/azure-netapp-files/backup-volume-edit-menu.png)   

    2. Dans la fenêtre Modifier, sous **Stratégie d’instantanés**, sélectionnez la stratégie à appliquer. Cliquez sur **OK**.   
        ![Capture d’écran qui montre la fenêtre Modifier avec le menu déroulant Stratégie d’instantané.](../media/azure-netapp-files/backup-volume-edit-snapshot-policy.png)    

## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

Une stratégie de sauvegarde permet de protéger un volume en suivant un intervalle planifié régulièrement.  

Vous devez créer une stratégie de sauvegarde et associer la stratégie de sauvegarde au volume que vous souhaitez sauvegarder. Une même stratégie de sauvegarde peut être attachée à plusieurs volumes. Les sauvegardes peuvent être temporairement suspendues soit en désactivant la stratégie, soit en désactivant les sauvegardes au niveau du volume. Les sauvegardes peuvent également être complètement désactivées au niveau du volume, ce qui entraîne le nettoyage de toutes les données associées dans le stockage Azure. Une stratégie de sauvegarde ne peut pas être supprimée si elle est attachée à des volumes.

Pour activer une sauvegarde (planifiée) basée sur une stratégie : 

1. Connectez-vous au portail Azure et accédez à **Azure NetApp Files**. 
2. Sélectionnez votre compte Azure NetApp Files.
3. Sélectionnez **Sauvegardes**. 

    ![Capture d’écran montrant comment naviguer vers l’option Sauvegardes.](../media/azure-netapp-files/backup-navigate.png)

4. Sélectionnez **Stratégies de sauvegarde**.
5. Sélectionnez **Ajouter**. 
6. Dans la page **Stratégie de sauvegarde**, spécifiez le nom de la stratégie de sauvegarde.  Entrez le nombre de sauvegardes que vous souhaitez conserver pour les sauvegardes quotidiennes, hebdomadaires et mensuelles. Cliquez sur **Enregistrer**.

    ![Capture d’écran qui montre la fenêtre Stratégie de sauvegarde.](../media/azure-netapp-files/backup-policy-window-daily.png)

    * Si vous configurez et attachez une stratégie de sauvegarde au volume sans attacher de stratégie d’instantané, la sauvegarde ne fonctionne pas correctement. Il n’y aura qu’un instantané de base transféré vers le Stockage Azure. 
    * Pour chaque stratégie de sauvegarde que vous configurez (par exemple, les sauvegardes quotidiennes), assurez-vous que vous disposez d’une configuration de stratégie d’instantané correspondante (par exemple, instantanés quotidiens).
    * La stratégie de sauvegarde a une dépendance vis-à-vis de la stratégie d’instantané. Si vous n’avez pas encore créé de stratégie d’instantané, vous pouvez configurer les deux stratégies en même temps en cochant la case **Créer une stratégie d’instantané** dans la fenêtre Stratégie de sauvegarde.   

        ![Capture d’écran montrant la fenêtre de stratégie de sauvegarde avec la stratégie d’instantané sélectionnée.](../media/azure-netapp-files/backup-policy-snapshot-policy-option.png)
 
### <a name="example-of-a-valid-configuration"></a>Exemple de configuration valide

L’exemple de configuration suivant vous montre comment configurer une stratégie de protection des données sur le volume avec les 5 derniers instantanés quotidiens, les 4 derniers instantanés hebdomadaires et les 3 derniers instantanés mensuels sur le volume. Cette configuration permet de sauvegarder 15 derniers instantanés quotidiens, 6 derniers instantanés hebdomadaires et 4 derniers instantanés mensuels.

* Stratégie d’instantané :   
    Quotidienne : `Number of Snapshots to Keep = 5`   
    Hebdomadaire : `Number of Snapshots to Keep = 4`   
    Mensuelle : `Number of Snapshots to Keep = 3`
* Stratégie de sauvegarde :   
    Quotidienne : `Daily Backups to Keep = 15`   
    Hebdomadaire : `Weekly Backups to Keep = 6`   
    Mensuelle : `Monthly Backups to Keep = 4`

### <a name="example-of-an-invalid-configuration"></a>Exemple de configuration non valide

L’exemple de configuration suivant a une stratégie de sauvegarde configurée pour les sauvegardes quotidiennes, mais la stratégie d’instantané n’a pas de configuration correspondante. Par conséquent, aucun instantané quotidien n’est créé pour être sauvegardé par la stratégie de sauvegarde. Cette configuration permet de sauvegarder uniquement des instantanés hebdomadaires et mensuels.

* Stratégie d’instantané :   
    Hebdomadaire : `Number of Snapshots to Keep = 4`   
    Mensuelle : `Number of Snapshots to Keep = 3`   
* Stratégie de sauvegarde :   
    Quotidienne : `Daily Backups to Keep = 15`   
    Hebdomadaire : `Weekly Backups to Keep = 6`   
    Mensuelle : `Monthly Backups to Keep = 4`   

## <a name="enable-backup-functionality-for-a-volume-and-assign-a-backup-policy"></a>Activation de la fonctionnalité de sauvegarde pour un volume et attribution d’une stratégie de sauvegarde

Chaque volume Azure NetApp Files doit disposer de la fonctionnalité de sauvegarde activée avant toute sauvegarde (basée sur une stratégie ou manuelle). 

Après avoir activé la fonctionnalité de sauvegarde, vous devez affecter une stratégie de sauvegarde à un volume pour que les sauvegardes basées sur des stratégies prennent effet. (Pour les sauvegardes manuelles, une stratégie de sauvegarde est facultative.)

Pour activer la fonctionnalité de sauvegarde pour un volume :  

1. Allez dans **Volumes** et sélectionnez le volume pour lequel vous voulez activer la sauvegarde.
2. Sélectionnez **Configurer**.
3. Dans la page Configuration des sauvegardes, faites basculer le paramètre **Activé** sur **On**.
4. Dans le menu déroulant **Stratégie d’instantané**, attribuez la stratégie d’instantané à utiliser pour le volume. 
5. Dans le menu déroulant **Stratégie de sauvegarde**, attribuez la stratégie de sauvegarde à utiliser pour le volume. Cliquez sur **OK**.

    Les informations du coffre sont préremplies.  

    ![Capture d’écran montrant la fenêtre Configurer les sauvegardes.](../media/azure-netapp-files/backup-configure-window.png)


## <a name="next-steps"></a>Étapes suivantes  

* [Présentation de la sauvegarde Azure NetApp Files](backup-introduction.md)
* [Exigences et considérations relatives à la sauvegarde Azure NetApp Files](backup-requirements-considerations.md)
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Configuration des sauvegardes manuelles](backup-configure-manual.md)
* [Gestion des stratégies de sauvegarde](backup-manage-policies.md)
* [Recherche dans les sauvegardes](backup-search.md)
* [Restaurer une sauvegarde sur un nouveau volume](backup-restore-new-volume.md)
* [Désactivation de la fonctionnalité de sauvegarde pour un volume](backup-disable.md)
* [Supprimer des sauvegardes d’un volume](backup-delete.md)
* [Métriques de sauvegarde d’un volume](azure-netapp-files-metrics.md#volume-backup-metrics)
* [FAQ sur la sauvegarde Azure NetApp Files](faq-backup.md)


