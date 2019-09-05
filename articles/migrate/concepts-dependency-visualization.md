---
title: Visualisation des dépendances dans Azure Migrate | Microsoft Docs
description: Fournit une vue d’ensemble des calculs d’évaluation dans le service d’évaluation de serveur de Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: e7b69e57d4e01c292a2c6d032ba995d1936b88dc
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142900"
---
# <a name="dependency-visualization"></a>Visualisation de dépendance

Azure Migrate : L’évaluation de serveur évalue les groupes de machines locales pour la migration vers Azure. Vous pouvez utiliser la fonctionnalité de visualisation des dépendances dans l’évaluation du serveur pour créer des groupes. Cet article fournit des informations sur cette fonctionnalité.

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

## <a name="overview"></a>Vue d'ensemble

La visualisation des dépendances dans l’évaluation du serveur vous permet de créer des groupes de confiance élevée pour les évaluations de la migration. À l’aide de la visualisation des dépendances, vous pouvez voir les dépendances de réseau des machines et identifier les machines connexes qui doivent être migrées ensemble vers Azure. Cette fonctionnalité est utile dans les scénarios où vous ne savez pas exactement quelles sont les machines qui constituent votre application et doivent être migrées ensemble vers Azure.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](how-to-add-tool-first-time.md) un projet Azure Migrate.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) Azure Migrate : Server Assessment.
- Assurez-vous d'avoir découvert vos machines dans Azure Migrate ; vous pouvez le faire en configurant une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Azure Migrate utilise la solution [Service Map](../operations-management-suite/operations-management-suite-service-map.md) dans [Journaux d’activité Azure Monitor](../log-analytics/log-analytics-overview.md) pour la visualisation des dépendances.
- Pour tirer parti de la visualisation des dépendances, vous pouvez associer un espace de travail Log Analytics, nouveau ou existant, à un projet Azure Migrate.
- Vous ne pouvez créer ou attacher un espace de travail que dans l’abonnement où le projet Azure Migrate est créé.
- Pour attacher un espace de travail Log Analytics à un projet :
    1. Sous l’onglet **Serveurs**, dans la vignette **Azure Migrate : mosaïque Server Assessment**, cliquez sur **Vue d’ensemble**.
    2. Dans **Vue d’ensemble**, cliquez sur la flèche vers le bas pour développer **Éléments principaux**.
    3. Dans **Espace de travail OMS**, cliquez sur **Configuration obligatoire**.
    4. Dans **Configurer l’espace de travail**, spécifiez si vous voulez créer un espace de travail ou utiliser un espace existant :
    
    ![Ajouter un espace de travail](./media/how-to-create-group-machine-dependencies/workspace.png)

- Lors de l’association d’un espace de travail, vous aurez la possibilité de créer un nouvel espace de travail ou de joindre un espace existant :
  - Quand vous créez un espace de travail, vous devez spécifier un nom pour celui-ci. L’espace de travail est ensuite créé dans une région appartenant à la même [zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que le projet de migration.
  - Quand vous attachez un espace de travail existant, vous pouvez choisir parmi tous les espaces de travail disponibles dans le même abonnement que le projet de migration. Notez que seuls les espaces de travail qui ont été créés dans une région où [Service Map est pris en charge](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites) sont listés. Afin de joindre un espace de travail, assurez-vous d’avoir accès en lecture à l’espace de travail.

  > [!NOTE]
  > Une fois que vous avez joint un espace de travail à un projet, vous ne pouvez plus le modifier.

- L’espace de travail associé est étiqueté avec la clé **Projet de migration** et la valeur **Nom du projet**, que vous pouvez utiliser pour effectuer une recherche dans le portail Azure.
- Pour accéder à l’espace de travail associé au projet, vous pouvez accéder à la section **Bases** de la page **Vue d’ensemble** du projet.

    ![Accéder à l’espace de travail Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Pour utiliser la visualisation des dépendances, vous devez télécharger et installer des agents sur chaque machine locale que vous souhaitez analyser.  

- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) doit être installé sur chaque machine. [En savoir plus](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) sur l’installation de l’agent MMA.
- Le programme [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) doit être installé sur chaque machine. [En savoir plus](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) sur l’installation de l’agent de dépendances.
- En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

Vous n’avez pas besoin ces agents sur les machines que vous souhaitez évaluer, sauf si vous utilisez la visualisation des dépendances.

## <a name="do-i-need-to-pay-for-it"></a>Est-ce une fonctionnalité payante ?

La fonctionnalité de visualisation des dépendances est disponible sans frais supplémentaires. L’utilisation de la fonctionnalité de visualisation des dépendances dans Server Assessment nécessite Service Map et suppose que vous associiez un espace de travail Log Analytics, nouveau ou existant, au projet Azure Migrate. La fonctionnalité de visualisation des dépendances dans Server Assessment est gratuite pendant 180 jours.

1. L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics entraîne des frais [Log Analytics standard](https://azure.microsoft.com/pricing/details/log-analytics/).
2. Pour prendre en charge des scénarios de migration sans frais supplémentaires, la solution Service Map n’entraîne pas de frais pendant 180 jours à compter du jour où l’espace de travail Log Analytics est associé au projet Azure Migrate. Au bout de 180 jours, des frais Log Analytics standard s’appliquent.

Lorsque vous inscrivez des agents auprès de l’espace de travail, utilisez l’ID et la clé fournis par le projet sur la page des étapes de l’agent d’installation.

Lorsque le projet Azure Migrate est supprimé, l’espace de travail ne l’est pas. Après la suppression du projet, l’utilisation de Service Map n’est pas gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.

> [!NOTE]
> La fonctionnalité de visualisation des dépendances utilise Service Map via un espace de travail Log Analytics. Depuis le 28 février 2018, avec l’annonce de la disponibilité générale d’Azure Migrate, la fonctionnalité est désormais disponible sans frais supplémentaires. Vous devez créer un nouveau projet pour utiliser l’espace de travail gratuit. Les espaces de travail existants avant la disponibilité générale sont toujours facturés, nous vous recommandons donc d’utiliser un nouveau projet.

En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Comment gérer l’espace de travail ?

Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate. Il n’est pas supprimé si vous supprimez le projet Azure Migrate dans lequel il a été créé. Si vous n’avez plus besoin de l’espace de travail, [supprimez-le](../azure-monitor/platform/manage-access.md) manuellement.

Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet Azure Migrate. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.

## <a name="next-steps"></a>Étapes suivantes
- [Regrouper des machines à l’aide de dépendances de machines](how-to-create-group-machine-dependencies.md)
- [Découvrez plus en détail](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) les questions fréquemment posées au sujet de la visualisation des dépendances.
