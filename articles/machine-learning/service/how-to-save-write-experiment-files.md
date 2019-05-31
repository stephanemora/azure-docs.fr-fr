---
title: Éviter les limitations de stockage et de tester la latence avec des répertoires d’entrée et de sortie
description: Dans cet article, vous allez où enregistrer les fichiers d’entrée de votre expérience et dans lequel écrire les fichiers de sortie pour empêcher les erreurs de limitation du stockage et de tester la latence.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 28d8c47db8ea9c8a51bc8e9deb0a689eb0b20177
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392898"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Où enregistrer et d’écrire des fichiers pour les expériences de Azure Machine Learning

Dans cet article, vous allez où enregistrer les fichiers d’entrée et dans lequel écrire les fichiers de sortie à partir de vos expériences d’empêcher le stockage limitent les erreurs et tester la latence.

Lorsque le lancement de formation s’exécute sur un [cible de calcul](how-to-set-up-training-targets.md), ils sont isolés des environnements extérieurs. L’objectif de cette conception consiste à garantir la reproductibilité et la portabilité de l’expérience. Si vous exécutez deux fois le même script, sur le même ou une autre cible de calcul, vous obtenez les mêmes résultats. Grâce à cette conception, vous pouvez traiter les cibles de calcul en tant que ressources de calcul sans état, chacun n’ayant aucune affinité avec les travaux qui sont en cours d’exécution une fois terminés.

## <a name="where-to-save-input-files"></a>Emplacement où enregistrer les fichiers d’entrée

Avant d’initier une expérience sur votre ordinateur local ou sur une cible de calcul, vous devez vous assurer que les fichiers nécessaires sont disponibles pour la cible de calcul, telles que les fichiers de dépendance et des fichiers de données de que votre code doit s’exécuter.

Azure Machine Learning exécute des scripts d’apprentissage en copiant le dossier de la totalité du script dans le contexte de calcul cible et effectue une capture instantanée. La limite de stockage pour les instantanés de l’expérience est de 300 Mo et/ou les fichiers de 2000.

Pour cette raison, nous vous recommandons de :

* **En stockant les fichiers dans un Azure Machine Learning [banque de données](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Cela évite les problèmes de latence d’expérience et présente l’avantage de l’accès aux données à partir d’une cible de calcul à distance, ce qui signifie que l’authentification et le montage sont gérés par le service Azure Machine Learning. En savoir plus sur la spécification d’une banque de données en tant que votre répertoire source, télécharger des fichiers dans votre magasin de données dans le [accéder aux données à partir de vos banques de données](how-to-access-data.md) article.

* **Si vous devez uniquement quelques fichiers de données et la dépendance de scripts et ne pouvez pas utiliser une banque de données,** placer les fichiers dans le répertoire du même dossier que votre script de formation. Spécifiez ce dossier en tant que votre `source_directory` directement dans votre script de formation, ou dans le code qui appelle votre script de formation.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de stockage des instantanés d’expérience

Pour des expériences, Azure Machine Learning crée automatiquement un instantané de l’expérience de votre code basé sur le répertoire que vous suggérez lorsque vous configurez l’exécution. Cela a une limite totale de 300 Mo et/ou les fichiers de 2000. Si vous dépassez cette limite, vous verrez l’erreur suivante :

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Pour résoudre cette erreur, stocker vos fichiers de l’expérience sur une banque de données. Si vous ne pouvez pas utiliser une banque de données, le tableau ci-dessous propose des solutions alternatives possibles.

Expérience&nbsp;description|Solution de limite de stockage
---|---
Moins de 2000 fichiers & ne peut pas utiliser une banque de données| Remplacer la limite de taille de capture instantanée avec <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Cette opération peut prendre plusieurs minutes selon le nombre et la taille des fichiers.
Doit utiliser le répertoire de script spécifique| Rendre un `.amlignore` fichier à exclure des fichiers d’instantané de votre expérience qui ne font pas partie du code source. Ajoutez les noms de fichiers pour le `.amlignore` de fichier et placez-le dans le même répertoire que votre script de formation. Le `.amlignore` fichier utilise le même [syntaxe et les modèles](https://git-scm.com/docs/gitignore) comme un `.gitignore` fichier.
Pipeline|Utiliser un autre sous-répertoire pour chaque étape
Notebooks Jupyter| Créer un `.amlignore` de fichiers ou de déplacer votre ordinateur portable dans un sous-répertoire vide, et d’exécuter à nouveau votre code.

## <a name="where-to-write-files"></a>Dans lequel écrire les fichiers

En raison de l’isolation des expériences d’apprentissage, les modifications apportées aux fichiers qui se produisent lors des exécutions pas nécessairement persistent en dehors de votre environnement. Si votre script modifie les fichiers locaux de calcul, les modifications ne sont pas conservées pour votre expérimentation suivante, et elles ne sont pas propagées vers l’ordinateur client automatiquement. Par conséquent, les modifications apportées au cours de l’expérience de première exécution n’et ne doivent pas affecter ceux de la seconde.

Lors de l’écriture des modifications, nous vous recommandons d’écrire des fichiers dans un magasin de données Azure Machine Learning. Consultez [accéder aux données à partir de vos banques de données](how-to-access-data.md).

Si vous ne nécessitent pas une banque de données, écrire des fichiers à la `./outputs` et/ou `./logs` dossier.

>[!Important]
> Deux dossiers, *génère* et *journaux*, reçoivent un traitement spécial par Azure Machine Learning. Pendant l’apprentissage, lorsque vous écrivez des fichiers à`./outputs` et`./logs` dossiers, les fichiers téléchargera automatiquement à votre historique d’exécution, afin que vous ayez d’y accéder une fois l’exécution terminée.

* **Pour la sortie, tels que les messages d’état ou les résultats de la notation,** écrire des fichiers à le `./outputs` dossier, afin qu’ils sont conservés en tant qu’artefacts dans l’historique des exécutions. N’oubliez pas de nombre et taille des fichiers écrits dans ce dossier, comme la latence peut se produire lorsque le contenu est chargé dans l’historique des exécutions. Si la latence pose problème, il est recommandé d’écriture de fichiers sur une banque de données.

* **Pour enregistrer le fichier écrit sous forme de journaux dans l’historique des exécutions,** écrire des fichiers à `./logs` dossier. Les journaux sont téléchargés en temps réel, donc cette méthode convient pour la diffusion des mises à jour automatiques à partir d’une exécution à distance.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [l’accès aux données à partir de vos banques de données](how-to-access-data.md).

* En savoir plus sur [comment définir des cibles de formation](how-to-set-up-training-targets.md).
