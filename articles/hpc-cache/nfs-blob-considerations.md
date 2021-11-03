---
title: Utiliser le stockage d’objets blob NFS avec Azure HPC Cache
description: Décrit les procédures et les limitations liées à l’utilisation du stockage d’objets blob ADLS-NFS avec Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: femila
ms.openlocfilehash: 297030bc35e9fcf726201695b273eac79a044028
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051037"
---
# <a name="use-nfs-mounted-blob-storage-with-azure-hpc-cache"></a>Utiliser le stockage Blob monté sur NFS avec Azure HPC Cache

Vous pouvez utiliser des conteneurs d’objets blob montés sur NFS avec Azure HPC Cache. Pour en savoir plus sur la [prise en charge du protocole NFS 3.0 dans le stockage d’objets blob Azure](../storage/blobs/network-file-system-protocol-support.md), consultez le site de documentation du stockage d’objets blob.

Azure HPC Cache utilise le stockage d’objets blob compatibles NFS dans son type de cible de stockage ADLS-NFS. Ces cibles de stockage sont similaires aux cibles de stockage NFS standard, mais présentent également des chevauchements avec les cibles d’objets blob Azure standard.

Cet article explique les stratégies et les limitations que vous devez connaître lorsque vous utilisez des cibles de stockage ADLS-NFS.

Il vous est également conseillé de lire la documentation de l’objet blob NFS, en particulier les sections qui décrivent les scénarios compatibles et incompatibles :

* [Présentation de fonctionnalités](../storage/blobs/network-file-system-protocol-support.md)
* [Considérations relatives aux performances](../storage/blobs/network-file-system-protocol-support-performance.md)
* [Problèmes connus et limitations](../storage/blobs/network-file-system-protocol-known-issues.md)

## <a name="understand-consistency-requirements"></a>Comprendre les exigences de cohérence

HPC Cache requiert une cohérence forte pour les cibles de stockage ADLS-NFS. Par défaut, le stockage d’objets blob compatibles NFS ne met pas strictement à jour les métadonnées de fichier, ce qui empêche HPC Cache de comparer les versions de fichiers avec précision.

Pour contourner cette différence, Azure HPC Cache désactive automatiquement la mise en cache des attributs NFS sur n’importe quel conteneur d’objets blob compatibles NFS utilisé comme cible de stockage.

Ce paramètre persiste pendant la durée de vie du conteneur, même si vous le supprimez du cache.

## <a name="pre-load-data-with-nfs-protocol"></a>Précharger des données avec le protocole NFS
<!-- cross-referenced from hpc-cache-ingest.md and here -->

