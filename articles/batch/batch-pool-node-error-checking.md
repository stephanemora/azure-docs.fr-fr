---
title: Recherche des erreurs des pools et des nœuds Batch
description: Erreurs à rechercher et comment les éviter lors de la création de pools et de nœuds
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435821"
---
# <a name="checking-for-pool-and-node-errors"></a>Recherche des erreurs des pools et des nœuds

Lors de la création et de la gestion des pools Batch, certaines opérations se produisent immédiatement et d’autres sont asynchrones : ces dernières ne se produisent pas immédiatement, s’exécutent en arrière-plan et peuvent durer plusieurs minutes.

La détection des échecs des opérations qui ont lieu immédiatement est simple, car les échecs éventuels sont retournés immédiatement par l’API, l’interface CLI ou l’interface utilisateur.

Cet article couvre les opérations d’arrière-plan qui peuvent avoir lieu pour les pools et les nœuds de pool : il spécifie comment les échecs peuvent être détectés, et comment vous pouvez les éviter.

## <a name="pool-errors"></a>Erreurs de pool

### <a name="resize-timeout-or-failure"></a>Dépassement du délai d’attente ou échec du redimensionnement

Lors de la création d’un pool ou du redimensionnement d’un pool existant, le nombre de nœuds cible est spécifié.  L’opération se termine immédiatement, mais l’allocation réelle des nouveaux nœuds ou la suppression de nœuds existants a lieu en arrière-plan pendant parfois plusieurs minutes.  Un délai d’expiration pour le redimensionnement est spécifié dans l’API [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) ou [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) : si le nombre de nœuds cible ne peuvent pas être obtenu au terme du délai d’expiration du redimensionnement, l’opération doit s’arrêter, le pool passant à un état stable et avec des erreurs de redimensionnement.

Un dépassement de délai d’expiration du redimensionnement est signalé par la propriété [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) pour la dernière évaluation, qui liste une ou plusieurs erreurs qui se sont produites.

Les causes courantes des dépassements de délai d’expiration du redimensionnement sont les suivantes :
- Le délai d’expiration du redimensionnement est trop court
  - Le délai d’expiration par défaut est de 15 minutes, ce qui est normalement un temps bien suffisant pour l’allocation ou la suppression des nœuds du pool.
  - Lors de l’allocation d’un grand nombre de nœuds (plus de 1 000 nœuds à partir d’une image de la Place de marché ou plus de 300 à partir d’une image personnalisée), pendant la création ou le redimensionnement d’un pool, un délai d’expiration de 30 minutes est recommandé.
