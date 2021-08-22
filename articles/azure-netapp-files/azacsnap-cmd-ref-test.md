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
ms.date: 08/04/2021
ms.author: phjensen
ms.openlocfilehash: eb41e1ebda2e5a14bc2987dded8948221ee93452
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524308"
---
# <a name="test-azure-application-consistent-snapshot-tool"></a>Tester l’outil Azure Application Consistent Snapshot Tool

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

## <a name="next-steps"></a>Étapes suivantes

- [Sauvegarde instantanée avec l’outil Azure Application Consistent Snapshot](azacsnap-cmd-ref-backup.md)
