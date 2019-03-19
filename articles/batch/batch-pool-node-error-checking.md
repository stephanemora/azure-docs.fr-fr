---
title: Rechercher les erreurs des pools et des nœuds - Azure Batch
description: Erreurs à rechercher et comment les éviter lors de la création de pools et de nœuds
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 8d8df9935e935ac8d5a1194cfab103a006cf5546
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57791339"
---
# <a name="check-for-pool-and-node-errors"></a>Rechercher les erreurs des pools et des nœuds

Lorsque vous créez et gérez des pools Azure Batch, certaines opérations se produisent immédiatement. Toutefois, certaines opérations sont asynchrones et s’exécutent en arrière-plan. L’exécution de ces opérations peut prendre plusieurs minutes.

La détection des échecs des opérations qui ont lieu immédiatement est simple, car les échecs éventuels sont retournés immédiatement par l’API, l’interface CLI ou l’interface utilisateur.

Cet article traite des opérations d’arrière-plan qui peuvent survenir pour les groupes et les nœuds de groupe. Il indique comment vous pouvez détecter et éviter les échecs.

## <a name="pool-errors"></a>Erreurs de pool

### <a name="resize-timeout-or-failure"></a>Dépassement du délai d’attente ou échec du redimensionnement

Lors de la création d’un pool ou du redimensionnement d’un pool existant, vous spécifiez le nombre de nœuds cible.  L’opération se termine immédiatement, mais l’allocation réelle des nouveaux nœuds ou la suppression de nœuds existants peut prendre plusieurs minutes.  Vous spécifiez le délai d’expiration du redimensionnement dans l’API [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou l’API [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize). Si Batch ne peut pas obtenir le nombre cible de nœuds pendant le délai de redimensionnement, il arrête l’opération. Le pool est placé dans un état stable et signale les erreurs de redimensionnement.

La propriété [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) de l’évaluation la plus récente signale un délai d’attente de redimensionnement et répertorie toutes les erreurs survenues.

Les causes courantes des dépassements de délai d’expiration du redimensionnement sont les suivantes :

- Le délai d’expiration du redimensionnement est trop court
  - Dans la plupart des cas, le délai d’attente par défaut de 15 minutes est suffisamment long pour que les nœuds de pool puissent être affectés ou supprimés.
  - Si vous allouez un grand nombre de nœuds, nous vous recommandons de régler le délai d’expiration du redimensionnement à 30 minutes. Par exemple, lorsque vous redimensionnez plus de 1 000 nœuds à partir d’une image Place de marché Microsoft Azure, ou plus de 300 nœuds à partir d’une image de machine virtuelle personnalisée.
- Quota de cœurs insuffisant
  - Un compte Batch est limité dans le nombre de cœurs qu’il peut allouer dans tous les pools. Batch arrête l’allocation de nœuds une fois que le quota a été atteint. Vous [ pouvez augmenter](https://docs.microsoft.com/azure/batch/batch-quota-limit) le quota de cœurs pour que Batch puisse allouer plus de nœuds.
- Nombre insuffisant d’adresses IP du sous-réseau quand un [pool est dans un réseau virtuel](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Un sous-réseau de réseau virtuel doit avoir suffisamment d’adresses IP non affectées à allouer à chaque nœud du pool demandés. Sinon, les nœuds ne peuvent pas être créés.
- Ressources insuffisantes quand un [pool est dans un réseau virtuel](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Vous pouvez créer des ressources comme des équilibreurs de charge, des adresses IP publiques et des groupes de sécurité réseau dans le même abonnement que le compte Batch. Vérifiez que les quotas de l’abonnement sont suffisants pour ces ressources.
- Vastes pools avec images de machines virtuelles personnalisées
  - L’allocation de vastes pools utilisant des images de machine virtuelle personnalisées peut prendre plus de temps et des dépassements du délai d’expiration peuvent se produire.  Consultez [Utiliser une image personnalisée pour créer un pool de machines virtuelles](https://docs.microsoft.com/azure/batch/batch-custom-images) pour connaître les suggestions sur les limites et la configuration.

### <a name="automatic-scaling-failures"></a>Échecs de mise à l’échelle automatique

Vous pouvez également configurer Azure Batch pour mettre automatiquement à l’échelle le nombre de nœuds dans un pool. Vous définissez les paramètres de la [formule de mise à l’échelle automatique d’un pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). Le service Batch utilise la formule pour évaluer régulièrement le nombre de nœuds dans le pool et définir un nouveau nombre de cibles. Les types de problèmes suivants peuvent se produire :

- L’évaluation de la mise à l’échelle automatique échoue.
- L’opération de redimensionnement qui en résulte échoue et dépasse le délai d’expiration.
- Un problème portant sur la formule de mise à l’échelle automatique produit des valeurs cibles de nœud incorrectes. Le redimensionnement fonctionne ou arrive à expiration.

Vous pouvez obtenir des informations sur la dernière évaluation automatique de mise à l’échelle à l’aide de la propriété [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Cette propriété rapporte le temps d’évaluation, les valeurs et les résultats, ainsi que les éventuelles erreurs de performance.

Un [événement de fin de redimensionnement de pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) capture les informations sur toutes les évaluations.

### <a name="delete"></a>Supprimer

Lorsque vous supprimez un pool qui contient des nœuds, le premier Batch supprime les nœuds. Il supprime ensuite l’objet pool lui-même. Quelques minutes peuvent être nécessaires pour supprimer les nœuds du pool.

Batch définit l’[état du pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) sur **En cours de suppression** pendant le processus de suppression. L’application appelante peut détecter si la suppression du pool prend trop de temps en utilisant les propriétés **state** et **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Erreurs des nœuds de calcul du pool

Même lorsque Batch alloue des nœuds d’un pool, différents problèmes peuvent les endommager ou les rendre inutilisables. Ces nœuds engendrent des frais. Il est important de détecter les problèmes afin de ne pas payer pour des nœuds inutilisables.

### <a name="start-task-failure"></a>Échec de la tâche de démarrage

Vous souhaiterez peut-être indiquer une [tâche de démarrage](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) facultative pour un pool. Comme avec n’importe quelle tâche, vous pouvez utiliser une ligne de commande et des fichiers de ressources à télécharger à partir du stockage. La tâche de démarrage est exécutée pour chaque nœud après son lancement. La propriété **waitForSuccess** indique si Batch attend que la tâche de démarrage se termine avec succès avant de planifier des tâches vers un nœud.

Que se passe-t-il si vous avez configuré le nœud de sorte qu’il attende l’achèvement réussi de la tâche de démarrage mais que la tâche de démarrage échoue ? Dans ce cas, le nœud est inutilisable mais entraîne toujours des frais.

Vous pouvez détecter les échecs des tâches de démarrage via les propriétés [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) et [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) de la propriété du nœud [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) du plus haut niveau.

Une tâche de démarrage qui a échoué fait passer [l’état](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) du nœud à **starttaskfailed** si vous avez défini **waitForSuccess** sur **true**.

Comme avec n’importe quelle tâche, plusieurs causes d’échec de la tâche de démarrage sont possibles.  Pour résoudre les problèmes, examinez les fichiers journaux stdout, stderr et tous les fichiers journaux spécifiques aux tâches.

### <a name="application-package-download-failure"></a>Échec du téléchargement des packages d’application

Vous pouvez spécifier un ou plusieurs packages d’application pour un pool. Batch télécharge les fichiers de package spécifiés vers chaque nœud et décompresse les fichiers après le démarrage du nœud, mais avant que les tâches ne soient planifiées. Il est courant d’utiliser une ligne de commande de tâche de démarrage conjointement avec des packages d’application. Par exemple, pour copier des fichiers vers un emplacement différent ou exécuter le programme d’installation.

La propriété [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) du nœud rapporte un échec du téléchargement et de décompression d’un package d’application. Batch définit l’état du nœud sur **inutilisable**.

### <a name="node-in-unusable-state"></a>Nœud dans un état inutilisable

Azure Batch peut définir [l’état du nœud](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) sur **inutilisable** pour de nombreuses raisons. Quand l’état du nœud est défini sur **inutilisable**, il n’est pas possible de planifier des tâches pour le nœud, mais il continue d’entraîner la facturation de frais.

Batch essaie toujours de récupérer les nœuds inutilisables, mais la récupération est ou non possible, selon ce qui en est la cause.

Si Batch peut déterminer la cause de l’erreur, Batch la signale à l’aide de la propriété [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror).

Voici des exemples supplémentaires de causes aboutissant à des nœuds **inutilisables** :

- Une image de machine virtuelle personnalisée n’est pas valide. Par exemple, il s’agit d’une image qui n’est pas correctement préparée.
- Une machine est déplacée en raison d’une défaillance de l’infrastructure ou d’une mise à niveau de bas niveau. Batch récupère le nœud.

### <a name="node-agent-log-files"></a>Fichiers de journaux de l’agent du nœud

Si vous devez contacter le support technique concernant un problème de nœud de pool, le processus de l’agent Batch qui s’exécute sur chaque nœud du pool peut vous fournir des fichiers journaux très utiles. Les fichiers journaux pour un nœud peuvent être chargés via le portail Azure, Batch Explorer ou une [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Il est utile de charger et d’enregistrer les fichiers journaux. Ensuite, vous pouvez supprimer le nœud ou le pool pour maîtriser le coût des nœuds en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

Vérifiez que vous avez paramétré votre application pour implémenter le contrôle complet des erreurs, en particulier pour les opérations asynchrones. Il peut être essentiel de rapidement détecter et diagnostiquer les problèmes.
