---
title: Présentation du rendu
description: Présentation de l’utilisation d’Azure pour le rendu et vue d’ensemble des fonctionnalités de rendu Azure Batch
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98232112"
---
# <a name="rendering-using-azure"></a>Rendu à l’aide d’Azure

Le rendu correspond au processus visant à récupérer des modèles 3D et à les convertir en images 2D. Les fichiers de scène 3D sont créés dans des applications telles qu’Autodesk 3ds Max, Autodesk Maya et Blender.  Les applications de rendu telles qu’Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray et Blender Cycles produisent des images 2D.  Des images sont parfois créées à partir de fichiers de scène. Toutefois, il est courant de modéliser et d’effectuer le rendu de plusieurs images, puis de les combiner dans une animation.

La charge de travail de rendu est principalement utilisée pour les effets spéciaux (VFX) dans l’industrie du multimédia et du divertissement. Le rendu est également utilisé dans de nombreux autres secteurs comme la publicité, la vente au détail, le pétrole et le gaz et la fabrication.

Le processus de rendu est très gourmand en ressources ; le nombre d’images à produire peut être très important et le rendu de chaque image peut prendre plusieurs heures.  Par conséquent, le rendu est une charge de travail de traitement par lots idéale qui peut tirer parti d’Azure pour exécuter plusieurs rendus en parallèle et utiliser un large éventail de matériel, y compris les GPU.

## <a name="why-use-azure-for-rendering"></a>Pourquoi utiliser Azure pour le rendu ?

Pour de nombreuses raisons, le rendu est une charge de travail parfaitement adaptée pour Azure :

* Les travaux de rendu peuvent être fractionnés en plusieurs éléments qui peuvent être exécutés en parallèle à l’aide de plusieurs machines virtuelles :
  * Les animations sont constituées de nombreuses images, et chaque image peut être rendue en parallèle.  Plus le nombre de machines virtuelles disponibles pour traiter que image est important, et plus la production de toutes les images et de l’animation sera rapide.
  * Certains logiciels de rendu permettent de diviser des images uniques en plusieurs éléments, comme des vignettes ou tranches.  Chaque élément peut être rendu séparément, puis combiné dans l’image finale lorsque tous les éléments sont terminés.  Plus le nombre de machines virtuelles disponibles est important, et plus le rendu d’une image sera rapide.
* Les projets de rendu peuvent nécessiter une mise à l’échelle importante :
  * Les images individuelles peuvent être complexes et leur rendu peut prendre plusieurs heures, même sur du matériel haut de gamme ; les animations peuvent contenir des centaines de milliers d’images.  Une quantité de calcul importante est nécessaire pour effectuer le rendu d’animations de haute qualité dans un délai raisonnable.  Dans certains cas, plus de 100 000 cœurs ont été utilisés pour effectuer le rendu de milliers d’images en parallèle.
* Les projets de rendu sont basés sur le projet et nécessitent différentes quantités de calcul :
  * Allouez de la capacité de calcul et de stockage si nécessaire, augmentez-la ou réduisez-la en fonction de la charge pendant le projet, puis supprimez-la lorsque le projet est terminé.
  * Payez la capacité lorsqu’elle est allouée, mais ne la payez pas lorsqu’il n’y a pas de charge, entre les projets par exemple.
  * Répondez aux pics d’activité dus à des modifications non prévues ; augmentez en cas de modifications inattendues et tardives dans un projet, ces modifications doivent être traitées dans un délai serré.
* Choisissez parmi un large choix de matériels en fonction de l’application, de la charge de travail et du délai d’exécution :
  * Un large choix de matériels est disponible dans Azure, qui peut être alloué et géré avec Batch.
  * En fonction du projet, la configuration requise peut être le meilleur rapport prix/performances ou les meilleures performances globales.  Différentes applications de scènes et/ou de rendu auront des exigences de mémoire différentes.  Certaines applications de rendu peuvent utiliser des GPU pour des performances optimales ou certaines fonctionnalités. 
* Les [machines virtuelles spot](https://azure.microsoft.com/pricing/spot/) ou de faible priorité réduisent les coûts :
  * Les machines virtuelles spot et de faible priorité sont disponibles à un prix bien moindre que les machines virtuelles standard et conviennent pour certains types de travaux.
  
## <a name="existing-on-premises-rendering-environment"></a>Environnement de rendu local existant

Le cas le plus courant est un groupe de rendus local existant géré par une application de gestion des rendus comme PipelineFX Qube, Render, Thinkbox Deadline ou une application personnalisée.  L’exigence consiste à étendre la capacité du groupe de rendus local à l’aide de machines virtuelles Azure.

L’infrastructure et les services Azure sont utilisés pour créer un environnement hybride dans lequel Azure est utilisé pour compléter la capacité locale. Par exemple :

* Utilisez un [réseau virtuel](../virtual-network/virtual-networks-overview.md) pour placer les ressources Azure sur le même réseau que le groupe de rendus local.
* Utilisez [Avere vFXT pour Azure](../avere-vfxt/avere-vfxt-overview.md) ou [Azure HPC Cache](../hpc-cache/hpc-cache-overview.md) pour mettre en cache les fichiers sources dans Azure afin de réduire l’utilisation de la bande passante et la latence, ce qui optimise les performances.
* Assurez-vous que le serveur de licences existant se trouve sur le réseau virtuel et achetez les licences supplémentaires requises pour satisfaire la capacité supplémentaire d’Azure.

## <a name="no-existing-render-farm"></a>Aucun groupe de rendus existant

Les stations de travail clientes peuvent effectuer le rendu, mais la charge de rendu augmente et prend trop de temps pour n’utiliser que la capacité de la station de travail.

Deux options principales sont disponibles :

* Déployez un gestionnaire de rendus local, tel que Royal Render, et configurez un environnement hybride pour utiliser Azure quand des capacités ou des performances supplémentaires sont requises. Un gestionnaire de rendus est spécifiquement conçu pour les charges de travail de rendu et inclura des plug-ins pour les applications clientes les plus courantes, permettant de soumettre facilement des travaux de rendu.

* Une solution personnalisée utilisant Azure Batch pour allouer et gérer la capacité de calcul ainsi que pour fournir la planification des travaux pour exécuter les travaux de rendu.

## <a name="next-steps"></a>Étapes suivantes

 Découvrez comment [utiliser l’infrastructure et les services Azure pour étendre un groupe de rendus local existant](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

En savoir plus sur les [capacités de rendu d’Azure Batch](batch-rendering-functionality.md).
