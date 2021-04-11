---
title: Ajouter des outils d’évaluation dans Azure Migrate
description: Découvrez comment ajouter des outils d’évaluation dans Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 37f3748b4f0f3db47bbd6fbe9bc06a307781c2f8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786802"
---
# <a name="add-assessment-tools"></a>Ajouter des outils d’évaluation

Cet article explique comment ajouter des outils d’évaluation dans [Azure Migrate](./migrate-services-overview.md). 

- Si vous souhaitez ajouter un outil d’évaluation et que vous n’avez pas encore de projet Azure Migrate, suivez cet [article](create-manage-projects.md).
- Si vous avez ajouté un outil ISV ou Movere, pour l’évaluation, [suivez les étapes](prepare-isv-movere.md) de préparation à l’utilisation de l’outil.

## <a name="select-an-assessment-scenario"></a>Sélectionnez un scénario d’évaluation

1. Dans le projet Azure Migrate, cliquez sur **Vue d’ensemble**.
2. Sélectionnez le scénario d’évaluation :

    - Découvrez, évaluez et migrez des serveurs (physiques ou virtuels) à partir de votre centre de données ou d’autres clouds vers Azure. Sélectionnez **Découvrir, évaluer et migrer**. Vous pouvez désormais également découvrir et évaluer SQL Server à partir de votre environnement VMware à l’aide de cet objectif de migration.
    - Pour évaluer les bases de données SQL Server locales, sélectionnez **Évaluer et migrer des bases de données**.
    - Pour évaluer ou migrer des applications web locales, sélectionnez **Explorer davantage** > **Web Apps**.
    - Pour évaluer votre infrastructure de bureau virtuel, sélectionnez **Explorer davantage** > **Virtual Desktop Infrastructure**.

    ![Options de sélection d’un scénario d’évaluation](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-discovery-and-assessment-tool"></a>Sélectionnez un outil de découverte et d’évaluation 


1. Ajoutez un outil :

    - Si vous avez créé un projet Azure Migrate à l’aide de l’option **Évaluer et migrer des serveurs** du portail, l’outil de découverte et d’évaluation d’Azure Migrate est automatiquement ajouté au projet. Pour ajouter des outils d’évaluation, dans **Windows, Linux et SQL Server**, en regard d’**Outils d’évaluation**, sélectionnez **Ajouter d’autres outils**.

         ![Bouton permettant d’ajouter d’autres outils d’évaluation](./media/how-to-assess/add-assessment-tool.png)

    - Si vous avez utilisé une autre option pour créer un projet et que vous n’avez pas encore d’outils d’évaluation, dans **Windows, Linux et SQL Server** > **Outils d’évaluation**, sélectionnez **Cliquez ici**.

        ![Bouton permettant d’ajouter le premier outil d’évaluation](./media/how-to-assess/no-assessment-tool.png)

2. Dans **Azure Migrate** > **Ajouter des outils**, sélectionnez les outils que vous voulez ajouter. Sélectionnez ensuite **Ajouter un outil**.

    ![Sélectionner des outils d’évaluation dans la liste](./media/how-to-assess/select-assessment-tool.png)



## <a name="select-a-database-assessment-tool"></a>Sélectionnez un outil d’évaluation de base de données

1. Ajoutez un outil :

    - Si vous avez créé un projet Azure Migrate à l’aide de l’option **Évaluer et migrer des bases de données** du portail, l’outil Évaluation de base de données est automatiquement ajouté au projet. Pour ajouter des outils d’évaluation supplémentaires, dans **Bases de données**, en regard d’**Outils d’évaluation**, sélectionnez **Ajouter d’autres outils**.

    - Si vous avez utilisé une autre option pour créer un projet et que vous n’avez pas encore d’outils d’évaluation de base de données, dans **Bases de données** > **Outils d’évaluation**, sélectionnez **Cliquez ici**.

2. Dans **Azure Migrate** > **Ajouter des outils**, sélectionnez les outils que vous voulez ajouter. Sélectionnez ensuite **Ajouter un outil**.

    ![Sélectionner des outils d’évaluation de base de données dans la liste](./media/how-to-assess/select-database-assessment-tool.png)


## <a name="select-a-web-app-assessment-tool"></a>Sélectionner un outil d’évaluation des applications web

Si vous avez créé un projet Azure Migrate à l’aide de l’option **Explorer davantage** > **Applications web** du portail, l’outil d’évaluation des applications web est automatiquement ajouté au projet. 


1. Si l’outil d’évaluation des applications web ne se trouve pas dans le projet, dans **Web Apps** > **Outils d’évaluation**, sélectionnez **Cliquez ici**.
    
    ![Ajouter des outils d’évaluation des applications web](./media/how-to-assess/no-web-app-assessment-tool.png)


2. Dans **Azure Migrate** > **Ajouter des outils**, sélectionnez l’outil d’évaluation des applications web. Sélectionnez ensuite **Ajouter un outil**.

    ![Sélectionner l’outil de migration de base de données dans la liste](./media/how-to-assess/select-web-app-assessment-tool.png)

 


## <a name="next-steps"></a>Étapes suivantes

Découvrir les serveurs locaux pour l’évaluation avec l’outil de découverte et d’évaluation d’Azure Migrate pour [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md) ou des [serveurs physiques](./tutorial-discover-physical.md)