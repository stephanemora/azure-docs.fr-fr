---
title: Sauvegarder les groupes de disponibilité SQL Server Always On
description: Dans cet article, découvrez comment sauvegarder les groupes de disponibilité SQL Server Always On.
ms.topic: conceptual
ms.date: 08/20/2021
ms.openlocfilehash: 84826d7d6d8f0611c73dcfda25ca3492d9370c97
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115964"
---
# <a name="backup-sql-server-always-on-availability-groups"></a>Sauvegarder les groupes de disponibilité SQL Server Always On

Azure Backup offre un support de bout en bout pour sauvegarder les groupes de disponibilité SQL Server Always On si tous les nœuds se trouvent dans la même région et dans le même abonnement que le coffre Recovery Services. Toutefois, si les nœuds de groupe de disponibilité sont répartis entre les régions/abonnements/emplacements locaux et Azure, il existe quelques considérations à prendre en compte.

>[!Note]
>La sauvegarde des bases de données de groupe de disponibilité de base n’est pas prise en charge par Sauvegarde Azure.

La préférence de sauvegarde utilisée par les groupes de disponibilité SQL de Sauvegarde Azure prend en charge les sauvegardes complètes et différentielles uniquement à partir du réplica principal. Par conséquent, ces tâches de sauvegarde s’exécutent toujours sur le nœud principal, quelle que soit la préférence de sauvegarde. Pour les sauvegardes avec copie complète uniquement et de journal des transactions, la préférence de sauvegarde AG est prise en compte lors de la détermination du nœud où la sauvegarde sera exécutée.

| Préférence de sauvegarde pour les groupes de disponibilité | Les sauvegardes complètes et Diff s’exécutent sur | Les sauvegardes en copie uniquement et de fichier journal sont effectuées à partir de |
| -------------------- | ---------------------------- | ---------------------------------------- |
| Principal | Réplica principal | Réplica principal |
| Secondaire uniquement | Réplica principal | L’un des réplicas secondaires |
| Préférer secondaire | Réplica principal | Les réplicas secondaires sont préférés, mais les sauvegardes peuvent également s’exécuter sur le réplica principal. |
| Aucun/tous | Réplica principal | Tout réplica |

L’extension de sauvegarde de la charge de travail est installée sur le nœud lorsqu’elle est inscrite auprès du service Azure Backup. Lorsqu’une base de données de groupe de disponibilité est configurée pour la sauvegarde, les planifications de sauvegarde sont envoyées à tous les nœuds inscrits du groupe de disponibilité. Les planifications sont activées sur tous les nœuds du groupe de disponibilité et les extensions de sauvegarde de la charge de travail sur ces nœuds se synchronisent entre eux afin de décider quel nœud effectuera la sauvegarde. La sélection du nœud dépend du type de sauvegarde et de la préférence de sauvegarde, comme expliqué dans la section 1. 

Le nœud sélectionné se poursuit avec la tâche de sauvegarde, tandis que la tâche déclenchée sur les autres nœuds est déroulée, c’est-à-dire que la tâche est ignorée.

>[!Note]
>Sauvegarde Azure ne tient pas compte des priorités ou des réplicas de sauvegarde lorsque vous décidez parmi les réplicas secondaires.

## <a name="register-ag-nodes-to-the-recovery-services-vault"></a>Inscrire des nœuds de groupe de disponibilité dans le coffre Recovery Services

Un coffre Recovery Services prend en charge la sauvegarde des bases de données uniquement à partir de machines virtuelles dans la même région et le même abonnement que le coffre.

- Vous devez inscrire le nœud principal dans le coffre (sinon, les sauvegardes complètes ne peuvent pas se produire).
- Si la préférence de sauvegarde est _secondaire uniquement_, vous devez inscrire au moins un nœud secondaire dans le coffre (sinon, les sauvegardes de journal/copie complète uniquement ne peuvent pas se produire).

La configuration des sauvegardes pour les bases de données de groupe de disponibilité échoue avec le code d’erreur _FabricSvcBackupPreferenceCheckFailedUserError_ si les conditions ci-dessus ne sont pas remplies.

Prenons comme référence le déploiement de groupe de disponibilité suivant.

