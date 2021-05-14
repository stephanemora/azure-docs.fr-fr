---
title: Rechercher des erreurs et reprendre des travaux avec des journaux dans AzCopy (stockage Azure) | Microsoft Docs
description: Découvrez l’utilisation des journaux pour diagnostiquer les erreurs et pour reprendre des travaux suspendus à l’aide de fichiers de plan.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d3b956803e9a796c49288f90873e88c3b69f1c7b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502893"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Rechercher des erreurs et reprendre des travaux à l’aide des fichiers journaux et de plan dans AzCopy

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article vous aide à utiliser les journaux pour diagnostiquer les erreurs, puis à utiliser des fichiers de plan pour reprendre des travaux. Cet article explique également comment configurer les fichiers journaux et de plan en modifiant leur niveau de détail et l’emplacement par défaut où ils sont stockés.

> [!NOTE]
> Si vous recherchez du contenu pour vous aider à bien démarrer avec AzCopy, consultez [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md).

## <a name="log-and-plan-files"></a>Fichiers journaux et de plan

AzCopy crée des fichiers *journaux* et *de plan* pour chaque travail. Vous pouvez utiliser ces journaux pour investiguer et résoudre les problèmes potentiels. 

Les journaux d’activité contiennent l’état de la défaillance (`UPLOADFAILED`, `COPYFAILED`et `DOWNLOADFAILED`), le chemin complet et la raison de la défaillance.

Par défaut, les fichiers journaux et de plan se trouvent dans le répertoire `%USERPROFILE%\.azcopy` sur Windows ou dans le répertoire `$HOME$\.azcopy` sur Mac et Linux, mais vous pouvez changer cet emplacement. 

L’erreur correspondante n’est pas nécessairement la première erreur qui apparaît dans le fichier. Pour les erreurs telles que les erreurs réseau, les délais d’expiration et les erreurs de serveur occupé, AzCopy effectue jusqu’à 20 nouvelles tentatives. En général, le processus aboutit.  La première erreur que vous voyez peut être un problème anodin qui a fait l’objet d’une nouvelle tentative réussie.  Ainsi, au lieu de vous concentrer sur la première erreur du fichier, recherchez les erreurs à proximité de `UPLOADFAILED`, `COPYFAILED` ou `DOWNLOADFAILED`. 

> [!IMPORTANT]
> Lorsque vous soumettez requête au support Microsoft (ou que vous résolvez le problème impliquant un tiers), partagez la version rédigée de la commande que vous souhaitez exécuter. Cela garantit que la SAP n’est pas accidentellement partagée avec tout le monde. Vous trouverez la version expurgée au début du fichier journal.

## <a name="review-the-logs-for-errors"></a>Passer en revue les journaux d’activité pour détecter la présence d’erreurs

La commande suivante obtient toutes les erreurs avec l’état `UPLOADFAILED` à partir du journal `04dc9ca9-158f-7945-5933-564021086c79` :

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Afficher et reprendre des travaux

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

> [!TIP]
> Placez les arguments de chemin d’accès tels que le jeton SAS avec des guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

Lorsque vous reprenez un travail, AzCopy examine le fichier de plan de travail. Le fichier de plan répertorie tous les fichiers identifiés pour le traitement lors de la création du travail. Lorsque vous reprenez un travail, AzCopy essaiera de transférer tous les fichiers répertoriés dans le fichier de plan et qui n’ont pas été déjà transférés.

## <a name="change-the-location-of-plan-files"></a>Changer l’emplacement des fichiers de plan

Utilisez l’une de ces commandes.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | PowerShell :`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Dans une invite de commandes, tapez : `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Utilisez `azcopy env` pour vérifier la valeur actuelle de cette variable. Si la valeur est vide, les fichiers de plan sont écrits à l’emplacement par défaut.

## <a name="change-the-location-of-log-files"></a>Changer l’emplacement des fichiers journaux

Utilisez l’une de ces commandes.

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | PowerShell :`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Dans une invite de commandes, tapez : `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Utilisez `azcopy env` pour vérifier la valeur actuelle de cette variable. Si la valeur est vide, les journaux sont écrits à l’emplacement par défaut.

## <a name="change-the-default-log-level"></a>Modifier le niveau de consignation par défaut

Par défaut, le niveau de consignation d’AzCopy est défini sur `INFO`. Si vous souhaitez réduire la verbosité du journal afin d’économiser de l’espace disque, écrasez ce paramètre en utilisant l’option ``--log-level``. 

Les niveaux d’enregistrement disponibles sont : `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` et `FATAL`.

## <a name="remove-plan-and-log-files"></a>Supprimer les fichiers de plan et les fichiers journaux

Si vous souhaitez supprimer tous les fichiers journaux et de plan de votre ordinateur local pour économiser de l’espace disque, utilisez la commande `azcopy jobs clean`.

Pour supprimer les fichiers journaux et de plan associés à un seul travail, utilisez `azcopy jobs rm <job-id>`. Remplacez l’espace réservé `<job-id>` dans cet exemple par l’ID du travail.

## <a name="see-also"></a>Voir aussi

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)
