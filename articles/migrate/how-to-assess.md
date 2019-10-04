---
title: Ajouter des outils d’évaluation dans Azure Migrate | Microsoft Docs
description: Décrit comment ajouter des outils d’évaluation dans le hub Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810172"
---
# <a name="add-assessment-tools"></a>Ajouter des outils d’évaluation

Cet article explique comment ajouter des outils d’évaluation dans [Azure Migrate](migrate-overview.md).

Azure Migrate fournit un hub d’outils pour l’évaluation et la migration vers Azure. Il comprend des outils natifs, des outils fournis par d’autres services Azure et des offres tierces de fournisseurs de logiciels indépendants (ISV).

Si vous souhaitez ajouter un outil d’évaluation et que vous n’avez pas encore de projet Azure Migrate, suivez cet [article](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Sélection d’un outil de ISV

Si vous choisissez un [outil de ISV](migrate-services-overview.md#isv-integration) pour l’évaluation, vous pouvez commencer par obtenir une licence ou vous inscrire à un essai gratuit, conformément à la stratégie de ISV. Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Suivez les instructions et la documentation de l’outil pour connecter l’espace de travail de l’outil avec Azure Migrate. 


## <a name="select-an-assessment-scenario"></a>Sélectionnez un scénario d’évaluation

1. Dans le projet Azure Migrate, cliquez sur **Vue d’ensemble**.
2. Sélectionnez le scénario d’évaluation que vous souhaitez utiliser :

    - Pour découvrir et évaluer les machines et les charges de travail pour la migration vers Azure, sélectionnez **Évaluer et migrer des serveurs**.
    - Pour évaluer les machines SQL locales, sélectionnez **Évaluer et migrer des bases de données**.
    - Pour évaluer les applications web locales, sélectionnez **Évaluer et migrer des applications web**.

    ![Scénario d’évaluation](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Sélectionnez un outil d’évaluation de serveur 

1. Cliquez sur **Évaluer et migrer des serveurs**.
2. Dans **Azure Migrate - Serveurs**, si vous n’avez pas ajouté d’outil d’évaluation, sous **Outils d’évaluation**, sélectionnez **Cliquez ici pour ajouter un outil d’évaluation**. Si vous avez déjà ajouté des outils d’évaluation dans **Ajouter plus d’outils d’évaluation**, sélectionnez **Modifier**.

    > [!NOTE]
    > Si vous devez accéder à un autre projet, dans **Azure Migrate - Serveurs**, cliquez sur **Cliquer ici** qui se trouve à côté de **Consulter les détails pour un projet de migration différent**.

3. Dans **Azure Migrate**, sélectionnez l’outil d’évaluation que vous souhaitez utiliser.

    
    ![Outils d’évaluation](./media/how-to-assess/assess-tool.png)

    - Si vous utilisez Azure Migrate Server Assessment, vous pouvez configurer, exécuter et afficher les évaluations directement dans le projet Azure Migrate.
    - Si vous utilisez un outil d’évaluation tiers, accédez au lien fourni pour le site de l’outil et exécutez l’évaluation conformément aux instructions fournies.


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

Essayez une évaluation avec Azure Migrate Server Assessment pour les machines virtuelles [Hyper-V](tutorial-prepare-hyper-v.md) ou [VMware](tutorial-prepare-vmware.md).