:::image type="content" source="./media/backup-sql-server-on-availability-groups/ag-deployment.png" alt-text="Diagramme pour le déploiement du groupe de disponibilité comme référence.":::

En adoptant l’exemple de déploiement de groupe de disponibilité ci-dessus, les considérations suivantes sont à prendre en compte :

- Comme le nœud principal se trouve dans la région 1 et l’abonnement 1, le coffre Recovery Services (coffre 1) doit être dans la région 1 et l’abonnement 1 pour la protection de ce groupe de disponibilité.
- VM3 ne peut pas être inscrit dans le coffre 1, car il se trouve dans un autre abonnement.
- VM4 ne peut pas être inscrit dans le coffre 1, car il se trouve dans un autre abonnement.
- Si la préférence de sauvegarde est _secondaire uniquement_, VM1 (principal) et VM2 (secondaire) doivent être inscrits dans le coffre 1 (car les sauvegardes complètes nécessitent le nœud principal, et les journaux requièrent un nœud secondaire). Pour les autres préférences de sauvegarde, VM1 (principal) doit être enregistré dans le coffre 1, VM2 est facultatif (car toutes les sauvegardes peuvent s’exécuter sur le nœud principal).
- Si VM3 peut être inscrit dans le coffre 2 de l’abonnement 2 et que les bases de données de groupe de disponibilité s’affichent alors pour la protection dans le coffre 2, mais en raison de l’absence du nœud principal dans le coffre 2, la configuration des sauvegardes échoue.
- De même, si VM4 peut être inscrit dans le coffre 4 de la région 2, la configuration des sauvegardes échouera, car le nœud principal n’est pas inscrit dans le coffre 4.

## <a name="handle-failover"></a>Gérer le basculement

Une fois que le groupe de disponibilité a basculé vers l’un des nœuds secondaires :

- Les sauvegardes complètes et différentielles se poursuivent à partir du nouveau nœud principal s’il est inscrit dans le coffre.
- Les sauvegardes de journaux et de copies complètes uniquement se poursuivent à partir du nœud principal/secondaire en fonction de la préférence de sauvegarde.

>[!Note]
>Les interruptions de la séquence de journaux de transactions consécutifs ne se produisent pas lors du basculement si le basculement ne coïncide pas avec une sauvegarde.

En considérant l’exemple de déploiement de groupe de disponibilité ci-dessus, voici les différentes possibilités de basculement :

- Basculement vers VM2
  - Les sauvegardes complètes et différentielles sont effectuées à partir de VM2.
  - Les sauvegardes de journaux et de copies complètes uniquement sont effectuées à partir de VM1 ou VM2 selon la préférence de sauvegarde.
