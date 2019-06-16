---
title: Grouper des machines pour l’évaluation avec Azure Migrate | Microsoft Docs
description: Explique comment grouper des machines avant d’exécuter une évaluation avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544912"
---
# <a name="group-machines-for-assessment"></a>Grouper des machines pour l’évaluation

Cet article explique comment créer un groupe de machines pour l’évaluation par [Azure Migrate](migrate-overview.md). Azure Migrate évalue les machines du groupe pour vérifier si elles sont adaptées à la migration vers Azure, et fournit des estimations de dimensionnement et de coût d’exécution de chaque machine dans Azure. Si vous connaissez les machines à migrer ensemble, vous pouvez créer manuellement le groupe dans Azure Migrate à l’aide de la méthode suivante. Si vous n’êtes pas certain des machines à regrouper, vous pouvez utiliser la fonctionnalité de visualisation des dépendances dans Azure Migrate pour créer des groupes. [En savoir plus.](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

## <a name="create-a-group"></a>Créer un groupe

1. Dans la **vue d’ensemble** du projet Azure Migrate, sous Gérer, cliquez sur  **Groupes** > **+ Groupe** et spécifiez un nom de groupe.
2. Ajoutez une ou plusieurs machines au groupe, puis cliquez sur  **Créer**.
3. Vous pouvez éventuellement choisir d’exécuter une nouvelle évaluation pour le groupe.

    ![Créer un groupe](./media/how-to-create-a-group/create-group.png)

Une fois le groupe créé, vous pouvez le modifier en le sélectionnant dans la page **Groupes** et en ajoutant ou en supprimant des machines.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser le [mappage de dépendances de machine](how-to-create-group-machine-dependencies.md) pour créer des groupes à haute fiabilité.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
