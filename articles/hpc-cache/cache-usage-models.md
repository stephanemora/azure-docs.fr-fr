---
title: Modèles d’utilisation Azure HPC Cache
description: Décrit les différents modèles d’utilisation du cache et la manière de choisir entre eux pour définir la mise en cache en lecture seule ou en lecture/écriture et contrôler d’autres paramètres de mise en cache.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 3ad252520ca0cf7acdb3c84ef1da87c8076f3172
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775712"
---
# <a name="understand-cache-usage-models"></a>Comprendre les modèles d’utilisation du cache

Les modèles d’utilisation du cache vous permettent de personnaliser la façon dont Azure HPC Cache stocke les fichiers pour accélérer votre workflow.

## <a name="basic-file-caching-concepts"></a>Concepts de base de la mise en cache de fichiers

La mise en cache des fichiers correspond à la façon dont Azure HPC Cache accélère les demandes des clients. Il utilise les pratiques de base suivantes :

* **Mise en cache en lecture** : Azure HPC Cache conserve une copie des fichiers que les clients demandent au système de stockage. La prochaine fois qu’un client demande le même fichier, le cache HPC peut fournir la version présente dans son cache au lieu de devoir aller le chercher à nouveau dans le système de stockage back-end.

* **Mise en cache en écriture** : En option, Azure HPC Cache peut stocker une copie de tous les fichiers modifiés envoyés par les ordinateurs clients. Si plusieurs clients apportent des modifications au même fichier sur une courte période, le cache peut rassembler toutes les modifications dans le cache au lieu de devoir écrire chaque modification individuellement dans le système de stockage back-end.

  Après un laps de temps spécifié sans modification, le cache déplace le fichier vers le système de stockage à long terme.

  Si la mise en cache en écriture est désactivée, le cache ne stocke pas le fichier modifié et l’écrit immédiatement dans le système de stockage back-end.

* **Délai de l’écriture différée** : Pour un cache dont la mise en cache en écriture est activée, le délai d’écriture différée correspond à la durée pendant laquelle le cache attend des modifications supplémentaires du fichier avant de copier le fichier sur le système de stockage back-end.

* **Vérification du back-end** : Le paramètre de vérification du back-end détermine la fréquence à laquelle le cache compare sa copie locale d’un fichier à la version distante sur le système de stockage back-end. Si la copie du back-end est plus récente que la copie mise en cache, le cache récupère la copie distante et la stocke pour les demandes ultérieures.

  Le paramètre de vérification du back-end indique quand le cache compare *automatiquement* ses fichiers aux fichiers sources dans le stockage distant. Toutefois, vous pouvez forcer Azure HPC Cache à comparer les fichiers en effectuant une opération d’annuaire incluant une demande readdirplus. Readdirplus est une API NFS standard (également appelée lecture étendue) qui retourne des métadonnées d’annuaire, ce qui amène le cache à comparer et à mettre à jour les fichiers.

Les modèles d’utilisation intégrés à Azure HPC Cache présentent différentes valeurs pour ces paramètres afin que vous puissiez choisir la combinaison la mieux adaptée à votre situation.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Choisir le modèle d’utilisation approprié pour votre workflow

Vous devez choisir un modèle d’utilisation pour chaque cible de stockage montée sur NFS que vous utilisez. Les cibles de stockage Blob Azure disposent d’un modèle d’utilisation intégré qui ne peut pas être personnalisé.

Les modèles d’utilisation du cache HPC vous permettent de choisir la manière d’équilibrer la vitesse de réponse avec le risque d’obtenir des données obsolètes. Si vous souhaitez optimiser la vitesse de lecture des fichiers, vous pourriez ne pas vous préoccuper du fait que les fichiers dans le cache sont vérifiés par rapport aux fichiers sur le serveur principal. En revanche, si vous souhaitez vous assurer que vos fichiers sont toujours à jour avec le stockage étendu, choisissez un modèle qui effectue des vérifications fréquemment.

Voici les options de modèle d’utilisation :

