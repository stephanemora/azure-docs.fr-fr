---
title: LogDownloader - Service Décision personnalisée
titlesuffix: Azure Cognitive Services
description: Téléchargez les fichiers journaux produits par le service Décision personnalisée Azure.
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829988"
---
# <a name="logdownloader"></a>LogDownloader

Téléchargez les fichiers journaux produits par le service Décision personnalisée Azure et générez les fichiers  *.gz* utilisés par l’expérimentation.

## <a name="prerequisites"></a>Conditions préalables

- Python 3 : installé et sur votre chemin d’accès. Nous vous recommandons la version 64 bits pour gérer les fichiers volumineux.
- Le référentiel *mwt/Microsoft-ds* : [Clonez le référentiel](https://github.com/Microsoft/mwt-ds).
- Package *azure-storage-blob* : pour en savoir plus sur l’installation, accédez à la [bibliothèque de stockage Microsoft Azure pour Python](https://github.com/Azure/azure-storage-python#option-1-via-pypi).
- Entrez la chaîne de connexion de stockage Azure dans *mwt-ds/DataScience/ds.config* : suivez le modèle *my_app_id: my_connectionString*. Vous pouvez spécifier plusieurs `app_id`. Lorsque vous exécutez `LogDownloader.py`, si l’entrée `app_id` est introuvable dans `ds.config`, `LogDownloader.py` utilise la chaîne de connexion `$Default`.

## <a name="usage"></a>Usage

Accédez à `mwt-ds/DataScience` et exécutez `LogDownloader.py` avec les arguments appropriés, comme indiqué dans le code suivant :

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>parameters

| Entrée | Description | Default |
| --- | --- | --- |
| `-h`, `--help` | Afficher le message d’aide et quitter. | |
| `-a APP_ID`, `--app_id APP_ID` | ID d’application (autrement dit, nom du conteneur d’objet BLOB de stockage Azure). | Obligatoire |
| `-l LOG_DIR`, `--log_dir LOG_DIR` | Répertoire de base pour le téléchargement des données (un sous-dossier est créé).  | Obligatoire |
| `-s START_DATE`, `--start_date START_DATE` | Date de début du téléchargement (incluse), au format *AAAA-MM-JJ*. | `None` |
| `-e END_DATE`, `--end_date END_DATE` | Date de fin du téléchargement (incluse), au format *AAAA-MM-JJ*. | `None` |
| `-o OVERWRITE_MODE`, `--overwrite_mode OVERWRITE_MODE` | Mode de remplacement à utiliser. | |
| | `0`: ne jamais remplacer. Demander à l’utilisateur si les objets BLOB sont actuellement utilisés. | Default |
| | `1`: demander à l’utilisateur la marche à suivre lorsque les fichiers sont de tailles différentes, ou lorsque les objets BLOB sont en cours d’utilisation. | |
| | `2`: toujours remplacer. Télécharger les objets BLOB actuellement utilisés. | |
| | `3`: ne jamais remplacer, et ajouter si la taille est supérieure, sans demander. Télécharger les objets BLOB actuellement utilisés. | |
| | `4`: ne jamais remplacer, et ajouter si la taille est supérieure, sans demander. Ignorer les objets BLOB actuellement utilisés. | |
| `--dry_run` | Imprimer les objets BLOB qui auraient été téléchargés, sans télécharger. | `False` |
| `--create_gzip` | Créer un fichier *gzip* pour Vowpal Wabbit. | `False` |
| `--delta_mod_t DELTA_MOD_T` | Fenêtre de temps, en secondes, pour détecter si un fichier est actuellement en cours d’utilisation. | `3600` s (`1` heure) |
| `--verbose` | Imprimer plus de détails. | `False` |
| `-v VERSION`, `--version VERSION` | Version de l’outil de téléchargement de journal à utiliser. | |
| | `1`: pour les journaux d’activité non traités (uniquement pour la compatibilité descendante). | Déconseillé |
| | `2`: pour les journaux d’activité traités. | Default |

### <a name="examples"></a>Exemples

Pour tester le téléchargement de toutes les données dans votre conteneur d’objets BLOB de stockage Azure, utilisez le code suivant :
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

Pour télécharger uniquement les journaux d’activité créés depuis le 1er janvier 2018 avec `overwrite_mode=4`, utilisez le code suivant :
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

Pour créer un fichier *gzip* fusionnant tous les fichiers téléchargés, utilisez le code suivant :
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
