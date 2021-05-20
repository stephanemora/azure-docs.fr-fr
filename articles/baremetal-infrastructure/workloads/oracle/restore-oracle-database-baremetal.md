---
title: Restaurer la base de données Oracle
description: Découvrez comment restaurer votre base de données Oracle sur BareMetal Infrastructure avec SnapCenter.
ms.topic: how-to
ms.subservice: workloads
ms.date: 05/07/2021
ms.openlocfilehash: 35cc8bc4cd1aa357bf1eea255f12bcf3bdf4bbcf
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109520476"
---
# <a name="restore-oracle-database"></a>Restaurer la base de données Oracle

Dans cet article, nous allons parcourir les étapes de restauration de votre base de données Oracle sur BareMetal Infrastructure avec SnapCenter.

Pour restaurer votre base de données Oracle sur BareMetal Infrastructure, vous avez le choix entre plusieurs options. Avant d’entreprendre la restauration, nous vous recommandons de consulter la [matrice décisionnelle de la restauration Oracle](oracle-high-availability-recovery.md#choose-your-method-of-recovery). Cette matrice peut vous aider à choisir la méthode de restauration la mieux adaptée en fonction du temps de récupération et de la perte de données potentielle. 

En général, vous restaurez les instantanés des volumes de données et de journaux d’archivage les plus récents, l’objectif étant de restaurer la base de données au dernier point de récupération connu. La restauration d’instantanés est un processus permanent. 

>[!IMPORTANT]
>La sélection de l’instantané à restaurer demande la plus grande prudence. La restauration à partir d’un instantané supprime tous les autres instantanés et les données associées, notamment les instantanés plus récents que celui sélectionné pour la restauration. Nous vous recommandons donc d’aborder le processus de restauration avec circonspection. Par mesure de précaution, privilégiez l’instantané le plus récent si vous avez des doutes concernant l’instantané à récupérer.

## <a name="restore-database-locally-with-restored-archive-logs"></a>Restaurer la base de données localement avec des journaux d’archivage restaurés

Si ce n’est pas déjà fait, mettez la base de données hors connexion avant de tenter une récupération. Après avoir vérifié que la base de données ne s’exécute sur aucun nœud dans Oracle Real Application Clusters (RAC), vous pouvez démarrer. Vous allez commencer par restaurer les journaux d’archivage. 

1. Identifiez les sauvegardes disponibles. Dans SnapCenter, sélectionnez **Resources** dans le menu de gauche.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-available-backups.png" alt-text="Capture d’écran montrant la vue des bases de données.":::

2. Sélectionnez la base de données à restaurer dans la liste. La base de données contient la liste des groupes de ressources créés et les stratégies associées.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/available-backups-list.png" alt-text="Capture d’écran montrant la liste complète des sauvegardes disponibles à restaurer.":::

3. Une liste des sauvegardes principales s’affiche. Les sauvegardes sont identifiées par leur nom et leur type : Log (journal) ou Data (données). Gérez tout d’abord la restauration des journaux, car SnapCenter n’est pas conçu pour restaurer directement les volumes de journaux d’archivage. Identifiez le volume de journal d’archivage nécessitant une restauration. Le volume à restaurer correspond généralement à la dernière sauvegarde. En effet, la plupart des récupérations restaurent par progression tous les journaux d’archivage à partir de la dernière sauvegarde de fichiers de données valide connue.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/primary-backups-list.png" alt-text="Capture d’écran montrant la liste des sauvegardes principales.":::

4. Le fait de sélectionner le volume de journal d’archivage active l’option de montage dans le coin supérieur droit de la liste des sauvegardes. Sélectionnez **Monter**. Dans la page Mount backups (Monter des sauvegardes), dans la liste déroulante, sélectionnez l’hôte à utiliser pour monter la sauvegarde. Vous pouvez sélectionner n’importe quel nœud RAC, mais utilisez le même hôte de récupération pour les journaux d’archivage et les fichiers de données. Copiez le chemin de montage, car vous en aurez besoin à l’étape suivante pour récupérer les fichiers de données. Sélectionnez **Monter**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-backups.png" alt-text="Capture d’écran montrant les sauvegardes à monter.":::

5. Contrairement aux travaux de sauvegarde, la visionneuse de travaux dans cette page n’indique pas l’état du processus de montage. Pour voir l’état du montage, sélectionnez **Monitor** (Superviser) dans le menu de gauche. L’état de chaque travail ayant été exécuté est alors présenté. L’opération de montage doit être la première entrée. Vous pouvez également accéder au chemin de montage copié pour vérifier l’existence des journaux d’archivage.

    L’hôte sélectionné pour le montage du système de fichiers reçoit également une entrée dans /etc/fstab avec le chemin de montage ci-dessus. Il peut être supprimé une fois la récupération terminée.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-mount-output.png" alt-text="Capture d’écran montrant la sortie du montage.":::

6. Ensuite, restaurez les fichiers de données et de contrôle. Comme précédemment, sélectionnez **Resources** dans le menu de gauche, puis la base de données à restaurer. Sélectionnez la sauvegarde de données à restaurer. Là encore, la sauvegarde restaurée est généralement la plus récente. Cette fois-ci, quand vous sélectionnez la sauvegarde de données, l’option de restauration est activée. Sélectionnez **Restaurer**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-selected-data-backup.png" alt-text="Capture d’écran de la sauvegarde de données à restaurer.":::

7. Sous l’onglet **Restore Scope** (Étendue de restauration) : Dans la liste déroulante, sélectionnez le même hôte que celui choisi précédemment pour monter les fichiers journaux. Veillez à sélectionner **All Datafiles** (Tous les fichiers de données), puis cochez la case **Control files** (Fichiers de contrôle). Sélectionnez **Suivant**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-recovery-scope.png" alt-text="Capture d’écran montrant les détails de l’étendue de restauration pour les fichiers de données.":::

8. Sous l’onglet **Recovery Scope** (Étendue de récupération) : Le numéro SCN (System Change Number) de la sauvegarde de fichier journal précédemment montée est entré. L’option **Until SCN** (Jusqu’au SCN) est également sélectionnée. 
 
    Le SCN se trouve dans la liste de toutes les sauvegardes pour chaque sauvegarde. L’emplacement du point de montage précédemment copié est également entré sous **Specify external archive log files locations** (Spécifier les emplacements des fichiers journaux d’archivage externes). Vous pouvez avoir plusieurs emplacements de restauration des journaux d’archivage, mais ceci n’entre pas dans le cadre du présent document. Sélectionnez **Suivant**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-choose-recovery-scope.png" alt-text="Capture d’écran montrant les options d’étendue de récupération.":::

9. Sous l’onglet **PreOps** (Opérations préalables) : Aucun script n’est actuellement recommandé dans le cadre du processus de prérestauration. Sélectionnez **Suivant**.

10. Sous l’onglet PostOps (Opérations postérieures) : Là encore, aucun script n’est pris en charge dans le cadre du processus de post-restauration. Cochez la case **Open the database or container database in READ-WRITE mode after recovery** (Ouvrir la base de données ou la base de données conteneur en mode lecture-écriture après la récupération). Sélectionnez **Suivant**.

11. Sous l’onglet **Notification** : Si un serveur SMTP est disponible et que vous souhaitez être notifié par e-mail du processus de restauration, renseignez les paramètres de messagerie. Sélectionnez **Suivant**.

12. Sous l’onglet **Summary** (Récapitulatif) : Vérifiez que tous les détails sont corrects. Sélectionnez **Terminer**.

13. Vous pouvez voir l’état de la restauration en sélectionnant le travail de restauration dans l’écran inférieur **Activité**. Vous pouvez suivre la progression en sélectionnant les flèches vertes pour ouvrir chaque sous-section de restauration et sa progression.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-job-details.png" alt-text="Capture d’écran montrant les détails du travail de restauration.":::

    Une fois la restauration terminée, toutes les étapes sont cochées. Sélectionnez **Fermer**.

    Les lignes supprimées dans la base de données simple créée sont désormais vérifiées pour déterminer si elles ont bien été restaurées.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/output-of-restored-files.png" alt-text="Capture d’écran montrant la sortie de la vérification des fichiers restaurés.":::

## <a name="clone-database-locally-or-remotely"></a>Cloner la base de données localement ou à distance

Le clonage de la base de données est similaire au processus de restauration. L’opération de clonage de la base de données peut être effectuée localement ou à distance en fonction du résultat souhaité. Le clonage de la base de données dans l’emplacement de reprise d’activité est utile à plusieurs fins, notamment les tests de basculement de reprise d’activité et la mise en place de l’assurance qualité des données de production. Cela suppose que l’emplacement de reprise d’activité est utilisé pour le test, le développement, l’assurance qualité, etc.

### <a name="create-a-clone"></a>Créer un clone

La création d’un clone est une fonctionnalité de SnapCenter qui vous permet d’utiliser un instantané comme référence dans le temps pour capturer un ensemble similaire de données entre le volume parent et le volume cloné à l’aide de pointeurs. Le volume cloné est ensuite accessible en lecture et développé uniquement au moyen d’écritures, tandis que des lectures sont toujours effectuées sur le volume parent. Cette fonctionnalité vous permet de créer un ensemble « dupliqué » de données accessibles à un hôte, sans interférer avec les données qui existent sur le volume parent. 

Cette fonctionnalité est particulièrement utile pour les tests de reprise d’activité, car un système de fichiers temporaire peut être basé sur les mêmes instantanés que ceux utilisés dans une récupération réelle. Vous pouvez vérifier les données et le bon fonctionnement des applications, puis arrêter le test de reprise d’activité sans impacter les volumes de reprise d’activité ou la réplication.

Voici les étapes à effectuer pour cloner une base de données. 

1. Dans SnapCenter, sélectionnez **Resources**, puis la base de données à cloner. Si vous créez le clone localement, passez à l’étape suivante. Si vous restaurez le clone à l’emplacement secondaire, sélectionnez les sauvegardes situées au-dessus de la zone Mirror copies (Copies miroirs).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-manage-copies.png" alt-text="Diagramme de clonage des copies managées de base de données.":::

2. Sélectionnez la sauvegarde de données appropriée dans la liste des sauvegardes fournie. Sélectionnez **Cloner**.

    >[!NOTE]
    >La sauvegarde de données doit avoir été créée avant l’horodatage ou le SCN si un clone à horodatage est nécessaire. 

3. Sous l’onglet **Name** (Nom) : Entrez le nom du **SID** pour le clone. Vérifiez que **Source Volume** (Volume source) et **Destination Volume** (Volume de destination) pour Data (Données) et Archive Logs (Journaux d’archivage) correspondent aux volumes fournis par Microsoft Operations pour le mappage de reprise d’activité. Sélectionnez **Suivant**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/provide-clone-database-sid.png" alt-text="Capture d’écran montrant où entrer le SID de la base de données.":::

4. Sous l’onglet **Locations** (Emplacements) : L’opération de clonage créera les systèmes de fichiers nécessaires pour la récupération aux emplacements indiqués pour les fichiers de données, fichiers de contrôle et journaux de restauration par progression. Nous vous recommandons de les laisser tels quels. L’exploration d’alternatives n’entre pas dans le cadre du présent document. Vérifiez que l’hôte approprié est sélectionné pour l’emplacement de la restauration. Sélectionnez **Suivant**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-select-host.png" alt-text="Capture d’écran montrant comment sélectionner un hôte à cloner.":::

5. Sous l’onglet **Credentials** (Informations d’identification) : Les valeurs des sections Credentials et Oracle Home Settings (Paramètres Oracle Home) sont extraites de l’emplacement de production existant. Nous vous recommandons de les laisser telles quelles, sauf si vous savez que l’emplacement secondaire a des valeurs différentes de celles de l’emplacement principal. Sélectionnez **Suivant**.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-credentials.png" alt-text="Capture d’écran montrant la saisie des informations d’identification de base de données pour le clone.":::

6. Sous l’onglet **PreOps** (Opérations préalables) : Aucun script de prérécupération n’est actuellement pris en charge. Sélectionnez **Suivant**.

7. Sous l’onglet **PostOps** (Opérations postérieures) : Si la base de données est récupérée uniquement jusqu’à une heure ou un SCN donné, sélectionnez la case d’option appropriée, puis ajoutez l’horodatage ou le SCN. SnapCenter récupère la base de données jusqu’à ce point. Sinon, laissez l’option **Until Cancel** (Jusqu’à l’annulation) sélectionnée. Sélectionnez **Suivant**.

8. Sous l’onglet **Notification** : Entrez les informations SMTP nécessaires si vous souhaitez envoyer une notification par e-mail une fois le clonage terminé. Sélectionnez **Suivant**.

9. Sous l’onglet **Summary** (Récapitulatif) : Veillez à entrer le bon **Clone SID** (SID du clone) et à sélectionner l’hôte approprié. Faites défiler la liste pour vous assurer que l’étendue de récupération appropriée a été entrée. Sélectionnez **Terminer**.

10. Le travail de clonage apparaît dans la fenêtre contextuelle active en bas de l’écran. Sélectionnez l’activité de clonage pour afficher les détails du travail. Une fois l’activité terminée, la page Job Details (Détails du travail) ne compte que des coches vertes et indique l’heure de fin. Le clonage prend généralement entre 7 et 10 minutes environ.

11. Une fois le travail terminé, basculez vers l’hôte utilisé comme cible pour le clone et vérifiez les points de montage en utilisant cat/etc/fstab. Cette vérification garantit que les points de montage appropriés précédemment listés dans l’Assistant Clonage existent pour la base de données. Le SID de base de données entré dans l’Assistant est également mis en évidence. Dans l’exemple ci-dessous, le SID est dbsc4 (fourni par les points de montage sur l’hôte).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/clone-database-switch-to-target-host.png" alt-text="Capture d’écran de la commande permettant de basculer vers l’hôte cible.":::

12. Sur l’hôte, tapez **oraenv**, puis appuyez sur **Entrée**. Entrez le SID nouvellement créé.

    Il vous appartient de vérifier que la base de données est correctement restaurée. Toutefois, les étapes supplémentaires suivantes sont basées sur la base de données créée par un utilisateur autre qu’Oracle. 

13. Entrez **sqlplus / as sysdba**. Étant donné que la table a été créée sous un autre utilisateur, le nom d’utilisateur et le mot de passe d’origine non valides sont entrés automatiquement. Entrez le nom d’utilisateur et le mot de passe appropriés. L’invite SQL> apparaît une fois la connexion réussie.

Entrez une requête de la base de données de base pour vérifier que les données appropriées ont été reçues. Dans l’exemple suivant, nous utilisons les journaux d’archivage pour restaurer par progression la base de données. L’exemple suivant montre que les journaux d’archivage ont été correctement utilisés puisque l’entrée clonetest est créée après la création de la sauvegarde de données. Par conséquent, si les journaux d’archivage ne sont pas restaurés par progression, cette entrée n’est pas listée.

```sql
SQL> select * from acolvin.t;

COL1
-----------------------------------------
COL2
---------------
first insert
17-DEC-20

log restore
17-DEC-20

clonetest
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
after first insert
17-DEC-20

next insert
17-DEC-20

final insert
18-DEC-20

COL1
-----------------------------------------
COL2
---------------
BILLY
17-DEC-20

7 rows selected.

```

### <a name="delete-a-clone"></a>Supprimer un clone

Quand vous n’avez plus besoin d’un clone, il est important de le supprimer. Si vous envisagez de continuer à l’utiliser, fractionnez-le. Un instantané qui est le parent d’un clone ne peut pas être supprimé et est ignoré dans le cadre du décompte de rétention. Si vous ne supprimez pas les clones devenus inutiles, le nombre d’instantanés que vous gérez peut utiliser un stockage excessif. 

Un clone fractionné copie les données du volume et de l’instantané existants dans un nouveau volume. Ce processus rompt la relation entre le clone et l’instantané parent, autorisant ainsi la suppression de l’instantané quand son numéro de rétention est atteint. Si son numéro de rétention a déjà été atteint, l’instantané est supprimé lors de la capture instantanée suivante. Notez toutefois qu’un clone fractionné entraîne des coûts de stockage.

Quand un clone est créé, l’onglet des ressources de cette base de données liste un clone présent, localement ou à distance. Pour supprimer le clone :

1. Sous l’onglet **Resource** : Cochez la case contenant le clone que vous souhaitez supprimer.

2. La page Secondary Mirror Clone(s) (Clones miroirs secondaires) affiche le clone. Dans cet exemple, le clone est dans l’emplacement secondaire. Sélectionnez **Delete** (Supprimer) en haut à droite de la liste des clones.

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-secondary-mirror-clones.png" alt-text="Capture d’écran des clones miroirs secondaires.":::

3. Veillez à quitter SQLPLUS avant de lancer l’exécution. Sélectionnez **OK**.

4. Pour voir la progression du travail, sélectionnez **Jobs** (Travaux) dans le menu de gauche. Une coche verte indique quand le clone a été supprimé.

5. Une fois le clone supprimé, il peut également être utile de démonter les journaux d’archivage qui ont été utilisés dans le cadre du processus de clonage, le cas échéant. De retour dans la liste de sauvegardes appropriée (issue de la phase de création du clone), vous pouvez trier la liste selon que les sauvegardes ont été montées ou non. L’algorithme de tri n’est pas parfait et ne trie pas systématiquement tous les éléments ensemble, mais il trie généralement dans le bon sens. Le volume suivant a été précédemment monté. Sélectionnez **Unmount** (Démonter).

    :::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/delete-clone-unmount-backup.png" alt-text="Capture d’écran montrant la liste des sauvegardes.":::

6. Sélectionnez **OK**. Pour voir l’état du travail de démontage, sélectionnez **Monitor** (Superviser) dans le menu de gauche. Une coche verte apparaît quand la sauvegarde est démontée.

### <a name="split-a-clone"></a>Diviser un clone

La division d’un clone crée une copie du volume parent en répliquant toutes les données du volume parent jusqu’au moment où l’instantané utilisé pour créer le clone a été créé. Ce processus sépare le volume parent du volume du clone et supprime la suspension sur l’instantané utilisé pour créer le volume du clone. L’instantané peut ensuite être supprimé dans le cadre de la stratégie de rétention.

La division d’un clone est utile pour remplir les données dans l’environnement de production ou de reprise d’activité. La division permet aux nouveaux volumes de fonctionner indépendamment du volume parent.

>[!NOTE]
>Le processus de division d’un clone ne peut pas être inversé ou annulé.

Pour diviser un clone :

1. Sélectionnez la base de données qui contient déjà un clone.

2. Une fois l’emplacement du clone sélectionné, celui-ci est listé dans la page Secondary Mirror Clone(s) (Clones miroirs secondaires).

3. Juste au-dessus de la liste des clones, sélectionnez **Clone Split** (Division de clone). SnapCenter n’autorisant aucune modification dans le cadre du processus de division, la page suivante montre simplement les modifications qui se produiront. Sélectionnez **Démarrer**.

    >[!NOTE]
    >Le processus de division peut prendre beaucoup de temps en fonction de la quantité de données à copier, de la disposition de la base de données dans le stockage et du niveau d’activité du stockage.

Une fois le processus terminé, le clone qui était divisé est supprimé de la liste des sauvegardes. L’instantané associé au clone peut alors être supprimé dans le cadre du plan de conservation normal sur SnapCenter.

## <a name="restore-database-remotely-after-disaster-recovery-event"></a>Restaurer la base de données à distance après un événement de reprise d’activité

SnapCenter n’est pas actuellement conçu pour automatiser le processus de basculement. Si un événement de reprise d’activité se produit, des scripts de récupération Microsoft Operations ou REST sont nécessaires pour restaurer la base de données dans l’emplacement secondaire. Le processus d’exécution des scripts de récupération REST dépasse le cadre de ce document.

## <a name="restart-all-rac-nodes-after-restore"></a>Redémarrer tous les nœuds RAC après la restauration

Une fois la base de données restaurée dans SnapCenter, elle n’est active que sur le nœud RAC sélectionné lors de la restauration de la base de données, comme indiqué ci-dessous. Dans cet exemple, la base de données a été restauré sur bn6sc2 pour vous montrer que vous pouvez sélectionner n’importe quel nœud RAC pour effectuer la restauration.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-example-output-rac-node.png" alt-text="Capture d’écran montrant que la base de données a été restaurée sur bn6sc2.":::

Démarrez les nœuds RAC restants à l’aide de la commande _srvctl start database_. Une fois la commande terminée, vérifiez l’état pour confirmer la participation de tous les nœuds RAC.

:::image type="content" source="media/netapp-snapcenter-integration-oracle-baremetal/restore-database-srvctl-start-database-command.png" alt-text="Capture d’écran montrant la commande srvctl start database utilisée pour redémarrer les nœuds RAC restants.":::

## <a name="unmount-log-archive-volume"></a>Démonter le volume de journaux d’archivage

Après le clonage ou la restauration d’une base de données, le volume de journaux d’archivage doit être démonté puisqu’il sert uniquement à restaurer la base de données et n’est plus utilisé. 

1. Sous l’onglet **Resources**, sélectionnez la liste de sauvegardes appropriée sur la base de données en sélectionnant la liste locale ou distante.

2. Une fois que vous avez sélectionné la liste de sauvegardes appropriée, triez la liste des sauvegardes selon qu’elles ont été montées ou non. L’algorithme de tri n’est pas parfait et ne trie pas systématiquement tous les éléments ensemble, mais il donne des résultats utilisables. 
 
3. Une fois que vous avez trouvé le volume précédemment monté, sélectionnez **Unmount** (Démonter). Sélectionnez **OK**.

4. Pour voir l’état du travail de démontage, sélectionnez **Monitor** (Superviser) dans le menu de gauche. Une coche verte apparaît quand le volume est démonté.

>[!NOTE]
>Le fait de démonter supprime l’entrée dans /etc/fstab sur l’hôte sur lequel le volume est monté. Si nécessaire, le démontage permet également de supprimer la sauvegarde dans le cadre de la stratégie de rétention définie dans la stratégie de protection.

## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur la haute disponibilité et la reprise d’activité pour Oracle sur BareMetal :

> [!div class="nextstepaction"]
> [Haute disponibilité et récupération d’urgence pour Oracle sur BareMetal](concepts-oracle-high-availability.md)
