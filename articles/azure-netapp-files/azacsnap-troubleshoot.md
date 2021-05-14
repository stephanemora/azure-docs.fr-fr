---
title: Dépanner l’outil Azure Application Consistent Snapshot pour Azure NetApp Files | Microsoft Docs
description: Fournit du contenu pour la résolution des problèmes rencontrés lors de l’utilisation de l’outil Azure Application Consistent Snapshot avec Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 66272e59dd67375b1da119648cf461bd0c2baa9b
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929594"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool"></a>Dépanner l'outil Azure Application Consistent Snapshot

Cet article fournit du contenu pour la résolution des problèmes rencontrés lors de l’utilisation de l’outil Azure Application Consistent Snapshot avec Azure NetApp Files.

Voici les problèmes courants que vous pouvez rencontrer lors de l’exécution des commandes. Suivez les instructions de résolution indiquées pour résoudre le problème. Si vous rencontrez toujours un problème, ouvrez une demande de service à partir du portail Azure et attribuez la demande à la file d’attente Grande instance SAP HANA pour que le Support Microsoft y réponde.

## <a name="log-files"></a>Fichiers journaux

Les fichiers journaux constituent l’une des meilleures sources d’informations pour le débogage des erreurs avec AzAcSnap.  

### <a name="log-file-location"></a>Emplacement du fichier journal

Les fichiers journaux sont stockés dans le répertoire configuré selon le paramètre `logPath` dans le fichier de configuration AzAcSnap.  Le nom de fichier de configuration par défaut est `azacsnap.json` et la valeur par défaut pour `logPath` est `"./logs"`. Cela signifie que les fichiers journaux sont écrits dans le répertoire `./logs` associé à l’emplacement où la commande `azacsnap` est exécutée.  Lorsque le `logPath` est un emplacement absolu (par exemple `/home/azacsnap/logs`), `azacsnap` renvoie les journaux dans `/home/azacsnap/logs`, quel que soit l’emplacement d’exécution de la commande `azacsnap`.

### <a name="log-file-naming"></a>Nom du fichier journal

Le nom du fichier journal est basé sur le nom de l’application (par exemple `azacsnap`), l’option de commande (`-c`) utilisée (par exemple `backup`, `test`, `details`, etc.) et le nom de fichier de configuration (par exemple, par défaut = `azacsnap.json`).  Par conséquent, si vous utilisez la commande `-c backup`, le nom du fichier journal par défaut est `azacsnap-backup-azacsnap.log` et le fichier est écrit dans le répertoire configuré par `logPath`.  

Cette convention d’affectation de noms a été établie pour autoriser plusieurs fichiers de configuration (un par base de données) et pour faciliter la localisation des fichiers journaux associés.  Par conséquent, si le nom de fichier de configuration est `SID.json`, le nom de fichier de résultat lors de l’utilisation des options `azacsnap -c backup --configfile SID.json` est `azacsnap-backup-SID.log`.

### <a name="result-file-and-syslog"></a>Fichier de résultat et syslog

Pour l’option de commande `-c backup`, AzAcSnap écrit dans un fichier `*.result` et dans le journal système (`/var/log/messages`) à l’aide de la commande `logger`.  Le nom de fichier `*.result` a le même nom de base que le [fichier journal](#log-file-naming) et il est placé dans le [même emplacement que le fichier journal ](#log-file-location).  Il s’agit d’un fichier de sortie d’une seule ligne, comme dans les exemples suivants.

Exemple de sortie à partir d’un fichier `*.result`.
```output
Database # 1 (PR1) : completed ok
```

Exemple de sortie à partir d’un fichier `/var/log/messages`.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Échec de la communication avec Azure NetApp Files

Lors de la validation de la communication avec Azure NetApp Files, la communication peut échouer ou expirer.  Vérifiez que les règles de pare-feu ne bloquent pas le trafic sortant du système exécutant AzAcSnap vers les adresses et les ports TCP/IP suivants :-

- (https://)management.azure.com:443
- (https://)login.microsoftonline.com:443 

## <a name="failed-communication-with-sap-hana"></a>Échec de la communication avec SAP HANA

Lors de la validation de la communication avec SAP HANA en exécutant un test avec `azacsnap -c test --test hana`, l’erreur suivante s’affiche :

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Solution :**

1. Vérifiez le fichier de configuration (par exemple `azacsnap.json`) de chaque instance HANA pour vous assurer que les valeurs de la base de données SAP HANA sont correctes.
1. Essayez d’exécuter la commande ci-dessous pour vérifier si la commande `hdbsql` se trouve dans le chemin d’accès et qu’elle peut se connecter au serveur SAP HANA. L’exemple suivant montre l’exécution correcte de la commande et sa sortie.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    Dans cet exemple, la commande `hdbsql` ne se trouve pas dans le `$PATH` de l’utilisateur.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    Dans cet exemple, la commande `hdbsql` est ajoutée temporairement au `$PATH` de l’utilisateur, mais quand elle est exécutée, le résultat montré est que la clé de connexion n’a pas été correctement configurée avec la commande `hdbuserstore Set` (pour plus d’informations, consulter le guide de prise en main) :

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Pour ajouter définitivement au `$PATH` de l’utilisateur, mettez à jour le fichier `$HOME/.profile` de l’utilisateur.

## <a name="the-hdbuserstore-location"></a>Emplacement `hdbuserstore`

Lors de la configuration de la communication avec SAP HANA, le programme `hdbuserstore` est utilisé pour créer les paramètres d’une communication sécurisée.  Le programme `hdbuserstore` se trouve généralement sous `/usr/sap/<SID>/SYS/exe/hdb/` ou `/usr/sap/hdbclient`.  Normalement, le programme d’installation ajoute l’emplacement correct au `$PATH` de l’utilisateur `azacsnap`.

## <a name="failed-test-with-storage"></a>Échec du test avec le stockage

La commande `azacsnap -c test --test storage` ne se termine pas correctement.

### <a name="azure-large-instance"></a>Instance volumineuse Azure

L’exemple suivant consiste à exécuter `azacsnap` sur SAP HANA sur une grande instance Azure :

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Solution :** L’erreur ci-dessus s’affiche généralement lorsque l’utilisateur du stockage Grande instance Azure n’a pas accès au stockage sous-jacent. Pour valider l’accès au stockage avec l’utilisateur de stockage fourni, exécutez la commande `ssh` pour valider la communication avec la plateforme de stockage.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Exemple de sortie attendue :

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Impossible d’établir l’authenticité de l’hôte « 172.18.18.11 (172.18.18.11) »

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Solution :** Ne sélectionnez pas Oui. Vérifiez que votre adresse IP de stockage est correcte. Si le problème persiste, confirmez l’adresse IP de stockage auprès de l’équipe des opérations Microsoft.

### <a name="azure-netapp-files"></a>Azure NetApp Files

L’exemple suivant consiste à exécuter `azacsnap` sur une machine virtuelle à l’aide d’Azure NetApp Files :

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Solution :**

1. Vérifiez l’existence du fichier de principal de service `azureauth.json` tel que défini dans le fichier de configuration `azacsnap.json`.
1. Vérifiez le fichier journal (par exemple `logs/azacsnap-test-azacsnap.log`) pour voir si le principal de service (`azureauth.json`) a le contenu approprié.  Exemple du journal comme suit :

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Vérifiez le fichier journal (par exemple `logs/azacsnap-test-azacsnap.log`) pour voir si le principal de service (`azureauth.json`) a expiré. Exemple du journal comme suit :

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Étapes suivantes

- [Conseils](azacsnap-tips.md)
