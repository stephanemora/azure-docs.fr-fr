---
title: Effectuer une sauvegarde à l’aide de l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Fournit un guide pour l’exécution de la commande de sauvegarde de l’outil Azure Application Consistent Snapshot Tool que vous pouvez utiliser avec Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 17c29fdf88495f6ecc40963eda08858887173fd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730936"
---
# <a name="back-up-using-azure-application-consistent-snapshot-tool-preview"></a>Effectuer une sauvegarde à l’aide de l’outil Azure Application Consistent Snapshot Tool (préversion)

Cet article fournit un guide pour l’exécution de la commande de sauvegarde de l’outil Azure Application Consistent Snapshot Tool que vous pouvez utiliser avec Azure NetApp Files.

## <a name="introduction"></a>Introduction

La commande `azacsnap -c backup` permet de réaliser une sauvegarde basée sur une capture instantanée du stockage.  Cette commande effectue l’orchestration d’une capture instantanée du stockage cohérente avec la base de données sur les volumes de données et une capture instantanée du stockage (sans configuration de cohérence avec la base de données) sur les autres volumes.  

Pour les volumes de données, `azacsnap` prépare la base de données pour une capture instantanée du stockage, puis effectue la capture instantanée du stockage pour tous les volumes configurés, et enfin informe la base de données une fois l’instantané effectué.  Cette commande gère également les entrées de base de données qui enregistrent l’activité de sauvegarde d’instantanés (par exemple, le catalogue de sauvegarde SAP HANA).

## <a name="command-options"></a>Options de commande

La commande `-c backup` prend les trois arguments suivants :

- `--volume=` Type de volume pour l’instantané ; ce paramètre peut contenir `data` ou `other`.
  - `data` Instantané des volumes dans la strophe dataVolume du fichier config.
  - `other` Instantané des volumes dans la strophe otherVolume du fichier config.
  
  > [!NOTE]
  > En créant un fichier config distinct avec le volume de démarrage en tant que otherVolume, il est possible que les captures instantanées `boot` soient effectuées selon une planification totalement différente (par exemple quotidiennement).

- `--prefix=` Préfixe de capture instantanée du client pour le nom de la capture instantanée. Ce paramètre a deux objectifs. En premier lieu, il fournit un nom unique pour le regroupement des instantanés. Deuxièmement, il détermine le nombre `--retention` de captures instantanées de stockage conservées pour l’argument `--prefix`spécifié.

    > [!IMPORTANT]
    > Seuls les caractères alphanumériques (« A-Z, a-z, 0-9 »), le trait de soulignement (« _ ») et le tiret (« - ») sont autorisés.

- `--retention` Nombre de captures instantanées de l’argument `--prefix` défini à conserver. Toutes les captures instantanées supplémentaires sont supprimées une fois qu’un nouvel instantané est pris pour cet argument `--prefix`.

- `--trim` Disponible pour SAP HANA v2 et versions ultérieures, cette option gère le catalogue de sauvegarde et les sauvegardes de journaux et de catalogues sur disque. Le nombre d’entrées à conserver dans le catalogue de sauvegarde est déterminé par l’option `--retention` ci-dessus, et supprime les entrées plus anciennes pour le préfixe défini (`--prefix`) du catalogue de sauvegarde, ainsi que la sauvegarde des journaux physiques associés. Il supprime également toutes les entrées de sauvegarde du journal antérieures à l’entrée de sauvegarde non-journal la plus ancienne. Ces opérations permettent d’empêcher les sauvegardes de journaux d’utiliser tout l’espace disque disponible.

  > [!NOTE]
  > L’exemple de commande suivant conservera 9 captures instantanées de stockage et garantira que le catalogue de sauvegarde est réduit en permanence pour correspondre aux 9 captures instantanées de stockage conservées.

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
    ```

- `[--ssl=]` Paramètre facultatif qui définit la méthode de chiffrement utilisée pour communiquer avec SAP HANA, `openssl` ou `commoncrypto`. S’il est défini, la commande `azacsnap -c backup` s’attend à trouver deux fichiers dans le même répertoire, ces fichiers devant être nommés d’après le SID correspondant. Consultez [Utiliser SSL pour la communication avec SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana). L’exemple suivant prend un instantané de type `hana` avec un préfixe `hana_TEST` et conserve les `9` qui communiquent avec SAP HANA à l’aide de SSL (`openssl`).

    ```bash
    azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim --ssl=openssl
    ```

- `[--configfile <config filename>]` est un paramètre facultatif qui autorise les noms de fichiers config personnalisés.

## <a name="snapshot-backups-are-fast"></a>Les sauvegardes d’instantanés sont rapides

La durée d’une sauvegarde d’instantané est indépendante de la taille du volume : la sauvegarde d’un volume de 10 To est aussi rapide que celle d’un volume de 10 Go.  

Les principaux facteurs qui ont un impact sur la durée d’exécution globale sont le nombre de volumes pour lesquels des instantanés doivent être effectués et toute modification apportée au paramètre `--retention` (où une réduction peut augmenter la durée d’exécution en cas de suppression d’instantanés supplémentaires).

Dans l’exemple de configuration ci-dessus (pour **Azure Large Instance**), l’exécution de captures instantanées pour les deux volumes a pris moins de 5 secondes. Pour **Azure NetApp Files**, les instantanés des deux volumes prennent environ 60 secondes.

> [!NOTE]
> Si l’argument `--retention` est considérablement réduit par rapport à la précédente exécution de `azacsnap` (par exemple, de `--retention 50` à `--retention 5`), le temps nécessaire augmente à mesure que `azacsnap` doit supprimer les instantanés supplémentaires.

## <a name="example-with-data-parameter"></a>Exemple avec le paramètre `data`

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention 9 --trim
```

