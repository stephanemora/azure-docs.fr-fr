---
title: Optimiser les performances d’AzCopy v10 avec Stockage Azure | Microsoft Docs
description: Cet article vous aide à optimiser les performances d’AzCopy v10 avec Stockage Azure.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b60fc4b1fc20c455c2c409f544a8af16f1dbf8d1
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107508954"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Optimiser les performances d’AzCopy avec Stockage Azure

AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage. Cet article vous aide à optimiser les performances.

> [!NOTE]
> Si vous recherchez du contenu pour vous aider à bien démarrer avec AzCopy, consultez [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md).

Vous pouvez effectuer un test d’évaluation des performances, puis utiliser des commandes et des variables d’environnement pour trouver un compromis optimal entre les performances et la consommation des ressources.

## <a name="run-benchmark-tests"></a>Exécuter des tests d’évaluation

Vous pouvez exécuter un test d’évaluation des performances sur des conteneurs d’objets blob ou des partages de fichiers spécifiques pour voir des statistiques générales sur les performances et identifier les goulots d’étranglement des performances. Vous pouvez exécuter le test en chargeant ou en téléchargeant des données de test générées. 

Utilisez la commande suivante pour exécuter un test d’évaluation des performances.

**Syntaxe**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**Exemple**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> Cet exemple englobe les arguments de chemin d’accès avec des guillemets simples (' '). Utilisez des guillemets simples dans tous les interpréteurs de commandes, à l’exception de l’interface de commande Windows (cmd. exe). Si vous utilisez une interface de commande Windows (cmd. exe), placez les arguments de chemin d’accès entre guillemets doubles (" ") au lieu de guillemets simples (' ').

Cette commande exécute un test d’évaluation des performances en chargeant les données de test dans une destination spécifiée. Les données de test sont générées en mémoire, chargées dans la destination, puis supprimées de la destination une fois le test terminé. Vous pouvez spécifier le nombre de fichiers à générer et leur taille souhaitée à l’aide de paramètres de commande facultatifs.

Si vous préférez exécuter ce test en téléchargeant des données, définissez le paramètre `mode` sur `download`. Pour obtenir des informations de référence détaillées, consultez [benchmark azcopy](storage-ref-azcopy-bench.md). 

## <a name="optimize-for-large-numbers-of-small-files"></a>Optimiser pour de grands nombres de petits fichiers

Le débit peut diminuer lors du transfert de petits fichiers, en particulier lors du transfert d’un grand nombre d’entre eux. Pour optimiser les performances, réduisez la taille de chaque travail. Pour les opérations de téléchargement et de chargement, augmentez la concurrence, diminuez l’activité des journaux et désactivez les fonctionnalités qui ont un coût élevé en matière de performances.

#### <a name="reduce-the-size-of-each-job"></a>Réduire la taille de chaque travail

Pour obtenir des performances optimales, vérifiez que chaque travail transfère moins de 10 millions de fichiers. Les travaux qui transfèrent plus de 50 millions de fichiers peuvent s’exécuter assez lentement, car le mécanisme de suivi des travaux d’AzCopy entraîne une surcharge importante. Pour réduire la charge, envisagez de diviser les grands travaux en travaux plus petits. 

Une façon de réduire la taille d’un travail consiste à limiter le nombre de fichiers affectés par un travail. Pour cela, vous pouvez utiliser des paramètres de commande. Par exemple, un travail peut copier seulement un sous-ensemble de répertoires si vous utilisez le paramètre `include path` dans le cadre de la commande [azcopy copy](storage-ref-azcopy-copy.md). 

