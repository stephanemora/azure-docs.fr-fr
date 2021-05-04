---
title: Conseils et astuces pour utiliser l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Fournit des conseils et astuces pour utiliser l’outil Azure Application Consistent Snapshot Tool avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 6b777c834917d2a5b1fba72d7c18ef536109675a
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929648"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool"></a>Conseils et astuces pour utiliser l’outil Azure Application Consistent Snapshot Tool

Cet article fournit des conseils et astuces qui peuvent être utiles lorsque vous utilisez AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Limiter les autorisations du principal de service

Il peut être nécessaire de limiter l’étendue du principal de service AzAcSnap.  Pour plus d’informations sur la gestion des accès affinée des ressources Azure, consultez la [documentation sur Azure RBAC](../role-based-access-control/index.yml).  

Voici un exemple de définition de rôle avec les actions minimales requises pour qu’AzAcSnap fonctionne.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

Pour que les options de restauration fonctionnent correctement, le principal du service AzAcSnap doit également être en mesure de créer des volumes.  Dans ce cas, la définition de rôle nécessite une action supplémentaire. par conséquent, le principal de service complet doit ressembler à l’exemple suivant.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Effectuer des instantanés manuellement

Avant d’exécuter des commandes de sauvegarde (`azacsnap -c backup`), contrôlez la configuration en exécutant les commandes de test et vérifiez qu’elles sont exécutées avec succès.  L’exécution correcte de ces tests prouve que `azacsnap` peut communiquer avec la base de données SAP HANA installée et le système de stockage sous-jacent de SAP HANA sur le système **Azure Large Instance** ou **Azure NetApp Files**.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Ensuite, pour effectuer manuellement une sauvegarde d’instantané de base de données, exécutez la commande suivante :

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Configurer la sauvegarde d’instantané automatique

Il est courant sur les systèmes Unix/Linux d’utiliser `cron` pour automatiser l’exécution des commandes sur un système. La pratique standard pour les outils d’instantané consiste à configurer la `crontab`de l’utilisateur.

Voici un exemple de `crontab` pour l’utilisateur `azacsnap` destinée à automatiser les instantanés.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Explication concernant la crontab ci-dessus.

- `MAILTO=""` : l’utilisation d’une valeur vide empêche cron d’essayer automatiquement d’envoyer un e-mail à l’utilisateur lors de l’exécution de l’entrée crontab, car celle-ci risquerait de se retrouver dans le fichier de courrier de l’utilisateur local.
- Les versions raccourcies du minutage des entrées crontab sont explicites :
  - `@monthly` = Exécution une fois par mois, autrement dit "0 0 1 * *".
  - `@weekly`  = Exécution une fois par semaine, autrement dit "0 0 * * 0".
  - `@daily`   = Exécution une fois par jour, autrement dit "0 0 * * *".
  - `@hourly`  = Exécution une fois par heure, autrement dit "0 * * * *".
- Les cinq premières colonnes sont utilisées pour désigner les heures. Reportez-vous aux exemples de colonnes ci-dessous :
  - `0,15,30,45` : toutes les 15 minutes.
  - `0-23` : toutes les heures.
  - `*` : tous les jours.
  - `*` : tous les mois.
  - `*` : tous les jours de la semaine.
- La ligne de commande à exécuter est entourée de parenthèses "()"
  - `. /home/azacsnap/.profile` = extraction du profil de l’utilisateur pour configurer son environnement, y compris $PATH, etc.
  - `cd /home/azacsnap/bin` = modification du répertoire d’exécution sur l’emplacement "/home/azacsnap/bin" dans lequel se trouvent les fichiers config.
  - `azacsnap -c .....` = commande azacsnap complète à exécuter, incluant toutes les options.

D’autres explications concernant le programme cron et le format du fichier crontab sont disponibles ici : <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Les utilisateurs doivent surveiller les tâches cron pour vérifier que les instantanés sont correctement générés.

## <a name="monitor-the-snapshots"></a>Surveiller les instantanés

Les conditions suivantes doivent être surveillées pour garantir l’intégrité du système :

1. Espace disque disponible. Les instantanés consomment lentement l’espace disque, car les blocs de disque plus anciens sont conservés dans l’instantané.
    1. Pour faciliter l’automatisation de la gestion de l’espace disque, utilisez les options `--retention` et `--trim` pour nettoyer automatiquement les anciens instantanés et fichiers journaux de base de données.
1. Réussite de l’exécution des outils d’instantané
    1. Recherchez dans le fichier `*.result` si la dernière exécution de `azacsnap` s’est soldée par une réussite ou un échec.
    1. Consultez le résultat de la commande `azacsnap` dans `/var/log/messages`.
1. Cohérence des instantanés en les restaurant régulièrement sur un autre système.

> [!NOTE]
> Pour répertorier les détails de l’instantané, exécutez la commande `azacsnap -c details`.

## <a name="delete-a-snapshot"></a>Supprimer un instantané

Pour supprimer un instantané, exécutez la commande `azacsnap -c delete`. Il n’est pas possible de supprimer des instantanés au niveau du système d’exploitation. Vous devez utiliser la commande correcte (`azacsnap -c delete`) pour supprimer les instantanés de stockage.

> [!IMPORTANT]
> Faites preuve de vigilance lorsque vous supprimez un instantané, car il est **IMPOSSIBLE** de récupérer les instantanés supprimés.

