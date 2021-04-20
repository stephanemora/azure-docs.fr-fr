---
title: Scénarios relatifs à Azure HPC Cache
description: Permet de déterminer si votre travail informatique est compatible avec Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259902"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Votre travail est-il adapté à Azure HPC Cache ?

Azure HPC Cache peut accélérer l’accès aux données pour les travaux informatiques hautes performances dans diverses disciplines. Néanmoins, il n’est pas parfait pour tous les types de workflows. Cet article fournit des instructions pour déterminer si HPC Cache peut répondre à vos besoins.

L’article [Vue d’ensemble](hpc-cache-overview.md) fournit également un plan bref du moment où utiliser Azure HPC Cache et quelques exemples de cas d’usage.

Consultez également [cet article](nfs-blob-considerations.md) sur la façon d’utiliser efficacement le [stockage Blob monté sur NFS](../storage/blobs/network-file-system-protocol-support.md), qui est en préversion.

## <a name="nfs-version-30-applications"></a>Applications NFS version 3.0

Azure HPC Cache ne prend en charge que les clients NFS 3.0.

## <a name="high-read-to-write-ratio"></a>Taux élevé de lecture/écriture

Les charges de travail dans lesquelles les clients de calcul effectuent davantage de lectures que d’écriture sont généralement de bonnes candidates pour un cache. Par exemple, si votre taux de lecture/écriture est de 80/20 ou 70/30, Azure HPC Cache peut être utile en livrant les fichiers fréquemment demandés à partir du cache au lieu de les récupérer à partir du stockage distant.

La récupération d’un fichier et son stockage dans le cache pour la première fois ont une latence supplémentaire réduite par rapport à une demande client normale directement dans le stockage. L’efficacité augmente donc la prochaine fois qu’un client demande le même fichier. Ceci est particulièrement vrai pour les fichiers volumineux. Si chaque requête client est unique, l’impact de HPC Cache est limité. Toutefois, plus le fichier est volumineux, plus les performances s’améliorent au fil du temps après ce premier accès.

## <a name="file-based-analytic-workload"></a>Charge de travail analytique basée sur les fichiers

Azure HPC Cache est idéal pour un pipeline qui utilise des données basées sur des fichiers et qui s’exécute sur un grand nombre de clients de calcul, en particulier si les clients de calcul sont des machines virtuelles Azure. Cela peut aider à corriger des performances lentes ou incohérentes provoquées par de longs temps d’accès aux fichiers.

## <a name="remote-data-access"></a>Service RDA (Remote Data Access)

Azure HPC Cache peut aider à réduire la latence si votre charge de travail doit accéder à des données distantes qui ne peuvent pas être plus proches des ressources informatiques. Par exemple, vos enregistrements peuvent être à l’extrémité la plus éloignée d’un environnement WAN, dans une autre région Azure ou dans un centre de données client. (Ce scénario est parfois appelé « bursting de fichiers ».)

## <a name="heavy-request-load"></a>Charge de requête lourde

Si un grand nombre de clients demandent des données à la source en même temps, Azure HPC Cache peut accélérer l’accès aux fichiers. Par exemple, lorsqu’il est utilisé avec un cluster informatique hautes performances, Azure HPC Cache fournit une scalabilité pour un nombre élevé de requêtes simultanées via le cache.

## <a name="compute-resources-are-located-in-azure"></a>Les ressources de calcul se trouvent dans Azure

Les machines virtuelles Azure sont une réponse évolutive et rentable à la charge de travail informatique hautes performances. Azure HPC Cache peut vous aider à rapprocher les informations nécessaires, surtout si les données d’origine sont stockées sur un système distant.

Si un client souhaite exécuter son pipeline actuel « en l’état » sur des machines virtuelles Azure, Azure HPC Cache peut fournir une solution de stockage (ou de mise en cache) partagée basée sur POSIX à des fins de scalabilité.

En utilisant Azure HPC Cache, vous ne devez pas remanier l’architecture du pipeline de travail pour passer des appels natifs au stockage Blob Azure. Vous pouvez accéder à vos données sur le système d’origine ou utiliser HPC Cache pour les déplacer vers un nouveau conteneur d’objets Blob.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la planification et la configuration d’un cache dans les articles [Vue d’ensemble](hpc-cache-overview.md) et [Prérequis](hpc-cache-prerequisites.md)
* Lire les considérations relatives à l’utilisation du [stockage d’objets Blob compatible NFS](nfs-blob-considerations.md) (PRÉVERSION) avec Azure HPC Cache
