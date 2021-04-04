---
title: Tester l’outil Azure Application Consistent Snapshot pour Azure NetApp Files | Microsoft Docs
description: Explique comment exécuter la commande de test de l’outil Azure Application Consistent Snapshot que vous pouvez utiliser avec Azure NetApp Files.
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
ms.openlocfilehash: 03ffa93a71ded40e033f2068ea23fc6b994a5bbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632093"
---
# <a name="test-azure-application-consistent-snapshot-tool-preview"></a>Tester l’outil Azure Application Consistent Snapshot (préversion)

Cet article explique comment exécuter la commande de test de l’outil Azure Application Consistent Snapshot que vous pouvez utiliser avec Azure NetApp Files.

## <a name="introduction"></a>Introduction

Un test de la configuration s’effectue à l’aide de la commande `azacsnap -c test`.

## <a name="command-options"></a>Options de la commande

La commande `-c test` offre les options suivantes :

- `--test hana` teste la connexion à l’instance SAP HANA.

- `--test storage` teste la communication avec l’interface de stockage sous-jacente en créant un instantané de stockage temporaire sur tous les volumes `data` configurés, puis en les supprimant. 

- `--test all` effectue les tests `hana` et `storage` dans cet ordre.

- `[--configfile <config filename>]` est un paramètre facultatif qui autorise les noms de fichiers config personnalisés.

## <a name="check-connectivity-with-sap-hana-azacsnap--c-test---test-hana"></a>Vérifier la connectivité avec SAP HANA `azacsnap -c test --test hana`

Cette commande vérifie la connectivité HANA pour toutes les instances de HANA dans le fichier de configuration. Elle utilise HDBuserstore pour se connecter à la SYSTEMDB et récupère les informations SID.

Pour le protocole SSL, cette commande peut prendre l’argument facultatif suivant :

- `--ssl=` force une connexion chiffrée avec la base de données et définit la méthode de chiffrement utilisée pour communiquer avec SAP HANA, `openssl` ou `commoncrypto`. Si la méthode est définie, la commande s’attend à trouver deux fichiers dans le même répertoire, ces fichiers devant être nommés d’après le SID correspondant. Consultez [Utiliser SSL pour la communication avec SAP HANA](azacsnap-installation.md#using-ssl-for-communication-with-sap-hana).

### <a name="output-of-the-azacsnap--c-test---test-hana-command"></a>Sortie de la commande `azacsnap -c test --test hana`

```bash
azacsnap -c test --test hana
```

```output
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
PASSED: Successful connectivity to HANA version 2.00.032.00.1533114046
END   : Test process complete for 'hana'
```

## <a name="check-connectivity-with-storage-azacsnap--c-test---test-storage"></a>Vérifier la connectivité avec le stockage `azacsnap -c test --test storage`

La commande `azacsnap` prend une capture instantanée pour tous les volumes de données configurés, afin de vérifier la disponibilité de l’accès approprié aux volumes pour chaque instance SAP HANA. Une capture instantanée temporaire est créée, puis supprimée pour chaque volume de données afin de vérifier l’accès à la capture instantanée pour chaque système de fichiers.

### <a name="output-of-the-azacsnap--c-test---test-storage-command"></a>Sortie de la commande `azacsnap -c test --test storage`

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 2 task(s) to Test Snapshots for Storage Volume Type 'data'
PASSED: Task#2/2 Storage test successful for Volume
PASSED: Task#1/2 Storage test successful for Volume
END   : Storage tests complete
END   : Test process complete for 'storage'
```

> [!NOTE]
> Pour une instance Azure de grande taille, la commande `azacsnap -c test --test storage` extrapole la génération du stockage et la référence (SKU) de HLI.  Sur la base de ces informations, elle fournit des conseils sur la configuration des captures instantanées de démarrage (voir la ligne commençant par la sortie `Action:`).

```output
SID1   : Generation 4
Storage: ams07-a700s-saphan-1-01v250-client25-nprod
HLI SKU: S96
Action : Configure the 'boot' snapshots on ALL the servers.
```

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarde instantanée avec l’outil Azure Application Consistent Snapshot](azacsnap-cmd-ref-backup.md)