## <a name="restore-a-snapshot"></a>Restauration d’un instantané

Un instantané de volume de stockage peut être restauré sur un nouveau volume (`-c restore --restore snaptovol`).  Pour Azure Large Instance, il est possible de restaurer l’instantané d’un volume (`-c restore --restore revertvolume`).

> [!NOTE]
> **AUCUNE** commande de récupération de base de données n’est fournie.

Un instantané peut être recopié dans la zone de données SAP HANA, mais SAP HANA ne doit pas être exécuté lorsqu’une copie est effectuée (`cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*`).

Pour Azure Large Instance, vous pouvez contacter l’équipe des opérations Microsoft en ouvrant une demande de service pour restaurer l’instantané de votre choix à partir des instantanés existants disponibles. Vous pouvez ouvrir une demande de service sur le portail Azure : <https://portal.azure.com>

Si vous décidez d’effectuer le basculement de récupération d’urgence, la commande `azacsnap -c restore --restore revertvolume` sur le site de récupération d’urgence met automatiquement à disposition les instantanés de volume les plus récents (`/hana/data` et `/hana/logbackups`) pour permettre une récupération SAP HANA. Utilisez cette commande avec précaution, car elle interrompt la réplication entre les sites de production et de récupération d’urgence.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Configurer des instantanés pour les volumes de démarrage uniquement

> [!IMPORTANT]
> Cette opération s’applique uniquement à Azure Large Instance.

Dans certains cas, les clients disposent déjà d’outils pour protéger SAP HANA et veulent uniquement configurer des instantanés de volume de démarrage.  Dans ce cas, la tâche est simplifiée et les étapes suivantes doivent être effectuées.

1. Effectuez les étapes 1-4 des conditions préalables à l’installation.
1. Activez la communication avec le stockage.
1. Téléchargez le programme d’installation pour installer les outils d’instantané.
1. Finalisez l’installation des outils d’instantané.
1. Créez un nouveau fichier de configuration comme suit. Les détails du volume de démarrage doivent se trouver dans la strophe OtherVolume (entrées utilisateur en <span style="color:red">rouge</span>) :
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Vérifiez le fichier de configuration. Reportez-vous à l’exemple suivant :

    Utilisez la commande `cat` pour afficher le contenu du fichier de configuration :

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume&quot;: &quot;t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Testez la sauvegarde du volume de démarrage.

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Vérifiez qu’elle est répertoriée. Notez l’ajout de l’option `--snapshotfilter` afin de limiter la liste des instantanés renvoyés.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Sortie de la commande :
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 (Build: 20210421.6349)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Configurez la sauvegarde d’instantané automatique.

> [!NOTE]
> La configuration de la communication avec SAP HANA n’est pas nécessaire.

## <a name="restore-a-boot-snapshot"></a>Restaurer un instantané de démarrage

> [!IMPORTANT]
> Cette opération concerne uniquement Azure Large Instance.
> Le serveur sera restauré au moment où l’instantané a été effectué.

Un instantané de démarrage peut être récupéré comme suit :

1. Le client doit arrêter le serveur.
1. Une fois le serveur arrêté, le client doit ouvrir une demande de service contenant l’ID de machine et l’instantané de l’ordinateur à restaurer.
    > Les clients peuvent ouvrir une demande de service sur le portail Azure : <https://portal.azure.com>
1. Microsoft restaure le numéro d’unité logique du système d’exploitation à l’aide de l’ID de machine et l’instantané spécifiés, puis démarre le serveur.
1. Le client doit ensuite vérifier que le serveur est démarré et intègre.

Aucune autre étape n’est requise après la restauration.

## <a name="key-facts-to-know-about-snapshots"></a>Informations clés concernant les instantanés

Attributs clés des instantanés de volume de stockage :

- **Emplacement des instantanés** : les instantanés se trouvent dans un répertoire virtuel (`.snapshot`) au sein du volume.  Consultez les exemples suivants pour **Azure Large Instance** :
  - Base de données : `/hana/data/<SID>/mnt00001/.snapshot`
  - Partagé : `/hana/shared/<SID>/.snapshot`
  - Journaux : `/hana/logbackups/<SID>/.snapshot`
  - Démarrage : les instantanés de démarrage pour HLI ne sont **pas visibles** au niveau du système d’exploitation, mais ils peuvent être répertoriés à l’aide de `azacsnap -c details`.

  > [!NOTE]
  >  `.snapshot` est un dossier *virtuel* masqué en lecture seule qui fournit un accès en lecture seule aux instantanés.

- **Nombre maximal d’instantanés :** le matériel peut supporter jusqu’à 250 instantanés par volume. La commande d’instantané conserve un nombre maximal d’instantanés pour le préfixe en fonction de la rétention définie sur la ligne de commande, et supprime l’instantané le plus ancien s’il dépasse le nombre maximal à conserver.
- **Nom de l’instantané :** le nom de l’instantané contient l’étiquette de préfixe fournie par le client.
- **Taille de l’instantané :** dépend de la taille ou des modifications apportées au niveau de la base de données.
- **Emplacement des fichiers journaux :** la sortie des fichiers journaux générés par les commandes est effectuée dans les dossiers définis dans le fichier de configuration JSON, qui est par défaut un sous-dossier sous lequel la commande est exécutée (par exemple, `./logs`).

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes](azacsnap-troubleshoot.md)
