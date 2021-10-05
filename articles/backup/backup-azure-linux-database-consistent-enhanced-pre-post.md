---
title: Captures instantanées de cohérence de bases de données utilisant une infrastructure de pré-post scripts améliorée
description: Découvrez comment la Sauvegarde Azure vous permet de prendre des instantanés cohérents de la base de données, en tirant parti de la sauvegarde de machines virtuelles Azure et de l’utilisation de pré-post scripts
ms.topic: conceptual
ms.date: 09/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa9f4ba3dc344e07a3383c6f8081c9304e3ebbeb
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092202"
---
# <a name="enhanced-pre-post-scripts-for-database-consistent-snapshot"></a>Pré-post scripts améliorés pour un instantané cohérent de la base de données

Le service Sauvegarde Azure fournit déjà une infrastructure de [_pré-post_ scripts](./backup-azure-linux-app-consistent.md) pour assurer la cohérence des applications sur les machines virtuelles Linux à l’aide de la Sauvegarde Azure. Cela implique l'appel d'un pré-script (pour suspendre les applications) avant la capture instantanée des disques et l'appel d'un post-script (commandes permettant de libérer les applications) au terme de la capture instantanée, pour rétablir les applications en mode normal.

La création, le débogage et la maintenance des pré-post scripts peuvent être difficiles. Pour supprimer cette complexité, la Sauvegarde Azure fournit une expérience de pré-post script simplifiée pour les bases de données de texte défilant afin d’obtenir une capture instantanée cohérente des applications avec une surcharge minimale.

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/linux-application-consistent-snapshot.png" alt-text="Diagramme montrant une capture instantanée de cohérence des applications Linux par la Sauvegarde Azure.":::

La nouvelle infrastructure pré-post script _améliorée_ offre les principaux avantages suivants :

- Ces pré-post scripts sont installés directement dans les machines virtuelles Azure avec l’extension de sauvegarde. Cela permet d’éliminer la création et de les télécharger à partir d’un emplacement externe.
- Vous pouvez afficher la définition et le contenu des pré-post scripts dans [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), même envoyer des suggestions et des modifications. Vous pouvez aussi envoyer des suggestions et des modifications via GitHub, qui seront triées et ajoutées pour tirer parti de la communauté plus large.
- Vous pouvez même ajouter de nouveaux pré-post scripts pour d’autres bases de données via [GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), _qui seront triés et traités pour tirer parti de la communauté plus large_.
- L’infrastructure robuste est efficace pour gérer les scénarios, tels que la défaillance de l’exécution ou les blocages pré-script. Dans tous les cas, le post-script s’exécute automatiquement pour restaurer toutes les modifications effectuées dans le pré-script.
- L’infrastructure fournit également un canal de _messagerie_ pour que les outils externes récupèrent les mises à jour et élaborent leur propre plan d’action sur n’importe quel message/événement.

## <a name="solution-flow-preview"></a>Flux de solutions (préversion)

:::image type="content" source="./media/backup-azure-linux-database-consistent-enhanced-pre-post/solution-flow.png" alt-text="Diagramme montrant le flux de solutions.":::

## <a name="support-matrix"></a>Matrice de prise en charge

La liste suivante répertorie les bases de données couvertes par l’infrastructure améliorée :

