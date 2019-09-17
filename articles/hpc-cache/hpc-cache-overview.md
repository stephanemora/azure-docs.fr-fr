---
title: Présentation d’Azure HPC Cache
description: Cet article décrit Azure HPC Cache, un accélérateur d’accès aux fichiers pour le calcul haute performance.
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: d772f66d14e0c0ab5ac73603e8faeb7e31e9891f
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70843784"
---
# <a name="what-is-azure-hpc-cache"></a>Qu’est-ce qu’Azure HPC Cache ?

Azure HPC Cache accélère l’accès à vos données pour les tâches de calcul haute performance. En mettant les fichiers en cache dans Azure, la solution permet de tirer parti de la scalabilité du cloud computing même pour les workflows impliquant un stockage de données sur des liaisons WAN, par exemple dans l’environnement NAS (Network Attached Storage) de votre centre de données local.

Azure HPC Cache peut être lancé et supervisé facilement à partir du portail Azure. Le stockage NFS existant ou les nouveaux conteneurs d’objets blob peuvent être intégrés à son espace de noms agrégé, ce qui simplifie l’accès client même si vous modifiez la cible de stockage back-end.

## <a name="use-cases"></a>Cas d'utilisation

Azure HPC Cache améliore la productivité d’une diversité de workflows, notamment :

* Workflow nécessitant un grand nombre de lectures de fichier
* Données stockées dans un stockage accessible par NFS, un objet blob Azure ou les deux
* Batteries de serveurs contenant jusqu’à 75 000 cœurs d’UC

Azure HPC Cache peut être ajouté à une grande diversité de workflows, et ce, dans de nombreux secteurs. Ce service se révèle avantageux pour tout système dans lequel de nombreuses machines doivent accéder à un ensemble de fichiers à grande échelle et avec une faible latence. Les sections ci-dessous fournissent des exemples spécifiques.

### <a name="visual-effects-vfx-rendering"></a>Rendu d’effets visuels

Pour les médias et le divertissement, Azure HPC Cache peut accélérer l’accès aux données pour des projets de rendu prioritaires. Les workflows de rendu d’effets visuels nécessitent souvent un traitement de dernière minute impliquant de nombreux nœuds de calcul. Les données de ces workflows se trouvent généralement dans un environnement NAS local. Azure HPC Cache peut mettre en cache ces données de fichiers dans le cloud pour réduire la latence et améliorer la flexibilité du rendu à la demande.

### <a name="life-sciences"></a>Sciences de la vie

De nombreux workflows relatifs aux sciences de la vie peuvent tirer parti de la mise en cache de fichiers avec scale-out.

Un institut de recherche qui souhaite transférer ses workflows d’analyse génomique dans Azure peut les déplacer facilement à l’aide d’Azure HPC Cache. Étant donné que le cache fournit un accès aux fichiers POSIX, le workflow côté client existant peut être exécuté dans le cloud sans aucune modification.

Azure HPC Cache peut également être utilisé pour améliorer l’efficacité de tâches telles que l’analyse secondaire, la simulation pharmacologique ou l’analyse d’images pilotée par intelligence artificielle.

### <a name="financial-services-analytics"></a>Analyses des services financiers

Azure HPC Cache permet d’accélérer les calculs d’analyse quantitative, les charges de travail d’analyse des risques et les simulations de Monte-Carlo, permettant aux sociétés de services financiers de prendre des décisions stratégiques plus éclairées.

## <a name="region-availability"></a>Disponibilité des régions

Azure HPC Cache est disponible dans les régions Azure suivantes :

* USA Est
* USA Est 2
* Europe Nord
* Europe Ouest
* Asie Sud-Est
* USA Ouest 2

Consultez la [page produit Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) pour obtenir les informations de disponibilité les plus récentes.

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [page produit Azure HPC Cache](https://azure.microsoft.com/services/hpc-cache) pour en savoir plus sur les fonctionnalités de la solution.
* Apprenez-en plus sur les [prérequis](hpc-cache-prereqs.md) liés au produit.
* [Créez un cache Azure HPC Cache](hpc-cache-create.md) à partir du portail Azure.
