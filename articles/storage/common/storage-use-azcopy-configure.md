---
title: Configurer, optimiser et résoudre les problèmes de AzCopy avec le stockage Azure | Microsoft Docs
description: Configurer, optimiser et résoudre les problèmes d’AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a160591ef0a47eed097ce8db373878f32965de9b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247123"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurer, optimiser et résoudre les problèmes d’AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des objets BLOB ou des fichiers vers ou à partir d’un compte de stockage. Cet article vous aide à effectuer les tâches de configuration avancées et vous aide à résoudre les problèmes qui peuvent survenir lorsque vous utilisez AzCopy.

> [!NOTE]
> Si vous avez besoin pour le contenu pour vous aider à bien démarrer avec AzCopy, consultez les articles suivants :
> - [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
> - [Transfert de données avec AzCopy et le stockage blob](storage-use-azcopy-blobs.md)
> - [Transfert de données avec AzCopy et stockage fichier](storage-use-azcopy-files.md)
> - [Transfert de données avec AzCopy et Amazon S3 compartiments](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configuration des paramètres de proxy

Pour configurer les paramètres de proxy pour AzCopy, définissez le `https_proxy` variable d’environnement.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Actuellement, AzCopy ne prend en charge les serveurs proxy qui requièrent une authentification avec NTLM ou Kerberos.

## <a name="optimize-throughput"></a>Optimiser le débit

Définir le `AZCOPY_CONCURRENCY_VALUE` variable d’environnement pour configurer le nombre de demandes simultanées et pour contrôler la consommation de ressources et des performances de débit. Si votre ordinateur dispose de moins de 5 unités centrales, la valeur de cette variable est définie sur `32`. Sinon, la valeur par défaut est égale à 16 multiplié par le nombre d’unités centrales. La valeur maximale par défaut de cette variable est `300`, mais vous pouvez définir manuellement cette valeur supérieure ou inférieure.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilisez le `azcopy env` pour vérifier la valeur actuelle de cette variable.  Si la valeur est vide, puis le `AZCOPY_CONCURRENCY_VALUE` variable est définie sur la valeur par défaut `300`.

## <a name="change-the-location-of-the-log-files"></a>Changer l'emplacement des fichiers journaux

Par défaut, les fichiers journaux sont situés dans le `%USERPROFILE\\.azcopy` sur Windows, ou dans le `$HOME\\.azcopy` dossier sur Mac et Linux. Vous pouvez modifier cet emplacement si vous avez besoin à l’aide de ces commandes.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilisez le `azcopy env` pour vérifier la valeur actuelle de cette variable. Si la valeur est vide, les journaux sont écrits à l’emplacement par défaut.

## <a name="change-the-default-log-level"></a>Modifier le niveau de consignation par défaut

Par défaut, le niveau de journal de AzCopy est défini sur `INFO`. Si vous souhaitez réduire le niveau de détail du journal pour économiser l’espace disque, remplacer ce paramètre à l’aide de la ``--log-level`` option. 

Niveaux de consignation disponibles sont : `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, et `FATAL`.

## <a name="troubleshoot-issues"></a>Problèmes de dépannage

AzCopy crée des fichiers journaux et de plan pour chaque travail. Vous pouvez utiliser les journaux d’activité pour examiner et résoudre les problèmes potentiels. 

Les journaux contient l’état de défaillance (`UPLOADFAILED`, `COPYFAILED`, et `DOWNLOADFAILED`), le chemin d’accès complet et le motif de l’échec.

Par défaut, les fichiers journaux et plan se trouvent dans le `%USERPROFILE\\.azcopy` dossier sur Windows ou `$HOME\\.azcopy` dossier sur Mac et Linux.

> [!IMPORTANT]
> Lorsque vous soumettez une demande de Support Microsoft (ou résoudre le problème impliquant un tiers), partagent la version rédigée de la commande que vous souhaitez exécuter. Cela garantit que la SAP n’est pas accidentellement partagée avec tout le monde. Vous trouverez la version expurgée au début du fichier journal.

### <a name="review-the-logs-for-errors"></a>Passer en revue les journaux d’activité pour détecter la présence d’erreurs

La commande suivante obtient toutes les erreurs avec `UPLOADFAILED` état à partir de la `04dc9ca9-158f-7945-5933-564021086c79` journal :

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Afficher et reprendre des travaux

Chaque opération de transfert crée un travail AzCopy. Utilisez la commande suivante pour afficher l’historique des travaux :

```
azcopy jobs list
```

Pour afficher les statistiques du travail, utilisez la commande suivante :

```
azcopy jobs show <job-id>
```

Pour filtrer les transferts par état, utilisez la commande suivante :

```
azcopy jobs show <job-id> --with-status=Failed
```

Utilisez la commande suivante pour reprendre un travail échec/annulé. Cette commande utilise son identificateur, ainsi que le jeton SAP comme il n’est pas persistant pour des raisons de sécurité :

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Lorsque vous reprenez un travail, AzCopy examine le fichier de plan de travail. Le fichier de plan répertorie tous les fichiers qui ont été identifiées pour le traitement lors de la création du travail. Lorsque vous reprenez un travail, AzCopy essaiera de transférer tous les fichiers qui sont répertoriés dans le fichier de plan qui n’ont pas été déjà transféré.