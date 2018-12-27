---
title: Grouper des machines à l’aide des dépendances de machine avec Azure Migrate | Microsoft Docs
description: Explique comment créer une évaluation à l’aide des dépendances de machine avec le service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 8756809de4ec1a8150610027a8197f1bcae213f0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53252503"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Grouper des machines à l’aide du mappage de dépendances de machine

Cet article explique comment créer un groupe de machines pour l’évaluation [Azure Migrate](migrate-overview.md) en visualisant les dépendances de machine. On utilise généralement cette méthode pour évaluer des groupes de machines virtuelles avec un niveau supérieur de confiance en vérifiant par recoupement les dépendances de machine avant d’exécuter une évaluation. La visualisation des dépendances peut vous aider à planifier efficacement votre migration vers Azure. Elle vous permet de ne rien oublier et vous épargne les pannes inopinées pendant la migration vers Azure. Vous pouvez découvrir tous les systèmes interdépendants qui doivent migrer en même temps et déterminer si un système en cours d’exécution continue de servir les utilisateurs ou si une mise hors service peut être envisagée au lieu de la migration.

> [!NOTE]
> La fonctionnalité de visualisation de dépendance n’est pas disponible dans Azure Government.

## <a name="prepare-for-dependency-visualization"></a>Préparer la visualisation des dépendances
Azure Migrate s’appuie sur la solution Service Map dans Log Analytics pour permettre la visualisation des dépendances des machines.

### <a name="associate-a-log-analytics-workspace"></a>Associer un espace de travail Log Analytics
Pour tirer parti de la visualisation des dépendances, vous pouvez associer un espace de travail Log Analytics, nouveau ou existant, à un projet Azure Migrate. Vous ne pouvez créer ou attacher un espace de travail que dans l’abonnement où le projet de migration est créé.