- Quota de cœurs insuffisant
  - Un compte Batch a un quota de nombre de cœurs à allouer pour tous les pools.  Batch n’alloue pas de nœuds une fois que le quota a été atteint.  Le quota de cœurs [peut être augmenté](https://docs.microsoft.com/azure/batch/batch-quota-limit) pour permettre l’allocation de plus de nœuds.
- Nombre insuffisant d’adresses IP du sous-réseau quand un [pool est dans un réseau virtuel](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Un sous-réseau de réseau virtuel doit avoir suffisamment d’adresses IP non affectées à allouer à tous les nœuds du pool demandés ; sinon, les nœuds ne peuvent pas être créés.
- Ressources insuffisantes quand un [pool est dans un réseau virtuel](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Des ressources comme des équilibreurs de charge, des adresses IP publiques et des groupes de sécurité réseau sont créées dans l’abonnement utilisé pour créer le compte Batch.  Les quotas de l’abonnement pour ces ressources doivent être suffisants.
- Utilisation d’une image de machine virtuelle personnalisée pour les grands pools
  - L’allocation des grands pools utilisant des images personnalisées peut prendre plus de temps et des dépassements du délai d’expiration peuvent se produire.  Les limites et des recommandations de configuration sont fournies dans un [article spécifique](https://docs.microsoft.com/azure/batch/batch-custom-images). 

### <a name="auto-scale-failures"></a>Échecs de la mise à l’échelle automatique

Au lieu de définir explicitement le nombre de nœuds cible pour un pool lors de la création ou le redimensionnement d’un pool, vous pouvez mettre à l’échelle automatiquement le nombre de nœuds d’un pool.  Une [formule de mise à l’échelle automatique peut être créée pour un pool](https://docs.microsoft.com/azure/batch/batch-automatic-scaling), qui doit être évaluée à un intervalle régulier configurable pour définir le nombre de nœuds cible pour le pool.  Les types de problèmes suivants peuvent se produire et être détectés :

- L’évaluation de la mise à l’échelle automatique peut échouer.
- L’opération de redimensionnement qui en résulte peut échouer et dépasser le délai d’expiration.
- Il peut y avoir un problème avec la formule de mise à l’échelle automatique, conduisant à des valeurs cibles incorrectes pour les nœuds, avec le redimensionnement en cours ou dépassant le délai d’expiration.

Vous pouvez obtenir des informations sur la dernière évaluation de la mise à l’échelle automatique avec la propriété [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun), qui signale l’heure de l’évaluation, les valeurs et le résultat de l’évaluation, ainsi que les erreurs éventuelles survenues lors de l’évaluation.

Les informations sur toutes les évaluations sont automatiquement capturées par un [événement de fin de redimensionnement de pool](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event).

### <a name="delete"></a>Supprimer

En supposant qu’il existe des nœuds dans un pool, une opération de suppression de pool supprime d’abord les nœuds, puis l’objet de pool lui-même.  Quelques minutes peuvent être nécessaires pour supprimer les nœuds du pool.

[L’état du pool](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) passe à « En cours de suppression » pendant le processus de suppression.  L’application appelante peut détecter si la suppression du pool prend trop de temps en utilisant les propriétés « state » et « stateTransitionTime ».

## <a name="pool-compute-node-errors"></a>Erreurs des nœuds de calcul du pool

Les nœuds peuvent être alloués correctement dans un pool, mais différents problèmes peuvent conduire les nœuds à être défectueux et inutilisables.  Une fois que les nœuds sont alloués dans un pool, ils entraînent une facturation de frais, et il est donc important de détecter les problèmes afin d’éviter de payer pour des nœuds qui ne peuvent pas être utilisés.

### <a name="start-task-failure"></a>Échec de la tâche de démarrage

Vous pouvez spécifier une [tâche de démarrage](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) facultative pour un pool.  Comme avec n’importe quelle tâche, vous pouvez spécifier une ligne de commande et des fichiers de ressources à télécharger à partir du stockage.  La tâche de démarrage est spécifiée pour le pool, mais elle s’exécute sur chaque nœud : une fois que chaque nœud a été démarré, la tâche de démarrage est exécutée.  Une propriété supplémentaire de la [tâche de démarrage](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask), « waitForSuccess », spécifie si Batch doit attendre que la tâche de démarrage se termine correctement avant de planifier des tâches sur un nœud.

Si une tâche de démarrage échoue et que la configuration de la tâche de démarrage spécifiait d’attendre qu’elle se termine correctement, le nœud est inutilisable, tout en entraînant la facturation de frais.

Les échecs des tâches de démarrage peuvent être détectés via les propriétés [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) et [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) de la propriété du nœud [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) du plus haut niveau.

Une tâche de démarrage qui a échoué fait passer [l’état](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) du nœud à « starttaskfailed », mais seulement si « waitForSuccess » a été défini sur « true ».

Comme avec n’importe quelle tâche, plusieurs causes d’échec de la tâche de démarrage sont possibles.  Pour résoudre les problèmes, les fichiers journaux stdout, stderr et tous les fichiers journaux spécifiques aux tâches doivent être examinés.

### <a name="application-package-download-failure"></a>Échec du téléchargement des packages d’application

Un ou plusieurs packages d’application facultatifs peuvent être spécifiés pour un pool, les fichiers de package spécifiés devant alors être téléchargés sur chaque nœud et décompressés une fois que le nœud a démarré, mais avant que des tâches soient planifiées.  Il est courant d’utiliser une ligne de commande de tâche de démarrage en combinaison avec des packages d’application, par exemple pour copier des fichiers à un autre emplacement ou exécuter un programme d’installation.

Un échec du téléchargement et de la décompression d’un package d’application est signalé par la propriété [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) du nœud.  L’état du nœud est défini sur « inutilisable ».

### <a name="node-in-unusable-state"></a>Nœud dans un état inutilisable

[L’état du nœud](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) peut être défini sur « inutilisable » pour de nombreuses raisons.  Quand l’état est « inutilisable », des tâches ne peuvent pas être planifiées pour le nœud, mais ce nœud continue d’entraîner la facturation de frais.

Batch essaie toujours de récupérer les nœuds inutilisables, mais la récupération est ou non possible, selon ce qui en est la cause.

Quand la cause peut être déterminée, elle est signalée par la propriété [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) du nœud.

Voici d’autres exemples de causes aboutissant à des nœuds « inutilisables » :

- Image personnalisée non valide ; par exemple incorrectement préparée.
- Échec de l’infrastructure ou mise à niveau de bas niveau, conduisant au déplacement de la machine virtuelle sous-jacente ; Batch pourra récupérer le nœud.

### <a name="node-agent-log-files"></a>Fichiers de journaux de l’agent du nœud

S’il est nécessaire de contacter le support technique concernant un problème de nœud de pool, vous pouvez obtenir les fichiers journaux du processus de l’agent Batch qui s’exécute sur chaque nœud du pool.  Les fichiers journaux pour un nœud peuvent être chargés via le portail Azure, Batch Explorer ou une [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs).  Charger et enregistrer les fichiers journaux peut être extrêmement utile, car le nœud ou le pool peut ainsi être supprimé pour économiser les coûts des nœuds en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

Vérifiez que votre application a implémenté une recherche complète des erreurs, en particulier pour les opérations asynchrones, pour détecter rapidement et diagnostiquer les problèmes.
