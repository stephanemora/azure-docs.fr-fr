---
title: Configurer l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Fournit un guide pour l’exécution de la commande de configuration de l’outil Azure Application Consistent Snapshot Tool que vous pouvez utiliser avec Azure NetApp Files.
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
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632118"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Configurer l’outil Azure Application Consistent Snapshot Tool (préversion)

Cet article fournit un guide pour l’exécution de la commande de configuration de l’outil Azure Application Consistent Snapshot Tool que vous pouvez utiliser avec Azure NetApp Files.

## <a name="introduction"></a>Introduction

Le fichier config peut être créé ou modifié à l’aide de la commande `azacsnap -c configure`.

## <a name="command-options"></a>Options de commande

La commande `-c configure` possède les options suivantes

- `--configuration new` pour créer un nouveau fichier config.

- `--configuration edit` pour modifier un fichier config existant.

- `[--configfile <config filename>]` est un paramètre facultatif qui autorise les noms de fichiers config personnalisés.

## <a name="configuration-file-for-snapshot-tools"></a>Fichier config pour les outils d’instantané

Vous pouvez créer un fichier config en exécutant `azacsnap -c configure --configuration new`.  Par défaut, le nom du fichier config est `azacsnap.json`.  Vous pouvez utiliser un nom de fichier personnalisé avec le paramètre `--configfile=` (par exemple, `--configfile=<customname>.json`). L’exemple suivant concerne la configuration d’Azure Large Instance :

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Détails des valeurs requises

Les sections suivantes fournissent des instructions détaillées sur les différentes valeurs requises pour le fichier config.

### <a name="sap-hana-values"></a>Valeurs SAP HANA

Lorsque vous ajoutez une *base de données* à la configuration, les valeurs suivantes sont requises :

- **HANA Server's Address** = nom d’hôte ou adresse IP du serveur SAP HANA.
- **HANA SID** = ID système SAP HANA.
- **HANA Instance Number** = numéro d’instance SAP HANA.
- **HANA HDB User Store Key** = utilisateur SAP HANA configuré avec les autorisations nécessaires pour exécuter des sauvegardes de base de données.

- Nœud unique : Adresse IP et nom d’hôte du nœud
- HSR avec STONITH : Adresse IP et nom d’hôte du nœud
- Scale-out (N + N, N + M) : Adresse IP et nom d’hôte du nœud principal actuel
- HSR sans STONITH : Adresse IP et nom d’hôte du nœud
- Plusieurs SID sur un seul nœud : Nom d’hôte et adresse IP du nœud qui héberge ces SID

### <a name="azure-large-instance-hli-storage-values"></a>Valeurs de stockage Azure Large Instance (HLI)

Lorsque vous ajoutez un *stockage HLI* à une section de base de données, les valeurs suivantes sont requises :

- **Storage User Name** = cette valeur correspond au nom d’utilisateur utilisé pour établir la connexion SSH au stockage.
- **Storage IP Address** = adresse du système de stockage.
- **Storage Volume Name** = nom du volume pour l’instantané.  Cette valeur peut être déterminée de plusieurs façons, la plus simple étant peut-être d’essayer la commande d’interpréteur de commandes suivante :

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Valeurs de stockage Azure NetApp Files (ANF)

Lorsque vous ajoutez un *stockage ANF* à une section de base de données, les valeurs suivantes sont requises :

- **Service Principal Authentication filename** = il s’agit du fichier `authfile.json` généré dans le Cloud Shell lors de la configuration de la communication avec le stockage Azure NetApp Files.
- **Full ANF Storage Volume Resource ID** = ID de ressource complet du volume pour l’instantané.  Cette valeur peut être récupérée à partir de : Portail Azure –> ANF –> Volume –> Paramètres/Propriétés –> ID de ressource

## <a name="configuration-file-overview-azacsnapjson"></a>Aperçu du fichier config (`azacsnap.json`)

Dans l'exemple suivant, `azacsnap.json` est configuré avec un SID.

Les valeurs des paramètres doivent être définies sur l’environnement SAP HANA spécifique du client.
Pour un système **Azure Large Instance**, ces informations sont fournies par la gestion des services Microsoft pendant l’appel d’intégration/transfert, et sont mises à disposition dans un fichier Excel fourni pendant le transfert. Ouvrez une demande de service si vous avez besoin de fournir à nouveau ces informations.

Ce qui suit est un exemple uniquement, mettez à jour toutes les valeurs en conséquence.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> Dans le cas d’un scénario de récupération d’urgence où les sauvegardes doivent être exécutées sur le site de récupération d’urgence, le nom du serveur HANA configuré dans le fichier config de la récupération d’urgence (par exemple, `DR.json`) sur le site de récupération d’urgence doit être le même que le nom du serveur de production.

> [!NOTE]
> Pour Azure Large Instance, votre adresse IP de stockage doit se trouver dans le même sous-réseau que votre pool de serveurs. Par exemple, dans le cas présent, notre sous-réseau de pools de serveurs est 172. 18. 18 .0/24 et l’adresse IP de stockage qui lui est assignée est 172.18.18.11.

## <a name="next-steps"></a>Étapes suivantes

- [Tester l’outil Azure Application Consistent Snapshot Tool](azacsnap-cmd-ref-test.md)