- Pour attacher un espace de travail Log Analytics à un projet, dans **Vue d’ensemble**, accédez à la section **Bases** du projet, puis cliquez sur **Requiert une configuration**

    ![Associer un espace de travail Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Lors de l’association d’un espace de travail, vous aurez la possibilité de créer un nouvel espace de travail ou de joindre un espace existant :
  - Quand vous créez un espace de travail, vous devez spécifier un nom pour celui-ci. L’espace de travail est ensuite créé dans une région appartenant à la même [zone géographique Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que le projet de migration.
  - Lorsque vous joignez un espace de travail existant, vous pouvez choisir parmi tous les espaces de travail disponibles dans le même abonnement que le projet de migration. Notez que seuls les espaces de travail qui ont été créés dans une région où [Service Map est pris en charge](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions) sont répertoriés. Pour être en mesure d’attacher un espace de travail, assurez-vous d’avoir accès en lecture à l’espace de travail.

> [!NOTE]
> Vous ne pouvez pas changer l’espace de travail associé à un projet de migration.

### <a name="download-and-install-the-vm-agents"></a>Téléchargement et installation des agents de machines virtuelles
Une fois que vous avez configuré un espace de travail, vous devez télécharger et installer des agents sur chacune des machines locales à évaluer. En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la [passerelle Log Analytics](../azure-monitor/platform/gateway.md) sur ces machines.

1. Dans **Vue d’ensemble**, cliquez sur **Gérer** > **Machines** et sélectionnez la machine souhaitée.
2. Dans la colonne **Dépendances**, cliquez sur **Installer des agents**.
3. Sur la page **Dépendances**, téléchargez et installez Microsoft Monitoring Agent (MMA) et l’agent de dépendances sur chacune des machines virtuelles à évaluer.
4. Copiez l’ID et la clé de l’espace de travail. Vous en aurez besoin lorsque vous installerez MMA sur la machine locale.

> [!NOTE]
> Pour automatiser l’installation des agents, vous pouvez utiliser n’importe quel outil de déploiement comme System Center Configuration Manager ou notre outil partenaire, [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), qui dispose d’une solution de déploiement de l’agent pour Azure Migrate.

### <a name="install-the-mma"></a>Installer MMA

Pour installer l’agent sur une machine Windows :

1. Double-cliquez sur l’agent téléchargé.
2. Sur la page d’**accueil**, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
3. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**.
4. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics** > **Suivant**.
5. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail Log Analytics. Collez l’ID et la clé de l’espace de travail que vous avez copiés sur le portail. Cliquez sur **Suivant**.

[Découvrez plus en détail](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) la liste des systèmes d’exploitation Windows pris en charge par MMA.

Pour installer l’agent sur une machine Linux :

1. Transférez le groupe approprié (x86 ou x64) sur votre ordinateur Linux à l’aide de scp/sftp.
2. Installez le bundle avec l’argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Découvrez plus en détail](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) la liste des systèmes d’exploitation Linux pris en charge par MMA.

### <a name="install-the-dependency-agent"></a>Installer l’agent de dépendances
1. Pour installer l’agent de dépendances sur une machine Windows, double-cliquez sur le fichier d’installation et suivez l’Assistant.
2. Pour installer l’agent de dépendances sur une machine Linux en tant que racine, exécutez la commande suivante :

    ```sh InstallDependencyAgent-Linux64.bin```

Apprenez-en davantage sur la prise en charge de l’agent de dépendances pour les systèmes d’exploitation [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) et [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[En savoir plus](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sur la façon dont vous pouvez utiliser des scripts pour installer l’agent de dépendances.

## <a name="create-a-group"></a>Créer un groupe

1. Après avoir installé les agents, accédez au portail et cliquez sur **Gérer** > **Machines**.
2. Recherchez la machine sur laquelle vous avez installé les agents.
3. La colonne **Dépendances** pour la machine doit maintenant apparaître comme **Afficher les dépendances**. Cliquez sur la colonne pour afficher les dépendances de la machine.
4. Le mappage de dépendances de la machine affiche les informations suivantes :
    - Connexions TCP entrantes (clients) et sortantes (serveurs) vers/depuis la machine
        - Les machines dépendantes sur lesquelles ne sont pas installés l’agent MMA et l’agent de dépendances sont regroupées par numéros de port
        - Les machines dépendantes sur lesquelles sont installés l’agent MMA et l’agent de dépendances apparaissent sous forme de zones distinctes
    - Processus en cours d’exécution dans la machine (vous pouvez développer chaque zone de machine pour afficher les processus correspondants)
    - Propriétés de chaque machine telles que Nom de domaine complet, Système d’exploitation ou Adresse MAC (vous pouvez cliquer sur chaque zone de machine pour afficher ces détails)

      ![Afficher les dépendances de machine](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Vous pouvez examiner les dépendances pour différentes durées en cliquant sur la durée dans l’étiquette de l’intervalle de temps. Par défaut, il est fixé à une heure. Vous pouvez le modifier ou spécifier une date de début, une date de fin et une durée.
5. Quand vous avez identifié des machines dépendantes à grouper, utilisez la commande Ctrl+clic pour les sélectionner sur le mappage, puis cliquez sur **Grouper les machines**.
6. Spécifiez un nom de groupe. Vérifiez que les machines dépendantes sont découvertes par Azure Migrate.

    > [!NOTE]
    > Si une machine dépendante n’est pas découverte par Azure Migrate, vous ne pouvez pas l’ajouter au groupe. Pour ajouter ces machines au groupe, vous devez réexécuter le processus de découverte avec l’étendue adéquate dans vCenter Server et vérifier que les machines sont découvertes par Azure Migrate.  

7. Si vous souhaitez créer une évaluation pour ce groupe, cochez la case prévue à cet effet.
8. Cliquez sur **OK** pour enregistrer le groupe.

Une fois le groupe créé, nous vous recommandons d’installer les agents sur toutes les machines du groupe et d’affiner le groupe en visualisant la dépendance de l’ensemble du groupe.

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez plus en détail](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) les questions fréquemment posées au sujet de la visualisation des dépendances.
- [Découvrez comment](how-to-create-group-dependencies.md) affiner le groupe en visualisant les dépendances de groupe.
- [Découvrez plus en détail](concepts-assessment-calculation.md) le mode de calcul des évaluations.