Utilisez le paramètre `include-pattern` pour copier les fichiers qui ont une extension spécifique (par exemple : `*.pdf`). Dans un travail distinct, utilisez le paramètre `exclude-pattern` pour copier tous les fichiers qui n’ont pas l’extension `*.pdf`. Pour obtenir des exemples, consultez [Charger des fichiers spécifiques](storage-use-azcopy-blobs-upload.md#upload-specific-files) et [Télécharger des objets blob spécifiques](storage-use-azcopy-blobs-download.md#download-specific-blobs).

Une fois que vous avez décidé de diviser les grands travaux en travaux plus petits, envisagez d’exécuter des travaux sur plusieurs machines virtuelles.

#### <a name="increase-concurrency"></a>Augmenter les accès concurrentiels

Si vous chargez ou téléchargez des fichiers, utilisez la variable d’environnement `AZCOPY_CONCURRENCY_VALUE` pour augmenter le nombre de demandes simultanées qui peuvent être effectuées sur votre machine. Définissez cette variable sur la valeur la plus élevée possible sans compromettre les performances de votre machine. Pour plus d’informations sur cette variable, consultez la section [Augmenter le nombre de demandes simultanées](#increase-the-number-of-concurrent-requests) de cet article.

Si vous copiez des objets blob entre des comptes de stockage, envisagez de définir la valeur de la variable d’environnement `AZCOPY_CONCURRENCY_VALUE` sur une valeur supérieure à `1000`. Vous pouvez définir cette variable sur une valeur élevée, car AzCopy utilise les API de serveur à serveur : les données sont donc copiées directement entre les serveurs de stockage et n’utilisent pas la puissance de traitement de votre machine.  

#### <a name="decrease-the-number-of-logs-generated"></a>Réduire le nombre de journaux générés

Vous pouvez améliorer les performances en réduisant le nombre d’entrées de journal créées par AzCopy au fur et à mesure de l’exécution d’une opération. Par défaut, AzCopy journalise toutes les activités relatives à une opération. Pour obtenir des performances optimales, envisagez de définir le paramètre `log-level` de votre commande de copie, de synchronisation ou de suppression sur `ERROR`. De cette façon, AzCopy consigne seulement les erreurs. Par défaut, le niveau de journalisation est défini sur `INFO`. 

#### <a name="turn-off-length-checking"></a>Désactiver la vérification de la longueur 

Si vous chargez ou téléchargez des fichiers, envisagez de définir le paramètre `--check-length` de vos commandes de copie et de synchronisation sur `false`. Ceci empêche AzCopy de vérifier la longueur d’un fichier après un transfert. Par défaut, AzCopy vérifie la longueur pour garantir que les fichiers source et de destination correspondent une fois le transfert terminé. AzCopy effectue cette vérification après chaque transfert de fichier. Cette vérification peut dégrader les performances quand les travaux transfèrent un grand nombre de petits fichiers. 

#### <a name="turn-on-concurrent-local-scanning-linux"></a>Activer l’analyse locale simultanée (Linux)

Les analyses de fichiers sur certains systèmes Linux ne s’exécutent pas suffisamment rapidement pour saturer toutes les connexions réseau parallèles. Dans ce cas, vous pouvez définir `AZCOPY_CONCURRENT_SCAN` sur `true`. 

## <a name="increase-the-number-of-concurrent-requests"></a>Augmenter le nombre de demandes simultanées

Vous pouvez augmenter le débit en définissant la variable d’environnement `AZCOPY_CONCURRENCY_VALUE`. Cette variable spécifie le nombre de demandes pouvant être effectuées simultanément.  

Si votre ordinateur dispose de moins de 5 unités centrales, la valeur de cette variable est définie sur `32`. Sinon, la valeur par défaut est égale à 16 multiplié par le nombre d’unités centrales. La valeur maximale par défaut de cette variable est `3000`, mais vous pouvez l’augmenter ou la diminuer manuellement. 

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Utilisez `azcopy env` pour vérifier la valeur actuelle de cette variable. Si la valeur est vide, vous pouvez lire la valeur utilisée en examinant le début de tout fichier journal AzCopy. La valeur sélectionnée et la raison pour laquelle elle a été sélectionnée sont signalées ici.

Avant de définir cette variable, nous vous recommandons d’exécuter un test d’évaluation. Le processus de test d’évaluation signalera la valeur de concurrence recommandée. En guise d’alternative, si vos conditions de réseau et charges utiles varient, affectez le mot `AUTO` à cette variable plutôt qu’un nombre particulier. AzCopy exécutera alors toujours le même processus de réglage automatique que celui qu’il utilise dans les tests d’évaluation.

## <a name="limit-the-throughput-data-rate"></a>Limiter le débit de données

Vous pouvez utiliser l’indicateur `cap-mbps` dans vos commandes pour plafonner le débit de données. Par exemple, la commande suivante reprend un travail et plafonne le débit à `10` mégabits (Mb) par seconde. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>Optimiser l’utilisation de la mémoire

Définissez la variable d’environnement `AZCOPY_BUFFER_GB` pour spécifier la quantité maximale de mémoire système qu’AzCopy doit utiliser pour la mise en mémoire tampon lors du téléchargement et du chargement des fichiers. Exprimez cette valeur en gigaoctets (Go).

| Système d’exploitation | Commande  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> Le suivi des tâches entraîne toujours une surcharge supplémentaire dans l’utilisation de la mémoire. La quantité varie en fonction du nombre de transferts dans une tâche. Les mémoires tampons représentent le composant le plus volumineux de l’utilisation de la mémoire. Vous pouvez contrôler la surcharge en utilisant `AZCOPY_BUFFER_GB` pour répondre approximativement à vos exigences, mais aucun indicateur ,’est disponible pour limiter strictement l’utilisation de la mémoire globale.

## <a name="optimize-file-synchronization"></a>Optimiser la synchronisation des fichiers

La commande [sync](storage-ref-azcopy-sync.md) identifie tous les fichiers à l'emplacement de destination, puis compare les noms de fichiers et les derniers horodatages modifiés avant de démarrer l'opération de synchronisation. Si vous disposez d'un grand nombre de fichiers, vous pouvez améliorer les performances en éliminant ce traitement préalable. 

Pour ce faire, utilisez plutôt la commande [azcopy copy](storage-ref-azcopy-copy.md) et définissez l'indicateur `--overwrite` sur `ifSourceNewer`. AzCopy comparera les fichiers tels qu'ils sont copiés sans effectuer d'analyses et de comparaison préalables. Cela offre un avantage en termes de performances lorsque le nombre de fichiers à comparer est important.

La commande [azcopy copy](storage-ref-azcopy-copy.md) ne supprime pas les fichiers de l'emplacement de destination. Par conséquent, si vous souhaitez supprimer les fichiers à l'emplacement de destination lorsqu'ils n'existent plus à l'emplacement source, utilisez la commande [azcopy sync](storage-ref-azcopy-sync.md) avec l'indicateur `--delete-destination` défini sur la valeur `true` ou `prompt`.

## <a name="see-also"></a>Voir aussi

- [Bien démarrer avec AzCopy](storage-use-azcopy-v10.md)