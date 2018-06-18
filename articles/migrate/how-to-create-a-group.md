---
title: Grouper des machines pour l’évaluation avec Azure Migrate | Microsoft Docs
description: Explique comment grouper des machines avant d’exécuter une évaluation avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 5c5b5dc97c0faf43c9543422406c4fa1c30ed679
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34203697"
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
