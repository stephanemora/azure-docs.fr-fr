---
title: Ajouter des outils de migration dans Azure Migrate
description: Découvrez comment ajouter des outils de migration dans Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185963"
---
# <a name="add-migration-tools"></a>Ajouter des outils de migration

Cet article explique comment ajouter des outils de migration dans [Azure Migrate](migrate-overview.md).

Azure Migrate fournit un hub d’outils pour l’évaluation et la migration vers Azure. Il comprend des outils natifs, des outils fournis par d’autres services Azure et des offres tierces de fournisseurs de logiciels indépendants (ISV).

Si vous souhaitez ajouter un outil de migration et que vous n’avez pas encore configuré de projet Azure Migrate, suivez cet [article](how-to-add-tool-first-time.md).



## <a name="selecting-an-isv-tool"></a>Sélection d’un outil d’éditeur de logiciels indépendant

Si vous choisissez un [outil de ISV](migrate-services-overview.md#isv-integration) pour la migration, vous pouvez commencer par obtenir une licence ou vous inscrire à un essai gratuit, conformément à la stratégie de ISV. Dans chaque outil, il existe une option permettant de se connecter à Azure Migrate. Déployer l’outil et suivez les instructions et la documentation de l’outil pour connecter l’espace de travail de l’outil avec Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Sélectionnez un scénario de migration

1. Dans le projet Azure Migrate, cliquez sur **Vue d’ensemble**.
2. Sélectionnez le scénario de migration que vous souhaitez utiliser :

    - Pour migrer des machines et des charges de travail vers Azure, sélectionnez **Évaluer et migrer des serveurs**.
    - Pour migrer les machines SQL locales, sélectionnez **Évaluer et migrer des bases de données**.
    - Pour migrer les applications web locales, sélectionnez **Évaluer et migrer des applications web**.
    - Pour migrer de grandes quantités de données locales vers Azure en mode hors connexion, sélectionnez **Commander Data Box**.

    ![Scénario d’évaluation](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Sélectionnez un outil de migration de serveur

1. Cliquez sur **Évaluer et migrer des serveurs**.
2. Dans **Azure Migrate - Serveurs**, si vous n’avez pas encore ajouté d’outils de migration, sous **Outils de migration**, sélectionnez **Cliquez ici pour ajouter un outil de migration**. Si vous avez déjà ajouté des outils de migration dans **Ajouter plus d’outils de migration**, sélectionnez **Modifier**.

    > [!NOTE]
    > Si vous devez accéder à un autre projet, dans **Azure Migrate - Serveurs**, cliquez sur **Cliquer ici** qui se trouve à côté de **Consulter les détails pour un projet de migration différent**.

3. Dans **Azure Migrate**, sélectionnez l’outil de migration que vous souhaitez utiliser.
    - Si vous utilisez la migration de serveur Azure Migrate, vous pouvez configurer et exécuter des migrations directement dans le projet Azure Migrate.
    - Si vous utilisez un outil d’évaluation tiers, accédez au lien fourni pour l’ISV, puis exécutez la migration en suivant les instructions fournies.

## <a name="select-a-database-migration-tool"></a>Sélectionnez un outil de migration de base de données

1. Cliquez sur **Évaluer et migrer des bases de données**
2. Dans **Bases de données**,cliquez sur **Ajouter des outils**.
3. Dans Ajouter un outil > **Sélectionner un outil de migration**, sélectionnez l’outil que vous souhaitez utiliser pour migrer votre base de données.

## <a name="select-a-web-app-migration-tool"></a>Sélectionnez un outil de migration d’application web

1. Cliquez sur **Évaluer et migrer des applications web**.
2. Suivez le lien vers l’outil de migration pour le Azure App Service. Utiliser l’outil de migration pour :

    - **Évaluer les applications en ligne** : Vous pouvez évaluer et migrer des applications avec une URL publique en ligne, à l’aide de l’Assistant Migration de Azure App Service.
    - **.NET/PHP** : Pour les applications internes .NET et PHP, vous pouvez télécharger et exécuter l’Assistant Migration.

## <a name="order-an-azure-data-box"></a>Commander une solution Azure Data Box

Pour migrer de grandes quantités de données vers Azure, vous pouvez commander Azure Data Box pour le transfert de données hors connexion.

1. Cliquez sur **Commander Data Box**.
2. Dans **Sélectionner votre Azure Data Box**, spécifiez votre abonnement. 
3. Le transfert sera une importation dans Azure. Spécifiez la source de données et la destination de la région Azure pour les données.

## <a name="next-steps"></a>Étapes suivantes

Essayez une migration à l’aide de la migration de serveur Azure Migrate pour les machines virtuelles [Hyper-V](tutorial-migrate-hyper-v.md) ou [VMware](tutorial-migrate-vmware.md).
