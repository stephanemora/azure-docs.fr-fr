---
title: Visualisation des dépendances dans Azure Migrate
description: Fournit une vue d’ensemble des calculs d’évaluation dans le service d’évaluation de serveur de Azure Migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 65a99e230262ae05d34dc8c04e87252c15133fda
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425678"
---
# <a name="dependency-visualization"></a>Visualisation de dépendance

Cet article décrit la fonctionnalité de visualisation des dépendances dans Azure Migrate : Server Assessment.

La visualisation des dépendances vous permet de comprendre les dépendances entre les machines que vous voulez utiliser et migrer. En général, vous utilisez le mappage des dépendances lorsque vous souhaitez évaluer les machines avec des niveaux de confiance élevés.

- Dans Azure Migrate : Server Assessment, vous rassemblez les machines en groupes à des fins d’évaluation. Les groupes se composent généralement de machines que vous souhaitez migrer simultanément et, dans un tel contexte, la visualisation des dépendances vous permet de vérifier les dépendances de ces machines afin de les regrouper de manière précise.
- Grâce à la visualisation, vous pouvez découvrir les systèmes interdépendants devant migrer en même temps. Vous pouvez déterminer si les systèmes en cours d’exécution sont toujours utilisés ou s'ils peuvent être mis hors service plutôt qu'être migrés.
- La visualisation des dépendances permet de s’assurer que rien n’est laissé de côté et d’éviter les pannes inopinées lors de la migration.
- Cette fonctionnalité est particulièrement utile si vous ne connaissez pas de manière précise les machines faisant partie d'applications et devant, dès lors, être migrées ensemble vers Azure.


> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

## <a name="agent-based-and-agentless"></a>Visualisation des dépendances basée sur les agents ou sans agent

Deux options permettent de déployer la visualisation des dépendances :

- **Visualisation des dépendances sans agent** : Cette option actuellement en préversion n’est disponible que pour des machines virtuelles VMware. Il n’est pas nécessaire d’installer des agents sur les machines. 
    - Une capture des données de connexion TCP des machines pour lesquelles elle est activée suffit. [Plus d’informations](how-to-create-group-machine-dependencies-agentless.md)
Une fois la découverte des dépendances lancée, l’appliance recueille les données des machines à un intervalle d’interrogation de cinq minutes.
    - Les données suivantes sont collectées :
        - Connexions TCP
        - Noms des processus ayant des connexions actives
        - Noms des applications installées exécutant les processus ci-dessus
        - Non. de connexions détectées à chaque intervalle d’interrogation
- **Visualisation des dépendances basée sur les agents** : Pour utiliser la visualisation des dépendances basée sur les agents, vous devez télécharger et installer les agents suivants sur chaque machine locale que vous souhaitez analyser.  
    - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) doit être installé sur chaque machine. [En savoir plus](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) sur l’installation de l’agent MMA.
    - Le programme [Dependency Agent](../azure-monitor/platform/agents-overview.md#dependency-agent) doit être installé sur chaque machine. [En savoir plus](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) sur l’installation de l’agent de dépendances.
    - En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.

## <a name="agent-based-requirements"></a>Exigences de la visualisation des dépendances basée sur les agents

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>De quoi ai-je besoin pour déployer la visualisation des dépendances ?

Avant de déployer la visualisation des dépendances, vous devez disposer d’un projet Azure Migrate, avec l'outil Azure Migrate : Server Assessment ajouté au projet. Vous déployez la visualisation des dépendances après avoir configuré une appliance Azure Migrate pour découvrir vos machines locales.

[Apprenez-en davantage](how-to-assess.md) sur l’ajout de l’outil et le déploiement d’une appliance pour [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) ou serveurs physiques.


### <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Azure Migrate utilise la solution [Service Map](../operations-management-suite/operations-management-suite-service-map.md) dans [Journaux d’activité Azure Monitor](../log-analytics/log-analytics-overview.md) pour la visualisation des dépendances.

- Pour tirer parti de la visualisation des dépendances, vous pouvez associer un espace de travail Log Analytics, nouveau ou existant, à un projet Azure Migrate.
- L’espace de travail doit se trouver dans le même abonnement que celui dans lequel vous créez le projet Azure Migrate.
- Azure Migrate prend en charge les espaces de travail résidant dans les régions USA Est, Asie Sud-Est et Europe Ouest. Les espaces de travail des autres régions ne peuvent pas être associés à un projet. Notez également que l’espace de travail doit se trouver au sein d'une région dans laquelle [Service Map est pris en charge](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- L’espace de travail d’un projet Azure Migrate ne peut pas être modifié une fois qu’il a été ajouté.
- Dans Log Analytics, l’espace de travail associé à Azure Migrate est marqué avec la clé de projet de migration et le nom du projet.

    ![Accéder à l’espace de travail Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Est-ce une fonctionnalité payante ?

La visualisation des dépendances requiert Service Map et un espace de travail Log Analytics associé. 

- La solution Service Map n’entraîne aucun frais durant les 180 premiers jours. À savoir, à compter du jour où vous associez l'espace de travail Log Analytics au projet Azure Migrate.
- Au bout de 180 jours, des frais Log Analytics standard s’appliquent.
- L’utilisation de solutions autres que Service Map dans l’espace de travail Log Analytics associé entraîne des frais [Log Analytics standard](https://azure.microsoft.com/pricing/details/log-analytics/).
- Lorsque le projet Azure Migrate est supprimé, l’espace de travail ne l’est pas. Une fois le projet supprimé, l’utilisation de Service Map n’est pas gratuite et chaque nœud est facturé en fonction du niveau payant de l’espace de travail Log Analytics.

En savoir plus sur la tarification Azure Migrate [ici](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Si vous disposez de projets créés avant la disponibilité générale d'Azure Migrate, intervenue le 28 février 2018, vous avez peut-être fait l'objet de frais Service Map supplémentaires. Pour veiller à ne payer qu'au terme de 180 jours, nous vous recommandons de créer un nouveau projet, car les espaces de travail existants avant la disponibilité générale restent facturables.



### <a name="how-do-i-manage-the-workspace"></a>Comment gérer l’espace de travail ?

- Lorsque vous inscrivez des agents dans l’espace de travail, vous utilisez l’ID et la clé fournis par le projet Azure Migrate.
- Vous pouvez utiliser l’espace de travail Log Analytics en dehors d’Azure Migrate.
- Si vous supprimez le projet Azure Migrate associé, l’espace de travail n’est pas automatiquement supprimé. Vous devez [le supprimer manuellement](../azure-monitor/platform/manage-access.md).
- Ne supprimez pas l’espace de travail créé par Azure Migrate, sauf si vous supprimez le projet Azure Migrate. La suppression de l’espace de travail entraînerait un dysfonctionnement de la fonctionnalité de visualisation des dépendances.

## <a name="next-steps"></a>Étapes suivantes
- [Regrouper des machines à l’aide de dépendances de machines](how-to-create-group-machine-dependencies.md)
- [Découvrez plus en détail](common-questions-discovery-assessment.md#what-is-dependency-visualization) les questions fréquemment posées au sujet de la visualisation des dépendances.


