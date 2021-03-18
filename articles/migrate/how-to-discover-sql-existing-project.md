---
title: Détecter des instances SQL Server dans un projet Azure Migrate existant
description: Découvrez comment détecter des instances SQL Server dans un projet Azure Migrate existant.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: ca0052eebd8d3c8e80943ca8c0e0346216436800
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452762"
---
# <a name="discover-sql-server-instances-in-an-existing-project"></a>Détecter les instances SQL Server dans un projet existant 

Cet article explique comment détecter les instances et bases de données SQL Server dans un projet [Azure Migrate](./migrate-services-overview.md) qui a été créé avant la préversion de la fonctionnalité d’évaluation d’Azure SQL.

La détection des instances et bases de données SQL Server s’exécutant sur des machines locales permet d’identifier et d’adapter le chemin de migration vers Azure SQL. L’appliance Azure Migrate effectue cette détection à l’aide des informations d’identification de domaine ou des informations d’identification d’authentification SQL Server qui ont accès aux instances et bases de données SQL Server s’exécutant sur les serveurs cibles. Ce processus de détection est sans agent, c’est-à-dire que rien n’est installé sur les serveurs cibles.

> [!Note]
> La détection et l’évaluation des instances et bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet dans la région Australie Est et que vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué les [**prérequis**](how-to-discover-sql-existing-project.md) indiqués dans cet article.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous d’avoir : 
    - Créé un [projet Azure Migrate](./create-manage-projects.md) avant l’annonce de la fonctionnalité d’évaluation SQL pour votre région
    - Ajouté l’outil [Azure Migrate : détection et évaluation](./how-to-assess.md) à un projet
- Passez en revue [la prise en charge et les exigences de la découverte d’application](./migrate-support-matrix-vmware.md#vmware-requirements).
-  Assurez-vous que PowerShell version 2.0 ou ultérieure est installé sur les serveurs sur lesquels vous exécutez la détection d’applications et que les outils VMware (versions ultérieures à 10.2.0) sont installés.
- Vérifiez la [configuration requise](./migrate-appliance.md) pour le déploiement de l’appliance Azure Migrate.
- Vérifiez que vous disposez des [rôles requis](./create-manage-projects.md#verify-permissions) dans l’abonnement pour créer des ressources.
- S’assurer que l’appliance a accès à Internet

## <a name="enable-discovery-of-sql-server-instances-and-databases"></a>Activer la détection des instances et bases de données SQL Server

1. Dans votre projet Azure Migrate, vous pouvez soit :
    - Sélectionner **Non activé** sur la vignette Hub ou   :::image type="content" source="./media/how-to-discover-sql-existing-project/hub-not-enabled.png" alt-text="Vignette Hub Azure Migrate avec détection SQL non activée":::
    - Sélectionner **Non activé** sur l’une des entrées de la page de détection du serveur sous la colonne Instances SQL   :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-not-enabled.png" alt-text="Panneau des serveurs détectés Azure Migrate avec la détection SQL non activée":::
2. Dans Détecter des instances et bases de données SQL Server, procédez comme suit :
    - Sélectionnez **Mettre à niveau** pour créer la ressource requise.
        :::image type="content" source="./media/how-to-discover-sql-existing-project/discovery-upgrade-appliance.png" alt-text="Bouton de mise à niveau de l’appliance Azure Migrate":::
    - Vérifiez que les services en cours d’exécution sur l’appliance sont mis à jour avec les dernières versions. Pour ce faire, lancez le gestionnaire de configuration de l’appliance à partir du serveur de votre appliance, puis sélectionnez l’affichage des services de l’appliance dans le volet de configuration des prérequis.
        - L’appliance et ses composants sont mis à jour automatiquement :::image type="content" source="./media/how-to-discover-sql-existing-project/appliance-services-version.png" alt-text="Vérifier la version de l’appliance":::
    - Dans le volet Gérer les informations d’identification et les sources de détection du Gestionnaire de configuration de l’appliance, ajoutez des informations d’identification de domaine ou d’authentification SQL Server qui disposent d’un accès d’administrateur système sur l’instance SQL Server et les bases de données à détecter. 
    Vous pouvez tirer parti de la fonctionnalité de mappage d’informations d’identification automatique de l’appliance ou mapper manuellement les informations d’identification sur le serveur respectif comme indiqué [ici](/azure/migrate/tutorial-discover-vmware#start-continuous-discovery).
        
    Points à noter :
    - Assurez-vous que l’inventaire logiciel est déjà activé ou fournissez des informations d’identification de domaine ou hors domaine pour activer le même paramètre. L’inventaire logiciel doit être effectué pour détecter les instances SQL Server.
    - L’appliance va tenter de valider les informations d’identification de domaine avec Active Directory à mesure qu’elles sont ajoutées. Vérifiez que le serveur de l’appliance dispose d’une ligne de mire sur le réseau sur le serveur Active Directory associé aux informations d’identification. Les informations d’identification associées à l’authentification SQL Server ne sont pas validées. 

3. Une fois que les informations d’identification souhaitées ont été ajoutées, sélectionnez Démarrer la détection pour lancer l’analyse.

> [!Note] 
>Autorisez l’exécution de la détection SQL pendant un certain temps avant de créer des évaluations pour Azure SQL. Si la détection des instances et bases de données SQL Server n’est pas autorisée, les instances respectives sont marquées comme étant **inconnues** dans le rapport d’évaluation.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment exécuter une [Évaluation d’Azure SQL](./how-to-create-azure-sql-assessment.md)
- En savoir plus sur les [Évaluations Azure SQL](./concepts-azure-sql-assessment-calculation.md)