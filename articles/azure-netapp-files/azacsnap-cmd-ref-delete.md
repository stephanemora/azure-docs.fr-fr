---
title: Supprimer avec l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Guide d’exécution de la commande delete de l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.
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
ms.openlocfilehash: 1f2c767d45bb08e25a057c7db1f380ceb250f607
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864905"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Supprimer avec l’outil Azure Application Consistent Snapshot Tool (préversion)

Cet article sert de guide pour exécuter la commande delete de l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.

## <a name="introduction"></a>Introduction

Il est possible de supprimer des instantanés de volume et des entrées de catalogue de base de données avec la commande `azacsnap -c delete`.

> [!IMPORTANT]
> Les instantanés créés moins de 10 minutes avant l’exécution de cette commande ne doivent pas être supprimés en raison du risque d’interférence avec la réplication d’instantané.

## <a name="command-options"></a>Options de commande

La commande `-c delete` comporte les options suivantes :

- `--delete hana` utilisée avec les options `--dbsid <SID>` et `--hanabackupid <HANA backup id>` supprime les entrées du catalogue de sauvegarde SAP HANA correspondant aux critères.

- `--delete storage` utilisé avec l’option `--snapshot <snapshot name>` supprime l’instantané du système de stockage back-end.

- `--delete sync` utilisée avec les options `--dbsid <SID>` et `--hanabackupid <HANA backup id>` obtient le nom de l’instantané de stockage à partir du catalogue de sauvegarde pour le `<HANA backup id>`, puis supprime l’entrée dans le catalogue de sauvegarde _et_ supprime l’instantané dans tous les volumes contenant l’instantané spécifié.

- `--delete sync` utilisée avec `--snapshot <snapshot name>` recherche les entrées dans le catalogue de sauvegarde pour le `<snapshot name>`, obtient l’ID de sauvegarde SAP HANA, puis supprime l’entrée dans le catalogue de sauvegarde _et_ supprime l’instantané dans tous les volumes contenant l’instantané spécifié.

- `[--force]` (facultatif) *À utiliser avec précaution*.  Cette opération force la suppression sans demander confirmation.

- `[--configfile <config filename>]` est un paramètre facultatif qui autorise les noms de fichiers config personnalisés.

### <a name="delete-a-snapshot-using-sync-option"></a>Supprimer un instantané à l’aide de l’option `sync`

```bash
azacsnap -c delete --delete sync --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Recherche les entrées dans le catalogue de sauvegarde pour l’ID de sauvegarde SAP HANA 157979797979, obtient le nom de l’instantané de stockage, puis supprime l’entrée dans le catalogue de sauvegarde et supprime l’instantané dans tous les volumes contenant l’instantané spécifié.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Recherche les entrées dans le catalogue de sauvegarde pour l’instantané nommé hana_hourly.2020-01-22_2358, obtient l’ID de sauvegarde SAP HANA, puis supprime l’entrée dans le catalogue de sauvegarde et supprime l’instantané dans tous les volumes contenant l’instantané spécifié.

### <a name="delete-a-snapshot-using-hana-option"></a>Supprimer un instantané à l’aide de l’option `hana`

```bash
azacsnap -c delete --delete hana --dbsid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Supprime l’ID de sauvegarde SAP HANA 157979797979 du catalogue de sauvegarde pour le SID H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Supprimer un instantané à l’aide de l’option `storage`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Supprime l’instantané de tous les volumes contenant l’instantané nommé hana_hourly.2020-01-22_2358.

**Sortie à l’aide de l’option `--delete storage`**

L’utilisateur est invité à confirmer la suppression.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

Il est possible d’éviter la confirmation de l’utilisateur, en utilisant le paramètre facultatif `--force` comme indiqué ci-dessous :

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir les détails de l’instantané](azacsnap-cmd-ref-details.md)
- [Effectuer une sauvegarde](azacsnap-cmd-ref-backup.md)
