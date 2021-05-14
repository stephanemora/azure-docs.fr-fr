---
title: Bien démarrer avec l’outil Azure Application Consistent Snapshot Tool pour Azure NetApp Files | Microsoft Docs
description: Guide d’installation de l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.
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
ms.openlocfilehash: eee7204413face46870de049b38a79061de28347
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929792"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool"></a>Bien démarrer avec l’outil Azure Application Consistent Snapshot

Cet article sert de guide pour installer l’outil Azure Application Consistent Snapshot Tool, que vous pouvez utiliser avec Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>Obtention des outils d’instantané

Il est recommandé aux clients de se procurer la version la plus récente du [programme d’installation d’AzAcSnap](https://aka.ms/azacsnapdownload) auprès de Microsoft.

Le fichier d’installation automatique est associé à un [fichier de signature du programme d’installation d’AzAcSnap](https://aka.ms/azacsnapdownloadsignature), qui est signé avec la clé publique de Microsoft pour permettre la vérification GPG du téléchargement du programme d’installation.

Une fois les téléchargements effectués, suivez les étapes de ce guide pour procéder à l’installation.

### <a name="verifying-the-download"></a>Vérification du téléchargement

Le programme d’installation, téléchargeable comme indiqué ci-dessus, est associé à un fichier de signature PGP ayant l’extension de nom de fichier `.asc`. Ce fichier permet de garantir que le programme d’installation téléchargé est un fichier vérifié fourni par Microsoft. La clé publique PGP de Microsoft utilisée pour la signature des packages Linux est disponible ici (<https://packages.microsoft.com/keys/microsoft.asc>) et a été utilisée pour signer le fichier de signature.

La clé publique PGP de Microsoft peut être importée dans l’environnement local d’un utilisateur, comme indiqué ci-dessous :

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Les commandes suivantes permettent de faire confiance à la clé publique PGP de Microsoft :

1. Listez les clés dans le magasin.
2. Modifiez la clé de Microsoft.
3. Vérifiez l’empreinte digitale avec `fpr`
4. Signez la clé pour lui faire confiance.

```bash
gpg --list-keys
```

Clés listées :
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Sortie de la session `gpg` interactive de signature de la clé publique de Microsoft :
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Vous pouvez vérifier le fichier de signature PGP du programme d’installation en procédant comme indiqué ci-dessous :

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Pour plus d’informations sur l’utilisation de GPG, consultez le [Manuel de confidentialité GNU](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Scénarios pris en charge

Vous pouvez utiliser les outils d’instantané dans les scénarios suivants.

- SID unique
- SID multiples
- HSR
- Scale-out
- MDC (un seul locataire pris en charge)
- Conteneur unique
- Système d’exploitation SUSE
- Système d’exploitation RHEL
- TYPE DE RÉFÉRENCE SKU I
- TYPE DE RÉFÉRENCE SKU II

Consultez [Scénario pris en charge pour des grandes instances HANA](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Matrice de prise en charge des instantanés de SAP

La matrice suivante est fournie à titre indicatif. Elle permet d’identifier les versions de SAP HANA prises en charge par SAP pour les sauvegardes basées sur des instantanés de stockage.

| Versions de base de données       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Base de données monoconteneur| √       | √      | -      | -      | -      | -      |
|MDC (monolocataire)        | -       | -      | √      | √      | √      | √      |
|MDC (multilocataire)     | -       | -      | -      | -      | -      | √      |
> √ = <small>pris en charge par SAP pour les instantanés de stockage</small>

## <a name="important-things-to-remember"></a>Points importants à retenir

- Une fois que vous avez installé les outils d’instantané, supervisez en permanence l’espace de stockage disponible et, si nécessaire, supprimez les anciens instantanés régulièrement pour éviter le remplissage du stockage.
- Utilisez toujours les derniers outils d’instantané.
- Utilisez partout la même version des outils d’instantané.
- Testez les outils d’instantané, puis familiarisez-vous avec les paramètres nécessaires ainsi que la sortie de la commande avant de vous en servir sur le système de production.
- Quand vous configurez l’utilisateur HANA dans le cadre de la sauvegarde (détails ci-dessous dans ce document), vous devez le faire pour chaque instance HANA. Créez un compte d’utilisateur SAP HANA afin d’accéder à l’instance HANA dans le SYSTEMDB (et non dans la base de données de SID) pour MDC. Dans l’environnement monoconteneur, vous pouvez effectuer la configuration au sein de la base de données de locataire.
- Les clients doivent fournir la clé publique SSH pour l’accès au stockage. Cette action doit être effectuée une fois par nœud et en fonction de chaque utilisateur pour lequel la commande est exécutée.
- Le nombre d’instantanés par volume est limité à 250.
- Si vous modifiez manuellement le fichier config, utilisez toujours un éditeur de texte Linux tel que « vi », et non un éditeur Windows tel que le Bloc-notes. L’utilisation d’un éditeur Windows peut endommager le format de fichier.
  - Configurez `hdbuserstore` pour permettre à l’utilisateur SAP HANA de communiquer avec SAP HANA.
- Pour la reprise d’activité DR : Les outils d’instantané doivent être testés sur le nœud DR avant la configuration de la reprise d’activité DR.
- Supervisez régulièrement l’espace disque. La suppression automatisée des journaux est gérée à l’aide de l’option `--trim` de `azacsnap -c backup` pour SAP HANA 2 et les versions ultérieures.
- **Risque d’absence d’instantanés** - Les outils d’instantané interagissent uniquement avec le nœud du système SAP HANA spécifié dans le fichier config.  Si ce nœud cesse d’être disponible, aucun mécanisme ne permet de démarrer automatiquement la communication avec un autre nœud.  
  - Pour un scénario de type **Scale-out de SAP HANA avec nœud de secours**, il est courant d’installer et de configurer les outils d’instantané sur le nœud maître. Toutefois, si le nœud maître cesse d’être disponible, le nœud de secours reprend le rôle de nœud maître. Dans ce cas, l’équipe d’implémentation doit configurer les outils d’instantané sur les deux nœuds (maître et de secours) pour éviter de perdre des instantanés. À l’état normal, le nœud maître prend des instantanés HANA lancés par crontab. Toutefois, après le basculement du nœud maître, ces instantanés doivent être exécutés à partir d’un autre nœud, par exemple le nouveau nœud maître (ancien nœud de secours). Pour ce faire, le nœud de secours nécessite plusieurs prérequis : l’outil d’instantané doit être installé, la communication avec le stockage doit être activée, hdbuserstore doit être configuré, `azacsnap.json` doit être configuré, et les commandes crontab doivent être préparées en prévision du basculement.
  - Pour un scénario de type **Haute disponibilité de la réplication HSR SAP HANA**, il est recommandé d’installer, de configurer et de planifier les outils d’instantané sur les deux nœuds (principal et secondaire). Si le nœud principal cesse d’être disponible, le nœud secondaire prend le relais, et les instantanés sont exécutés sur le nœud secondaire. À l’état normal, le nœud principal prend des instantanés HANA lancés par crontab. Le nœud secondaire tente de prendre des instantanés mais sans y parvenir, car le nœud principal fonctionne correctement.  Toutefois, après le basculement du nœud principal, ces instantanés sont exécutés à partir du nœud secondaire. Pour ce faire, le nœud secondaire nécessite plusieurs prérequis : l’outil d’instantané doit être installé, la communication avec le stockage doit être activée, `hdbuserstore` doit être configuré, azacsnap.json doit être configuré, et crontab doit être activé en prévision du basculement.

## <a name="guidance-provided-in-this-document"></a>Conseils d’aide fournis dans ce document

Les conseils d’aide suivants sont fournis pour illustrer l’utilisation des outils d’instantané.

### <a name="taking-snapshot-backups"></a>Exécution de sauvegardes basées sur des instantanés

- [Quels sont les prérequis d’un instantané de stockage](azacsnap-installation.md#prerequisites-for-installation)
  - [Activer la communication avec le stockage](azacsnap-installation.md#enable-communication-with-storage)
  - [Activer la communication avec SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Comment prendre des instantanés manuellement](azacsnap-tips.md#take-snapshots-manually)
- [Comment configurer l’automatisation des sauvegardes basées sur des instantanés](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Comment superviser les instantanés](azacsnap-tips.md#monitor-the-snapshots)
- [Comment supprimer un instantané ?](azacsnap-tips.md#delete-a-snapshot)
- [Comment restaurer un instantané](azacsnap-tips.md#restore-a-snapshot)
- [Comment restaurer un instantané de `boot`](azacsnap-tips.md#restore-a-boot-snapshot)
- [Quels sont les éléments clés à connaître sur les instantanés](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Les instantanés sont testés à la fois pour un seul SID et plusieurs SID.

### <a name="performing-disaster-recovery"></a>Exécution d’une reprise d’activité

- [Quels sont les prérequis de la configuration d’une reprise d’activité DR](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Comment configurer une reprise d’activité](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Comment superviser la réplication des données du site principal au site DR](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Comment effectuer un basculement vers un site DR ?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Étapes suivantes

- [Installer l’outil Azure Application Consistent Snapshot Tool](azacsnap-installation.md)
