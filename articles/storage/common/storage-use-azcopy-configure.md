---
title: Configurer, optimiser et résoudre les problèmes de AzCopy avec le Stockage Azure | Microsoft Docs
description: Configurer, optimiser et dépanner AzCopy
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 1a67846889b43d582a7a7d477a33f0e2168fd760
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147871"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurer, optimiser et dépanner AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article vous aide à effectuer les tâches de configuration avancées et vous aide à résoudre les problèmes pouvant survenir lorsque vous utilisez AzCopy.

> [!NOTE]
> Si vous recherchez du contenu pour vous aider à bien démarrer avec AzCopy, consultez les articles suivants :
> - [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
> - [Transférer des données avec AzCopy et le Stockage Blob](storage-use-azcopy-blobs.md)
> - [Transférer des données avec AzCopy et le stockage de fichiers](storage-use-azcopy-files.md)
> - [Transférer des données avec AzCopy et des compartiments Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Configuration des paramètres de proxy

Pour configurer les paramètres de proxy pour AzCopy, définissez la variable d’environnement `https_proxy`.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | Dans une invite de commandes, tapez : `set https_proxy=<proxy IP>:<proxy port>`<br> Pour PowerShell, tapez : `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Actuellement, AzCopy ne prend en charge les serveurs proxy qui requièrent une authentification avec NTLM ou Kerberos.

## <a name="optimize-throughput"></a>Optimiser le débit

Définissez la variable d’environnement `AZCOPY_CONCURRENCY_VALUE` pour configurer le nombre de demandes simultanées et contrôler les performances du débit et la consommation des ressources. Si votre ordinateur dispose de moins de 5 unités centrales, la valeur de cette variable est définie sur `32`. Sinon, la valeur par défaut est égale à 16 multiplié par le nombre d’unités centrales. La valeur maximale par défaut de cette variable est `300`, mais vous pouvez l’augmenter ou la diminuer manuellement.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilisez `azcopy env` pour vérifier la valeur actuelle de cette variable.  Si la valeur est vide, alors la variable `AZCOPY_CONCURRENCY_VALUE` est définie sur la valeur par défaut de `300`.

## <a name="change-the-location-of-the-log-files"></a>Changer l'emplacement des fichiers journaux

Par défaut, les fichiers journaux sont situés dans le répertoire `%USERPROFILE\\.azcopy` sur Windows ou dans le répertoire `$HOME\\.azcopy` sur Mac et Linux. Si nécessaire, vous pouvez modifier cet emplacement à l’aide de ces commandes.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilisez `azcopy env` pour vérifier la valeur actuelle de cette variable. Si la valeur est vide, les journaux sont écrits à l’emplacement par défaut.

## <a name="change-the-default-log-level"></a>Modifier le niveau de consignation par défaut

Par défaut, le niveau de consignation d’AzCopy est défini sur `INFO`. Si vous souhaitez réduire la verbosité du journal afin d’économiser de l’espace disque, écrasez ce paramètre en utilisant l’option ``--log-level``. 

Les niveaux de consignation disponibles sont : `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, et `FATAL`.

## <a name="troubleshoot-issues"></a>Problèmes de dépannage

AzCopy crée des fichiers journaux et de plan pour chaque travail. Vous pouvez utiliser les journaux d’activité pour examiner et résoudre les problèmes potentiels. 

Les journaux d’activité contiennent l’état de la défaillance (`UPLOADFAILED`, `COPYFAILED`et `DOWNLOADFAILED`), le chemin complet et la raison de la défaillance.

Par défaut, les fichiers journaux et de plan sont situés dans le répertoire `%USERPROFILE\\.azcopy` sur Windows ou dans le répertoire `$HOME\\.azcopy` sur Mac et Linux.

> [!IMPORTANT]
> Lorsque vous soumettez requête au support Microsoft (ou que vous résolvez le problème impliquant un tiers), partagez la version rédigée de la commande que vous souhaitez exécuter. Cela garantit que la SAP n’est pas accidentellement partagée avec tout le monde. Vous trouverez la version expurgée au début du fichier journal.

### <a name="review-the-logs-for-errors"></a>Passer en revue les journaux d’activité pour détecter la présence d’erreurs

La commande suivante obtient toutes les erreurs avec l’état `UPLOADFAILED` à partir du journal `04dc9ca9-158f-7945-5933-564021086c79` :

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

Utilisez la commande suivante pour reprendre un travail annulé/ayant échoué. Cette commande utilise son identificateur ainsi que le jeton SAP, car il n’est pas persistant pour des raisons de sécurité :

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Lorsque vous reprenez un travail, AzCopy examine le fichier de plan de travail. Le fichier de plan répertorie tous les fichiers identifiés pour le traitement lors de la création du travail. Lorsque vous reprenez un travail, AzCopy essaiera de transférer tous les fichiers répertoriés dans le fichier de plan et qui n’ont pas été déjà transférés.