La commande ne génère pas de sortie sur la console, mais écrit dans un fichier journal, un fichier de résultats et `/var/log/messages`.

Le *fichier journal* est constitué du nom de commande + l’option -c + le nom du fichier config. Par défaut, le nom de fichier journal d’un argument `-c backup` s’exécute avec un nom de fichier de configuration par défaut : `azacsnap-backup-azacsnap.log`.

Le fichier de *résultats* a le même nom de base que le fichier journal, avec `.result` comme suffixe, par exemple `azacsnap-backup-azacsnap.result` qui contient la sortie suivante :

```bash
cat logs/azacsnap-backup-azacsnap.result
```

```output
Database # 1 (H80) : completed ok
```

Le fichier `/var/log/messages` contient la même sortie que le fichier `.result`. Examinez l’exemple suivant (exécution comme racine) :

```bash
grep "azacsnap.*Database" /var/log/messages | tail -n10
```

```output
Jul  2 05:22:07 server01 azacsnap[183868]: Database # 1 (H80) : completed ok
Jul  2 05:27:06 server01 azacsnap[4069]: Database # 1 (H80) : completed ok
Jul  2 05:32:07 server01 azacsnap[19769]: Database # 1 (H80) : completed ok
Jul  2 05:37:06 server01 azacsnap[35312]: Database # 1 (H80) : completed ok
Jul  2 05:42:06 server01 azacsnap[50877]: Database # 1 (H80) : completed ok
Jul  2 05:47:06 server01 azacsnap[66429]: Database # 1 (H80) : completed ok
Jul  2 05:52:06 server01 azacsnap[82964]: Database # 1 (H80) : completed ok
Jul  2 05:57:06 server01 azacsnap[98522]: Database # 1 (H80) : completed ok
Jul  2 05:59:13 server01 azacsnap[105519]: Database # 1 (H80) : completed ok
Jul  2 06:02:06 server01 azacsnap[114280]: Database # 1 (H80) : completed ok
```

## <a name="example-with-other-parameter"></a>Exemple avec le paramètre `other`

```bash
azacsnap -c backup --volume other --prefix logs_TEST --retention 9
```

La commande ne génère pas de sortie sur la console, mais écrit dans un fichier journal uniquement.  Elle n’écrit _pas_ dans un fichier de résultats ou `/var/log/messages`.

Le *fichier journal* est constitué du nom de commande + l’option -c + le nom du fichier config. Par défaut, le nom de fichier journal d’un argument `-c backup` s’exécute avec un nom de fichier de configuration par défaut : `azacsnap-backup-azacsnap.log`.

## <a name="example-with-other-parameter-to-backup-host-os"></a>Exemple avec le paramètre `other` (pour le système d’exploitation hôte de sauvegarde)

> [!NOTE]
> L’utilisation d’un autre fichier config (`--configfile bootVol.json`) qui contient uniquement les volumes de démarrage.

```bash
azacsnap -c backup --volume other --prefix boot_TEST --retention 9 --configfile bootVol.json
```

La commande ne génère pas de sortie sur la console, mais écrit dans un fichier journal uniquement.  Elle n’écrit _pas_ dans un fichier de résultats ou `/var/log/messages`.

Le nom du *fichier journal* dans cet exemple est `azacsnap-backup-bootVol.log`.

> [!NOTE]
> Le nom du fichier journal est constitué de « (nom de commande)-(option `-c`)-(nom du fichier config) ».  Par exemple, si vous utilisez l’option `-c backup` avec le nom de fichier journal `h80.json`, le fichier journal est appelé `azacsnap-backup-h80.log`.  Ou si vous utilisez l’option `-c test` avec le même fichier config, le fichier journal est appelé `azacsnap-test-h80.log`.

- Type de grande instance HANA : Il existe deux valeurs valides avec `TYPEI` ou `TYPEII` en fonction de l’unité de grande instance HANA.
- Consultez [les références SKU disponibles pour les grandes instances HANA](../virtual-machines/workloads/sap/hana-available-skus.md) pour confirmer les références disponibles.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir les détails de l'instantané](azacsnap-cmd-ref-details.md)
- [Suppression d'instantanés](azacsnap-cmd-ref-delete.md)