* **Lire les écritures lourdes et peu fréquentes** : utilisez cette option si vous souhaitez accélérer l’accès en lecture aux fichiers qui sont statiques ou rarement modifiés.

  Cette option met en cache les lectures du client, mais pas les écritures. Elle transmet immédiatement les écritures au stockage back-end.
  
  Les fichiers stockés dans le cache ne sont pas comparés automatiquement aux fichiers sur le volume de stockage NFS. (Lisez la description de la vérification du back-end ci-dessus pour savoir comment les comparer manuellement.)

  N’utilisez pas cette option s’il existe un risque de modification directe d’un fichier sur le système de stockage sans l’avoir d’abord écrit dans le cache. Si cela se produit, la version mise en cache du fichier n’est pas synchronisée avec le fichier sur le serveur principal.

* **Opérations d’écriture supérieures à 15 %**  : cette option accélère les performances de lecture et d’écriture. Quand vous utilisez cette option, tous les clients doivent accéder aux fichiers par le biais d’Azure HPC Cache au lieu de monter le stockage backend directement. Les fichiers mis en cache auront des modifications récentes qui n’ont pas encore été copiées dans le back end.

  Dans ce modèle d’utilisation, les fichiers figurant dans le cache ne sont vérifiés par rapport aux fichiers se trouvant dans le stockage principal que toutes les huit heures. La version mise en cache du fichier est supposée être plus récente. Un fichier modifié dans le cache est écrit dans le système de stockage back-end après être resté dans le cache pendant 20 minutes<!-- an hour --> sans autre modification.

* **Les clients écrivent dans la cible NFS en ignorant le cache** : choisissez cette option si des clients dans votre workflow écrivent des données directement dans le système de stockage sans écrire au préalable dans le cache, ou si vous voulez optimiser la cohérence des données. Les fichiers que les clients demandent sont mis en cache (lecture), mais les modifications apportées à ces fichiers par le client (écriture) ne sont pas mises en cache. Elles sont transmises directement au système de stockage back-end.

  Avec ce modèle d’utilisation, les fichiers du cache sont fréquemment comparés aux versions du back-end pour y rechercher des mises à jour (toutes les 30 secondes). Cette vérification permet de modifier les fichiers en dehors du cache tout en conservant la cohérence des données.

  > [!TIP]
  > Ces trois premiers modèles d’utilisation de base peuvent être utilisés pour gérer la majorité des workflows d’Azure HPC Cache. Les options suivantes sont destinées à des scénarios moins courants.

* **Au-delà de 15 % d’écritures, recherche des changements sur le serveur de stockage toutes les 30 secondes** et **Au-delà de 15 % d’écritures, recherche des changements sur le serveur de stockage toutes les 60 secondes** : Ces options sont conçues pour les workflows dans lesquels vous souhaitez accélérer les lectures et les écritures, mais où il existe un risque qu’un autre utilisateur écrive directement dans le système de stockage back-end. Par exemple, si plusieurs groupes de clients travaillent sur les mêmes fichiers à partir de différents emplacements, ces modèles d’utilisation peuvent s’avérer utiles pour équilibrer la nécessité d’un accès rapide aux fichiers avec une faible tolérance au contenu obsolète à partir de la source.

* **Plus de 15 % d’écritures, mises à jour vers le serveur toutes les 30 secondes** : Cette option est conçue pour le scénario où plusieurs clients modifient activement les mêmes fichiers, ou si certains clients accèdent directement au stockage back-end au lieu de monter le cache.

  Les écritures fréquentes sur le back-end ont un impact sur les performances du cache. Vous devez donc envisager d’utiliser le modèle d’utilisation **Opérations d’écriture supérieures à 15 %** si le risque de conflit de fichiers est faible, par exemple si vous savez que différents clients travaillent dans des zones différentes du même ensemble de fichiers.

* **Lectures intensives, vérification du serveur de stockage toutes les 3 heures** : Cette option donne la priorité aux lectures rapides côté client, mais actualise aussi régulièrement les fichiers mis en cache à partir du système de stockage back-end, à la différence du modèle d’utilisation **Lectures intensives, écritures peu fréquentes**.

Ce tableau récapitule les différences entre les modèles d’utilisation :

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Si vous avez des questions sur le modèle d’utilisation le mieux adapté à votre workflow Azure HPC Cache, contactez votre représentant Azure ou ouvrez une demande de support pour obtenir de l’aide.

## <a name="next-steps"></a>Étapes suivantes

* [Ajouter des cibles de stockage](hpc-cache-add-storage.md) à Azure HPC Cache