- Basculement vers VM3 (un autre abonnement)
  - Comme les sauvegardes ne sont pas configurées dans le coffre 2, aucune sauvegarde ne se produit.
  - Si la préférence de sauvegarde n’est pas secondaire uniquement, les sauvegardes peuvent être configurées dans le coffre 2, car le nœud principal est inscrit dans ce coffre. Mais cela peut entraîner des conflits/échecs de sauvegarde. Pour plus d’informations, consultez [Configurer les sauvegardes pour un groupe de disponibilité à plusieurs régions](#configure-backups-for-a-multi-region-ag).
- Basculement vers VM4 (une autre région)
  - Comme les sauvegardes ne sont pas configurées dans le coffre 4, aucune sauvegarde ne se produit.
  - Si la préférence de sauvegarde n’est pas secondaire uniquement, les sauvegardes peuvent être configurées dans le coffre 4, car le nœud principal est inscrit dans ce coffre. Mais cela peut entraîner des conflits/échecs de sauvegarde. Pour plus d’informations, consultez [Configurer les sauvegardes pour un groupe de disponibilité à plusieurs régions](#configure-backups-for-a-multi-region-ag).

## <a name="configure-backups-for-a-multi-region-ag"></a>Configurer des sauvegardes pour un groupe de disponibilité à plusieurs régions

Le coffre Recovery Services ne prend pas en charge les sauvegardes lors du passage d’un abonnement ou d’une région à un(e) autre. Cette section résume comment activer des sauvegardes pour les groupes de disponibilité qui couvrent des abonnements ou des régions Azure, ainsi que les considérations associées.

- Évaluez si vous devez vraiment activer des sauvegardes à partir de tous les nœuds. Si une région/un abonnement possède la plupart des nœuds de groupe de disponibilité et que le basculement vers d’autres nœuds se produit très rarement, la configuration de la sauvegarde dans cette première région peut suffire. Si les basculements vers une autre région/un autre abonnement se produisent fréquemment et pour une durée prolongée, vous souhaiterez peut-être configurer les sauvegardes de manière proactive dans l’autre région.

- Chaque coffre sur lequel la sauvegarde est activée disposera de son propre ensemble de chaînes de points de récupération. Les restaurations à partir de ces points de récupération peuvent être effectuées sur des machines virtuelles inscrites dans ce coffre uniquement.

- Les sauvegardes complètes/différentielles se produisent avec succès uniquement dans le coffre qui possède le nœud principal. Ces sauvegardes dans d’autres coffres continuent d’échouer.

- Les sauvegardes de journaux continueront de fonctionner dans le coffre précédent jusqu’à ce qu’une sauvegarde de journal s’exécute dans le nouveau coffre (c’est-à-dire dans le coffre où le nouveau nœud principal est présent), et _interrompt_ la séquence de journaux pour l’ancien coffre.
  >[!Note]
  >Il existe une limite inconditionnelle de 15 jours au-delà de laquelle les sauvegardes de journaux échoueront.

- Les sauvegardes complètes de copie uniquement fonctionnent dans tous les coffres.

- La protection dans chaque coffre est traitée comme une source de données distincte et est facturée séparément.

Pour éviter les conflits de sauvegarde de journal entre les deux coffres, nous vous recommandons de définir la préférence de sauvegarde sur principal. Ensuite, le coffre ayant le nœud principal effectue également les sauvegardes du journal.

En considérant l’exemple de déploiement de groupe de disponibilité ci-dessus, voici les étapes permettant d’activer la sauvegarde à partir de tous les nœuds. L’hypothèse est que la préférence de sauvegarde est satisfaite à toutes les étapes.

### <a name="step-1-enable-backups-in-region-1-subscription-1-vault-1"></a>Étape 1 : activer les sauvegardes dans la région 1, abonnement 1 (coffre 1)

Comme le nœud principal se trouve dans la région et l’abonnement, les étapes habituelles pour activer les sauvegardes fonctionnent.

### <a name="step-2-enable-backups-in-region-1-subscription-2-vault-2"></a>Étape 2 : activer les sauvegardes dans la région 1, abonnement 2 (coffre 2)

1. Basculez le groupe de disponibilité vers VM3 afin que le nœud principal soit présent dans le coffre 2.
1. Configurez les sauvegardes des bases de données du groupe de disponibilité dans le coffre 2.
1. À ce stade :
   1. Les sauvegardes complètes/différentielles échouent dans le coffre 1, car aucun des nœuds inscrits ne peut effectuer cette sauvegarde.
   1. Les sauvegardes de fichiers journaux échouent dans le coffre 1 jusqu’à ce qu’une sauvegarde de journal s’exécute dans le coffre 2 et _interrompe_ la séquence de journaux pour le coffre 1.
1. Faire basculer le groupe de disponibilité vers VM1.

### <a name="step-3-enable-backups-in-region-2-subscription-1-vault-4"></a>Étape 3 : activer les sauvegardes dans la région 2, abonnement 1 (coffre 4)

Identique à l’étape 2.

## <a name="backup-an-ag-that-spans-azure-and-on-premises"></a>Sauvegarder un groupe de disponibilité qui s’étend sur Azure et sur site

Sauvegarde Azure pour SQL Server ne peut pas être exécuté en local. Si le nœud principal se trouve dans Azure et que la préférence de sauvegarde est satisfaite par les nœuds dans Azure, vous pouvez suivre les instructions ci-dessus pour le groupe de disponibilité à plusieurs régions afin d’activer des sauvegardes pour les réplicas dans Azure. Si un basculement vers un nœud local se produit, les sauvegardes complètes et différentielles dans Azure échouent. Les sauvegardes de fichiers journaux peuvent se poursuivre jusqu’à ce que les interruptions de la séquence de journaux se produisent sur 15 jours.

## <a name="throttling-for-backup-jobs-in-an-ag-database"></a>Limitation des tâches de sauvegarde dans une base de données AG

Actuellement, les limitations de sauvegarde s’appliquent à un niveau d’ordinateur individuel. La limite par défaut est fixée à 20 : si plus de 20 sauvegardes sont déclenchées simultanément, les 20 premières s’exécuteront et les autres seront mises en file d’attente. Lorsque les tâches en cours d’exécution sont terminés, celles qui sont en file d’attente commencent à s’exécuter.

Vous pouvez remplacer cette valeur par une valeur inférieure si les sauvegardes simultanées sont à l’origine de la sollicitation de la mémoire/des E/S/de l’UC sur le nœud.
**Dans la mesure où la limitation se fait au niveau du nœud, l’utilisation de nœuds de groupe de disponibilité déséquilibrés peut entraîner des problèmes de synchronisation de sauvegarde**. Pour comprendre cela, considérez un groupe de disponibilité à 2 nœuds par exemple.

Par exemple, le premier nœud compte 50 bases de données autonomes protégées, et les deux nœuds disposent de 5 bases de données de groupe de disponibilité protégées. En fait, le nœud 1 compte 55 tâches de sauvegarde de base de données planifiées, alors que le nœud 2 n’en compte que 5. En outre, toutes ces sauvegardes sont configurées pour s’exécuter en même temps, toutes les heures. À un moment, l’ensemble des 55 sauvegardes seront déclenchées sur les nœuds 1 et 35. Parmi celles-ci figurent des sauvegardes de la base de données du groupe de disponibilité. Toutefois, sur le nœud 2, les sauvegardes de la base de données du groupe de disponibilité n’ont pas été placées en file d’attente.

Comme les tâches de base de données du groupe de disponibilité sont mises en file d’attente sur un nœud et s’exécutent sur une autre, la synchronisation de sauvegarde (mentionnée dans la section 6) ne fonctionnera pas correctement. Le nœud 2 peut supposer que le nœud 1 est défaillant et, par conséquent, les tâches ne sont pas prévues pour la synchronisation. Cela peut entraîner des interruptions de la séquence de journaux de transactions consécutifs ou des sauvegardes supplémentaires, car les deux nœuds peuvent effectuer des sauvegardes indépendamment.

Un problème similaire peut se produire si le nombre de bases de données protégées du groupe de disponibilité dépasse la limitation. Dans ce cas, la sauvegarde de, par exemple, DB1 peut être mise en file d’attente sur le nœud 1 alors qu’elle s’exécute sur le nœud 2. 

Nous vous recommandons d’utiliser les préférences de sauvegarde suivantes pour éviter ces problèmes de synchronisation :

- Pour un groupe de disponibilité à 2 nœuds, définissez la préférence de sauvegarde sur principal ou sur secondaire uniquement : un seul nœud pourra alors effectuer les sauvegardes, l’autre abandonnera toujours. 
- Pour un groupe de disponibilité avec plus de 2 nœuds, définissez la préférence de sauvegarde sur principal. Ensuite, seul le nœud principal peut effectuer les sauvegardes, d’autres seront exclues.

## <a name="billing-for-ag-backups"></a>Facturation pour les sauvegardes de groupe de disponibilité

Comme pour une instance SQL autonome, une instance de groupe de disponibilité sauvegardée est considérée comme une instance protégée. La taille du serveur frontal total de toutes les bases de données protégées dans une instance est facturée. Prenons l’exemple du déploiement suivant :

:::image type="content" source="./media/backup-sql-server-on-availability-groups/protected-instances-calculation.png" alt-text="Diagramme montrant le calcul des instances protégées des bases de données.":::

Les instances protégées sont calculées comme suit :

| Instance protégée/instance de facturation | Bases de données prises en compte pour le calcul de la taille du serveur frontal |
| ------------------------------------ | -------------------------------------------------- |
| AG1 | DB1, DB2 |
| AG2 | DB4 |
| MV2 | DB3 |
| MV3 | DB6 |
| MV4 | DB5 |

## <a name="moving-a-protected-database-in-or-out-of-an-ag"></a>Déplacement d’une base de données protégée à l’extérieur ou à l’extérieur d’un groupe de disponibilité

Azure Backup considère **SQL instance ou le nom du groupe de disponibilité/de la base de données** comme nom unique de la base de données. Lorsque la base de la version autonome était protégée, son nom unique était _StandAloneInstanceName\DBName_. Lorsqu’il se déplace dans un groupe de disponibilité, le nom unique devient _AGName\DBName_. Les sauvegardes de la base de données autonome échouent avec le code d’erreur suivant : _UserErrorBackupFailedStandaloneDatabaseMovedInToAG_.

La base de données doit être configurée pour la protection à partir du groupe de disponibilité. Elle sera traitée comme une nouvelle source de données avec une chaîne de points de récupération distincte. L’ancienne protection de la base de données autonome peut cesser de conserver les données afin d’éviter le déclenchement et l’échec des sauvegardes ultérieures. De même, lorsqu’une base de données de groupe de disponibilité protégée quitte le groupe de disponibilité et devient une base de données autonome, ses sauvegardes commencent à échouer avec le code d’erreur suivant : _UserErrorBackupFailedDatabaseMovedOutOfAG_.

La base de données doit être configurée pour la protection à partir de l’instance autonome. Elle sera traitée comme une nouvelle source de données avec une chaîne de points de récupération distincte. L’ancienne protection de la base de données du groupe de disponibilité peut cesser de conserver les données afin d’éviter le déclenchement et l’échec des sauvegardes ultérieures.

## <a name="additionremoval-of-a-node-to-an-ag"></a>Ajout/suppression d’un nœud à/d’un groupe de disponibilité

Lorsqu’un nouveau nœud est ajouté à un groupe de disponibilité configuré pour les sauvegardes, les extensions de sauvegarde de la charge de travail qui s’exécutent sur les nœuds du groupe de disponibilité déjà enregistrés détectent la modification de la topologie du groupe de disponibilité et informent le service Sauvegarde Azure lors de la prochaine tâche de découverte de la base de données planifiée. Quand ce nouveau nœud est inscrit pour les sauvegardes dans le même coffre Recovery Services que les autres nœuds existants, le service Sauvegarde Azure déclenche un flux de travail qui configure ce nouveau nœud avec les métadonnées nécessaires à l’exécution des sauvegardes de groupe de disponibilité.

Après cela, le nouveau nœud synchronise les informations de planification de sauvegarde du groupe de disponibilité à partir du service Sauvegarde Azure et commence à participer au processus de sauvegarde synchronisé. Si le nouveau nœud n’est pas en mesure de synchroniser les planifications de sauvegarde et de participer aux sauvegardes, le déclenchement d’une nouvelle inscription sur le nœud force également la reconfiguration du nœud pour les sauvegardes du groupe de disponibilité. De même, l’ajout de nœuds, les extensions de charge de travail détectent la modification de topologie du groupe de disponibilité dans ce cas et informent le service Sauvegarde Azure. Le service démarre un flux de travail d’_annulation de la configuration_ de nœud dans le nœud supprimé afin d’effacer les planifications de sauvegarde pour les bases de données du groupe de disponibilité et supprimer les métadonnées associées au groupe de disponibilité.

## <a name="un-register-an-ag-node-from-azure-backup"></a>Annuler l’inscription d’un nœud de groupe de disponibilité à partir de Sauvegarde Azure

Si un nœud fait partie d’un groupe de disponibilité avec une ou plusieurs bases de données configurées pour la sauvegarde, la sauvegarde Azure n’autorise pas l’annulation de l’inscription de ce nœud. Cela permet d’éviter de futurs échecs de sauvegarde au cas où la préférence de sauvegarde ne peut pas être satisfaite sans ce nœud. Pour annuler l’inscription du nœud, vous devez d’abord le supprimer du groupe de disponibilité. Une fois le workflow d’_annulation de la configuration_ du nœud terminé, en nettoyant ce nœud, vous pouvez annuler son inscription.

La restauration d’une base de données à partir de Sauvegarde Azure dans un groupe de disponibilité Azure SQL ne prend pas en charge la restauration directe d’une base de données dans le groupe de disponibilité. La base de données doit être restaurée sur une instance SQL autonome, puis doit être jointe à un groupe de disponibilité.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment :

* [Restaurer des bases de données SQL Server sauvegardées](restore-sql-database-azure-vm.md)
* [Gérer les bases de données SQL Server sauvegardées](manage-monitor-sql-database-backup.md)
