---
title: Ajouter des outils d’évaluation dans Azure Migrate
description: Découvrez comment ajouter des outils d’évaluation dans Azure Migrate.
ms.topic: how-to
ms.date: 04/26/2020
ms.openlocfilehash: 5151135a57b7c2780485d85299ead5df83ea851e
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86122113"
---
# <a name="add-assessment-tools"></a>Ajouter des outils d’évaluation

Cet article explique comment ajouter des outils d’évaluation dans [Azure Migrate](./migrate-services-overview.md). 

- Si vous souhaitez ajouter un outil d’évaluation et que vous n’avez pas encore de projet Azure Migrate, suivez cet [article](how-to-add-tool-first-time.md).
- Si vous avez ajouté un outil ISV ou Movere, pour l’évaluation, [suivez les étapes](prepare-isv-movere.md) de préparation à l’utilisation de l’outil.

## <a name="select-an-assessment-scenario"></a>Sélectionnez un scénario d’évaluation

1. Dans le projet Azure Migrate, cliquez sur **Vue d’ensemble**.
2. Sélectionnez le scénario d’évaluation que vous souhaitez utiliser :

    - Pour découvrir et évaluer les machines et les charges de travail pour la migration vers Azure, sélectionnez **Évaluer et migrer des serveurs**.
    - Pour évaluer les bases de données SQL Server locales, sélectionnez **Évaluer et migrer des bases de données**.
    - Pour évaluer les applications web locales, sélectionnez **Évaluer et migrer des applications web**.

    ![Scénario d’évaluation](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Sélectionnez un outil d’évaluation de serveur 

1. Cliquez sur **Évaluer et migrer des serveurs**.
2. Dans **Azure Migrate - Serveurs**, si vous n’avez pas ajouté d’outil d’évaluation, sous **Outils d’évaluation**, sélectionnez **Cliquez ici pour ajouter un outil d’évaluation**. Si vous avez déjà ajouté des outils d’évaluation dans **Ajouter plus d’outils d’évaluation**, sélectionnez **Modifier**.

    > [!NOTE]
    > Si vous devez accéder à un autre projet, dans **Azure Migrate - Serveurs**, cliquez sur **Cliquer ici** qui se trouve à côté de **Consulter les détails pour un projet de migration différent**.

3. Dans **Azure Migrate**, sélectionnez l’outil d’évaluation que vous souhaitez utiliser.

    - Si vous utilisez Azure Migrate Server Assessment, vous pouvez configurer, exécuter et afficher les évaluations directement dans le projet Azure Migrate.
    - Si vous utilisez un outil d’évaluation différent, accédez au lien fourni pour son site et exécutez l’évaluation conformément aux instructions fournies.


## <a name="select-a-database-assessment-tool"></a>Sélectionnez un outil d’évaluation de base de données

1. Cliquez sur **Évaluer et migrer des bases de données**
2. Dans **Bases de données**, cliquez sur **Ajouter des outils**.
3. Dans Ajouter un outil > **Sélectionner un outil d’évaluation**, sélectionnez l’outil que vous souhaitez utiliser pour évaluer votre base de données.

## <a name="select-a-web-app-assessment-tool"></a>Sélectionner un outil d’évaluation des applications web

1. Cliquez sur **Évaluer et migrer des applications web**.
2. Suivez le lien vers l’outil de migration pour le Azure App Service. Utiliser l’outil de migration pour :

    - **Évaluer les applications en ligne** : Vous pouvez évaluer des applications avec une URL publique en ligne, à l’aide de l’Assistant Migration de Azure App Service.
    - **.NET/PHP** : Pour les applications internes .NET et PHP, vous pouvez télécharger et exécuter l’Assistant Migration.



## <a name="next-steps"></a>Étapes suivantes

Essayez une évaluation avec Azure Migrate Server Assessment pour les machines virtuelles [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md) ou les [serveur physiques](tutorial-prepare-physical.md).
