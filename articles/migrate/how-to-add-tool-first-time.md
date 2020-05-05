---
title: Ajouter un outil d’évaluation et de migration dans Azure Migrate
description: Décrit comment créer un projet Azure Migrate et ajouter un outil d’évaluation et de migration.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 48bdea31d17ea1ddf0b983af962dce30b22d8dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537727"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Ajouter un outil d’évaluation/de migration pour la première fois

Cet article explique comment ajouter un outil d’évaluation ou de migration à un projet de [Azure Migrate](migrate-overview.md) pour la première fois.  
Azure Migrate offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure d’applications et de charges de travail locales, ainsi que des machines virtuelles cloud privées/publiques. Le hub fournit des outils d’évaluation et de migration Azure Migrate, ainsi que d’autres outils et des [offres](migrate-services-overview.md#isv-integration) d’éditeurs de logiciels indépendants (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Créez un projet et ajouter un outil

Configurez un nouveau projet de Azure Migrate dans un abonnement Azure et ajoutez un outil.

- Un projet de Azure Migrate est utilisé pour stocker les métadonnées de découverte, d’évaluation et de migration collectées à partir de l’environnement que vous évaluez ou migrez. 
- Dans un projet, vous pouvez suivre les ressources découvertes et orchestrer l’évaluation et la migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.

    ![Configurer Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Dans **Vue d’ensemble**, cliquez sur **Évaluer et migrer des serveurs**.
4. Sous **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Dans **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Ajouter des outils**.
2. Dans **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.
3. Dans **Détails du projet**, spécifiez le nom du projet ainsi que la zone géographique où vous souhaitez le créer.  Passez en revue les zones géographiques prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et [gouvernementaux](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Créer un projet Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    - La zone géographique spécifiée pour le projet est utilisée uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales. Vous pouvez sélectionner n’importe quelle région cible pour la migration réelle.
    - Si vous devez déployer un projet dans une région spécifique d’une zone géographique, utilisez l’API suivante pour créer un projet. Spécifiez l’ID d’abonnement, le nom du groupe de ressources et le nom du projet, ainsi que l’emplacement. Passez en revue les zones géographiques ou les régions prises en charge pour les clouds [publics](migrate-support-matrix.md#supported-geographies-public-cloud) et [gouvernementaux](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Cliquez sur **Suivant**, puis ajoutez un outil d’évaluation ou de migration.

    > [!NOTE]
    > Lorsque vous créez un projet, vous devez ajouter au moins un outil d’évaluation ou de migration.

5. Dans **Sélectionner l’outil d’évaluation**, ajoutez un outil d’évaluation. Si vous n’avez pas besoin d’un outil d’évaluation, sélectionnez **Ignorer l’ajout d’un outil d’évaluation pour l’instant** > **Suivant**. 
2. Dans **Sélectionner l’outil de migration**, ajoutez un outil de migration en fonction des besoins. Si vous n’avez pas besoin d’un outil de migration pour le moment, sélectionnez **Ignorer l’ajout d’un outil de migration pour l’instant** > **Suivant**.
3. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres, puis cliquez sur **Ajouter des outils**.

Après avoir créé le projet, vous pouvez sélectionner des outils supplémentaires pour l’évaluation et la migration des serveurs, des charges de travail, des bases de données et des applications web.

## <a name="create-additional-projects"></a>Créer des projets supplémentaires

Dans certains cas, vous devrez peut-être créer des projets Azure Migrate supplémentaires. Par exemple, si vous avez des centres de données dans différentes zones géographiques, ou si vous avez besoin de stocker des métadonnées dans une autre zone géographique. Créez un projet supplémentaire comme suit :

1. Dans le projet Azure Migrate actif, cliquez sur **Serveurs** ou **Bases de données**.
2. Dans le coin supérieur droit, cliquez sur **Modifier** à côté nom du projet actuel.
3. Dans **Paramètres**, sélectionnez **Cliquez ici pour créer un nouveau projet**.
4. Créez un nouveau projet et ajoutez un nouvel outil comme décrit dans la procédure précédente.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des outils d’[évaluation](how-to-assess.md) et de [migration](how-to-migrate.md) supplémentaires. 