- [Oracle (en disposition générale)](../virtual-machines/workloads/oracle/oracle-database-backup-azure-backup.md) - [Lien vers la matrice de support](backup-support-matrix-iaas.md#support-matrix-for-managed-pre-post-scripts-for-linux-databases)
- MySQL (préversion)

## <a name="prerequisites"></a>Prérequis

Il vous suffit de modifier un fichier config, _workload.conf_ dans `/etc/azure`, pour fournir les détails de connexion. Cela permet à la Sauvegarde Azure de se connecter à l’application appropriée et d’exécuter des pré et des post-scripts. Dans le fichier config, configurez les paramètres suivants.

```json
[workload]
# valid values are mysql, oracle
workload_name =
command_path = 
linux_user =
credString = 
ipc_folder = 
timeout =
```

Le tableau suivant décrit les paramètres :

|Paramètre  |Obligatoire  |Explication  |
|---------|---------|---------|
|workload_name     |   Oui      |     Celui-ci contient le nom de la base de données pour laquelle vous avez besoin d’une sauvegarde cohérente des applications. Les valeurs actuellement prises en charge sont `oracle` ou `mysql`.    |
|command_path/configuration_path     |         |   Celui-ci contient le chemin d’accès au binaire de la charge de travail. Ce champ n’est pas obligatoire si le binaire de la charge de travail est défini en tant que variable de chemin d'accès.      |
|linux_user     |    Oui     |   Celui-ci contiendra le nom d’utilisateur de l’utilisateur Linux ayant accès à la connexion de l’utilisateur de la base de données. Si cette valeur n’est pas définie, la racine est considérée comme l’utilisateur par défaut.      |
|credString     |         |     Cela correspond à la chaîne d’informations d’identification pour la connexion à la base de données. Cela contiendra la chaîne de connexion entière.    |
|ipc_folder     |         |   La charge de travail peut uniquement écrire dans certains chemins d’accès du système de fichiers. Vous devez fournir ici ce chemin d’accès de dossier afin que le pré-script puisse écrire les états dans ce chemin d’accès au dossier.      |
|timeout     |    Oui     |     Il s’agit de la durée maximale pendant laquelle la base de données sera en état de suspension. La valeur par défaut est 90 secondes. Il n’est pas recommandé de définir une valeur inférieure à 60 secondes.    |

> [!NOTE]
> La définition JSON est un modèle que le service de Sauvegarde Azure peut modifier pour correspondre à une base de données particulière. Pour comprendre le fichier config de chaque base de données, reportez-vous à [chaque manuel de la base de données](#support-matrix).

L’expérience globale à suivre pour utiliser l’infrastructure de pré-post script améliorée est la suivante :

- Préparation de l'environnement de base de données
- Modifier le fichier de configuration
- Déclenchez la sauvegarde de machine virtuelle
- Restaurez des machines virtuelles ou des disques/fichiers à partir du point de récupération cohérent des applications selon les besoins.

## <a name="build-a-database-backup-strategy"></a>Créer une stratégie de sauvegarde de base de données

### <a name="using-snapshots-instead-of-streaming"></a>Utilisation d’instantanés au lieu de la diffusion en continu

En règle générale, les sauvegardes en continu (telles que complète, différentielle ou incrémentielle) et les journaux sont utilisés par les administrateurs de base de données dans leur stratégie de sauvegarde. Voici quelques-unes des tableaux croisés clés de la conception.

- **Performances et coûts** : une sauvegarde complète + journaux sont les plus rapides pendant la restauration, mais impliquent des coûts importants. L’inclusion du type de sauvegarde de diffusion en continu différentielle/incrémentielle réduit les coûts, mais peut avoir un impact sur le niveau de performance de la restauration. Mais les instantanés fournissent la meilleure combinaison entre niveau de performance et coût.  Comme les instantanés sont par nature incrémentiels, ils ont un impact minimal sur le niveau de performance lors de la sauvegarde, sont restaurés rapidement et peuvent également réduire les coûts.
- **Impact sur la base de données/infrastructure** : le niveau de performance d’une sauvegarde en continu dépend des e/s de stockage sous-jacentes et de la bande passante réseau disponible lorsque le flux est ciblé vers un emplacement distant. Les instantanés n’ont pas cette dépendance et la demande en IOPS et bande passante réseau est considérablement réduite.
- **Réutilisation** : les commandes de déclenchement de différents types de sauvegardes de diffusion en continu sont différentes pour chaque base de données. Par conséquent, les scripts ne peuvent pas être facilement réutilisés. En outre, si vous utilisez différents types de sauvegarde, veillez à évaluer la chaîne de dépendance pour maintenir le cycle de vie. Pour les instantanés, il est facile d’écrire un script, car il n’existe aucune chaîne de dépendance.
- **Rétention à long terme** : les sauvegardes complètes sont toujours bénéfiques pour les retentions à long terme, car elles peuvent être déplacées et récupérées indépendamment. Toutefois, pour les sauvegardes opérationnelles avec rétention à court-terme, les instantanés sont favorables.

Par conséquent, un instantané quotidien + journaux avec une sauvegarde complète occasionnelle pour la rétention à long terme constituent la meilleure stratégie de sauvegarde pour les bases de données.

### <a name="log-backup-strategy"></a>Stratégie de sauvegarde de fichier journal

L’infrastructure de pré-post script améliorée repose sur la sauvegarde de machine virtuelle Azure qui planifie la sauvegarde une fois par jour. Par conséquent, la fenêtre de perte de données avec RPO de 24 heures n’est pas adaptée aux bases de données de production. Cette solution est complétée par une stratégie de sauvegarde de fichier journal dans laquelle les sauvegardes de fichier journal sont transmises de manière explicite.

[NFS sur blob](../storage/blobs/network-file-system-protocol-support.md) et [NFS sur AFS (préversion)](../storage/files/files-nfs-protocol.md) facilitent le montage des volumes directement sur les machines virtuelles de base de données et utilisent les clients de bases de données pour transférer les sauvegardes de fichier journal. La fenêtre de perte de données, qui est RPO, correspond à la fréquence des sauvegardes de fichier journal. En outre, les cibles NFS n’ont pas besoin d’être très performantes, car vous n’avez peut-être pas besoin de déclencher une diffusion en continu normale (complète et incrémentielle) pour les sauvegardes opérationnelles une fois que vous avez des instantanés cohérents de bases de données.

>[!NOTE]
>Le pré-script amélioré effectue généralement le vidage de toutes les transactions du journal en transit vers la destination de sauvegarde du fichier journal, avant la suspension de la base de données pour prendre un instantané. Par conséquent, les captures instantanées sont cohérentes pour la base de données et fiables lors de la récupération.

### <a name="recovery-strategy"></a>Stratégie de récupération

Une fois les captures instantanées cohérentes de la base de données effectuées et les sauvegardes de fichier journal transmises à un volume NFS, la stratégie de récupération de la base de données peut utiliser la fonctionnalité de récupération des sauvegardes de machines virtuelles Azure. La capacité des sauvegardes de fichier journal est également appliquée à celle-ci à l’aide du client de la base de données. Voici quelques options de stratégie de récupération :

- Créer de nouvelles machines virtuelles à partir du point de récupération cohérent de bases de données. Le point de montage du journal doit déjà être connecté à la machine virtuelle. Utilisez les clients de bases de données pour exécuter des commandes de récupération pour la récupération jusqu’à une date et heure.
- Créez des disques à partir d’un point de récupération cohérent de la base de données et attachez-les à une autre machine virtuelle cible. Monter ensuite la destination du journal et utilisez les clients de bases de données pour exécuter les commandes de récupération pendant la récupération jusqu’à une date et heure
- Utilisez l’option de récupération de fichier et générez un script. Exécutez le script sur la machine virtuelle cible et attachez le point de récupération en tant que disques iSCSI. Utilisez ensuite les clients de bases de données pour exécuter les fonctions de validation spécifiques à la base de données sur les disques attachés et valider les données de sauvegarde. Utilisez également les clients de bases de données pour exporter/récupérer quelques tables/fichiers au lieu de récupérer la totalité de la base de données.
- Utilisez la fonctionnalité de restauration inter-régions pour effectuer les actions ci-dessus à partir de la région secondaire couplée pendant un sinistre régional.

## <a name="summary"></a>Résumé

À l’aide de captures instantanées cohérente de bases de données + journaux sauvegardés à l’aide d’une solution personnalisée, vous pouvez créer une solution de sauvegarde de base de données performante et économique tirant parti des avantages de la sauvegarde de machine virtuelle Azure et réutilisant des fonctionnalités des clients de bases de données.