Sur un conteneur d’objets blob compatibles NFS, *un fichier ne peut être modifié que par le protocole utilisé lors de sa création*. Autrement dit, si vous utilisez l’API REST Azure pour remplir un conteneur, vous ne pouvez pas utiliser NFS pour mettre à jour ces fichiers. Étant donné qu’Azure HPC Cache utilise uniquement NFS, il ne peut pas modifier les fichiers qui ont été créés avec l’API REST Azure. (En savoir plus sur les [problèmes connus avec les API du stockage d’objets blob](../storage/blobs/data-lake-storage-known-issues.md#blob-storage-apis))

Ce n’est pas un problème pour le cache si votre conteneur est vide ou si les fichiers ont été créés à l’aide de NFS.

Si les fichiers de votre conteneur ont été créés avec l’API REST d’Azure Blob au lieu de NFS, le Azure HPC Cache est limité à ces actions sur les fichiers d’origine :

* Répertorier les fichiers dans un répertoire.
* Lire les fichiers (et les conserver dans le cache pour les lectures suivantes).
* Supprimer les fichiers.
* Vider les fichiers (les tronquer à 0).
* Enregistrer une copie des fichiers. La copie est marquée comme un fichier créé par NFS et peut être modifiée à l’aide de NFS.

**Azure HPC Cache ne peut pas modifier le contenu d’un fichier qui a été créé avec REST.** Cela signifie que le cache ne peut pas enregistrer un fichier modifié à partir d’un client vers la cible de stockage.

Il est important de comprendre cette limitation, car cela peut entraîner des problèmes d’intégrité des données si vous utilisez des modèles d’utilisation de la mise en cache en lecture/écriture sur des fichiers qui n’ont pas été créés avec NFS.

> [!TIP]
> Vous pouvez en apprendre davantage sur la mise en cache en lecture et en écriture dans [Comprendre les modèles d’utilisation du cache](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Scénarios de mise en cache d’écriture

Ces modèles d’utilisation du cache incluent la mise en cache d’écriture :

* **Opérations d’écriture supérieures à 15 %**
* **Au-delà de 15 % d’écritures, recherche des changements sur le serveur de stockage toutes les 30 secondes**
* **Au-delà de 15 % d’écritures, recherche des changements sur le serveur de stockage toutes les 60 secondes**
* **Plus de 15 % d’écritures, réécrire sur le serveur toutes les 30 secondes**

Les modèles d’utilisation de la mise en cache d’écriture ne doivent être utilisés que sur les fichiers qui ont été créés avec NFS.

Si vous essayez d’utiliser le cache d’écriture sur les fichiers créés par REST, vous risquez de perdre vos modifications de fichiers. Cela est dû au fait que le cache n’essaie pas d’enregistrer immédiatement les modifications apportées aux fichiers dans le conteneur de stockage.

Voici comment la tentative de mise en cache des écritures dans des fichiers créés avec REST met en péril les données :

1. Le cache accepte les modifications des clients et renvoie un message de réussite à chaque modification.
1. Le cache conserve le fichier modifié dans son stockage et attend des modifications supplémentaires.
1. Après un certain temps, le cache tente d’enregistrer le fichier modifié dans le conteneur principal. À ce stade, un message d’erreur s’affiche, car il tente d’écrire dans un fichier REST avec NFS.

   Il est trop tard pour indiquer à l’ordinateur client que ses modifications n’ont pas été acceptées, et le cache n’a aucun moyen de mettre à jour le fichier d’origine. Les modifications des clients seront donc perdues.

### <a name="read-caching-scenarios"></a>Scénarios de mise en cache de lecture

Les scénarios de mise en cache de lecture sont appropriés pour les fichiers créés avec l’API REST NFS ou Azure Blob.

Ces modèles d’utilisation utilisent uniquement la mise en cache de lecture :

* **Lire les écritures lourdes et peu fréquentes**
* **Les clients écrivent dans la cible NFS en ignorant le cache**
* **Lectures intensives, vérification du serveur de stockage toutes les 3 heures**

Vous pouvez utiliser ces modèles d’utilisation avec les fichiers créés par l’API REST ou par NFS. Toutes les écritures NFS envoyées d’un client vers le conteneur principal échouent toujours, mais elles échouent immédiatement et retournent un message d’erreur au client.

Un workflow de mise en cache de lecture peut toujours impliquer des modifications de fichiers, à condition qu’elles ne soient pas mises en cache. Par exemple, les clients peuvent accéder à des fichiers à partir du conteneur, mais les réécrire sous la forme d’un nouveau fichier, ou ils peuvent enregistrer des fichiers modifiés à un autre emplacement.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Reconnaître les limitations du gestionnaire de verrous réseau (NLM)

Les conteneurs d’objets blob compatibles NFS ne prennent pas en charge le gestionnaire de verrous réseau (NLM), un protocole NFS couramment utilisé pour protéger les fichiers contre les conflits.

Si votre workflow NFS a été initialement écrit pour des systèmes de stockage matériel, vos applications clientes peuvent inclure des requêtes NLM. Pour contourner cette limitation lors du déplacement de votre processus vers un stockage d’objets blob compatibles NFS, assurez-vous que vos clients désactivent NLM lorsqu’ils montent le cache.

Pour désactiver NLM, utilisez l’option ``-o nolock`` dans la commande ``mount`` de vos clients. Cette option empêche les clients de demander des verrous NLM et de recevoir des erreurs en réponse. L’option ``nolock`` est implémentée différemment dans différents systèmes d’exploitation. Pour plus d’informations, consultez la documentation de votre système d’exploitation client (man 5 nfs).

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Simplifier les écritures dans les conteneurs compatibles NFS avec HPC Cache

Azure HPC Cache peut contribuer à améliorer les performances dans une charge de travail qui comprend l’écriture de modifications dans une cible de stockage ADLS-NFS.

> [!NOTE]
> Vous devez utiliser NFS pour remplir votre conteneur de stockage ADLS-NFS si vous souhaitez modifier ses fichiers par le biais d’Azure HPC Cache.

L’une des limitations présentées dans l’[article Considérations relatives aux performances](../storage/blobs/network-file-system-protocol-support-performance.md) des objets blob compatibles NFS est que le stockage ADLS-NFS n’est pas très efficace lors du remplacement des fichiers existants. Si vous utilisez Azure HPC Cache avec le stockage blob monté sur NFS, le cache gère les réécritures intermittentes à mesure que les clients modifient un fichier actif. La latence liée à l’écriture d’un fichier dans le conteneur principal est masquée pour les clients.

Gardez à l’esprit les limitations décrites ci-dessus dans [Précharger des données avec le protocole NFS](#pre-load-data-with-nfs-protocol).

## <a name="next-steps"></a>Étapes suivantes

* En apprendre davantage sur les [conditions préalables de cible de stockage ADLS-NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
* Créer un [compte de stockage d’objets blob compatibles NFS](../storage/blobs/network-file-system-protocol-support-how-to.md)
