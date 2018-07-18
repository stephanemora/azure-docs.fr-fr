---
title: Grouper des machines pour l’évaluation avec Azure Migrate | Microsoft Docs
description: Explique comment grouper des machines avant d’exécuter une évaluation avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ccab88c0195a7ca459c8579b7870d121dfd0fe1d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231404"
---
# <a name="group-machines-for-assessment"></a>Grouper des machines pour l’évaluation

Cet article explique comment créer un groupe de machines pour l’évaluation par [Azure Migrate](migrate-overview.md). Azure Migrate évalue les machines du groupe pour vérifier si elles sont adaptées à la migration vers Azure, et fournit des estimations de dimensionnement et de coût d’exécution de chaque machine dans Azure.


## <a name="create-a-group"></a>Créer un groupe

1. Dans la **vue d’ensemble** du projet Azure Migrate, sous Gérer, cliquez sur  **Groupes** > **+ Groupe** et spécifiez un nom de groupe.
2. Ajoutez une ou plusieurs machines au groupe, puis cliquez sur  **Créer**. 
3. Vous pouvez éventuellement choisir d’exécuter une nouvelle évaluation pour le groupe. 

    ![Créer un groupe](./media/how-to-create-a-group/create-group.png)

Une fois le groupe créé, vous pouvez le modifier en le sélectionnant sur la page **Groupes** et en ajoutant ou en supprimant des machines.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser le [mappage de dépendances de machine](how-to-create-group-machine-dependencies.md) pour créer des groupes à haute fiabilité.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
