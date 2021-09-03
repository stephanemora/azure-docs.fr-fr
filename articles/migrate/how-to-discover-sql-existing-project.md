---
title: Détecter des instances SQL Server dans un projet Azure Migrate existant
description: Découvrez comment détecter des instances SQL Server dans un projet Azure Migrate existant.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2021
ms.openlocfilehash: 38169324211a22012426b895d66bc6d0015f5587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532237"
---
# <a name="discover-web-apps-and-sql-server-instances-in-an-existing-project"></a>Détecter les applications web et les instances SQL Server dans un projet existant

Cet article explique comment détecter les applications web et les instances et bases de données SQL Server dans un projet [Azure Migrate](./migrate-services-overview.md) qui a été créé avant la préversion de la fonctionnalité d’évaluation d’Azure SQL et/ou avant la préversion de la fonctionnalité d’évaluation Azure App Service.

La détection des applications web ASP.NET et des instances et bases de données SQL Server s’exécutant sur des machines locales permet d’identifier et d’adapter le chemin de migration vers Azure SQL. L’appliance Azure Migrate effectue cette détection à l’aide des informations d’identification de domaine (ou non) du système d’exploitation Windows ou des informations d’identification d’authentification SQL Server qui ont accès aux instances et bases de données SQL Server s’exécutant sur les serveurs cibles.
Ce processus de découverte est sans agent, c’est-à-dire que rien n’est installé sur les serveurs cibles.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous d’avoir :
    - Créé un [projet Azure Migrate](./create-manage-projects.md) avant l’annonce de la fonctionnalité d’évaluation des applications web et SQL pour votre région
    - Ajouté l’outil [Azure Migrate : détection et évaluation](./how-to-assess.md) à un projet
- Passez en revue [la prise en charge et les exigences de la découverte d’application](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Assurez-vous que PowerShell version 2.0 ou ultérieure est installé sur les serveurs sur lesquels vous exécutez la détection d’applications et que les outils VMware (versions ultérieures à 10.2.0) sont installés.
- Vérifiez la [configuration requise](./migrate-appliance.md) pour le déploiement de l’appliance Azure Migrate.
- Vérifiez que vous disposez des [rôles requis](./create-manage-projects.md#verify-permissions) dans l’abonnement pour créer des ressources.
- Assurez-vous que votre appliance a accès à Internet.

## <a name="enable-discovery-of-aspnet-web-apps-and-sql-server-instances-and-databases"></a>Activer la découverte des applications web ASP.NET et des instances de SQL Server et des bases de données

1. Dans votre projet Azure Migrate, vous pouvez soit
    - Sélectionner **Non activé** sur la vignette Hub ou :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Vignette Hub Azure Migrate avec détection des applications web et SQL non activée":::
    - Sélectionner **Non activé** sur l’une des entrées de la page de détection du serveur sous la colonne Instances SQL ou applications web :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Panneau des serveurs détectés Azure Migrate avec la détection SQL et des applications web non activée":::
2. Pour Détecter des applications web ASP.NET et des instances et bases de données SQL Server, procédez comme suit :
    - Sélectionnez **Mettre à niveau** pour créer la ressource requise.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Bouton de mise à niveau de l’appliance Azure Migrate":::
    - Vérifiez que les services en cours d’exécution sur l’appliance sont mis à jour avec les dernières versions. Pour ce faire, lancez le gestionnaire de configuration de l’appliance à partir du serveur de votre appliance, puis sélectionnez l’affichage des services de l’appliance dans le volet de configuration des prérequis.
        - L’appliance et ses composants sont mis à jour automatiquement :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Vérifier la version de l’appliance":::
    - Dans le volet Gérer les informations d’identification et les sources de détection du Gestionnaire de configuration de l’appliance, ajoutez des informations d’identification de domaine ou d’authentification SQL Server qui disposent d’un accès d’administrateur système sur l’instance SQL Server et les bases de données à détecter.
    - ASP.NET la découverte des applications web fonctionne avec les informations d’identification de système d’exploitation Windows de domaine et non-domaine tant que le compte utilisé dispose de privilèges d’administrateur local sur les serveurs.
    Vous pouvez tirer parti de la fonctionnalité de mappage automatique des informations d’identification de l’appliance, comme indiqué [ici](./tutorial-discover-vmware.md#start-continuous-discovery).

    Points à noter :
    - Assurez-vous que l’inventaire logiciel est déjà activé ou fournissez des informations d’identification de domaine ou hors domaine pour activer le même paramètre. L’inventaire logiciel doit être effectué pour détecter les instances SQL Server et applications web ASP.NET.
    - L’appliance va tenter de valider les informations d’identification de domaine avec Active Directory à mesure qu’elles sont ajoutées. Assurez-vous que le serveur de l’appliance dispose d’une ligne de mire sur le réseau sur le serveur Active Directory associé aux informations d’identification. Les informations d’identification sans domaine et les informations d’identification associées à l’authentification SQL Server ne sont pas validées.

3. Une fois les informations d’identification souhaitées ajoutées, sélectionnez Démarrer la détection pour lancer l’analyse.

> [!Note]
>Autorisez l’exécution de la découverte des applications web et SQL pendant un certain temps avant de créer des évaluations pour Azure App Service ou Azure SQL. Si la détection des applications web et instances et bases de données SQL Server n’est pas autorisée, les instances respectives sont marquées comme étant **inconnues** dans le rapport d’évaluation.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment exécuter une [Évaluation d’Azure SQL](./how-to-create-azure-sql-assessment.md)
- En savoir plus sur les [Évaluations Azure SQL](./concepts-azure-sql-assessment-calculation.md)
- Découvrez comment exécuter une [Évaluation d’Azure App Service](./how-to-create-azure-app-service-assessment.md)
- En savoir plus sur les [évaluations Azure App Service](./concepts-azure-webapps-assessment-calculation.md)