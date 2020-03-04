---
title: Configurer la visualisation des dépendances sans agent dans Azure Migrate
description: 'Configurez des groupes à l’aide de la visualisation des dépendances sans agent dans Azure Migrate : Server Assessment.'
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589128"
---
# <a name="set-up-agentless-dependency-visualization"></a>Configurer la visualisation des dépendances sans agent 

Cet article explique comment configurer la visualisation des dépendances dans l’évaluation de serveur Azure Migrate. La [visualisation des dépendances](concepts-dependency-visualization.md#what-is-dependency-visualization) vous permet d’identifier et de comprendre les dépendances entre les machines que vous voulez évaluer et migrer vers Azure.

La visualisation des dépendances sans agent vous aide à identifier les dépendances de machine sans installer d’agents sur les ordinateurs. Une capture des données de connexion TCP des machines pour lesquelles elle est activée suffit.

> [!IMPORTANT]
> La visualisation des dépendances sans agent est actuellement en préversion pour les machines virtuelles VMware Azure uniquement, découvertes à l’aide de l’outil Azure Migrate : Server Assessment.
> Les fonctionnalités peuvent être limitées ou incomplètes.
> Cette préversion est couverte par le support client et peut être utilisée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limites actuelles

- Pour le moment, vous ne pouvez pas ajouter ou supprimer un serveur d’un groupe dans la vue d’analyse des dépendances.
- Une carte des dépendances pour un groupe de serveurs n’est pas disponible actuellement.
- Actuellement, les données de dépendance ne peuvent pas être téléchargées sous forme de tableau.

## <a name="before-you-start"></a>Avant de commencer

- [Consultez](concepts-dependency-visualization.md#agentless-visualization) les exigences et les coûts associés à la visualisation des dépendances sans agent.
- Consultez les [conditions de prise en charge](migrate-support-matrix-vmware.md#agentless-dependency-visualization) pour configurer la visualisation des dépendances sans agent.
- Assurez-vous que vous avez [créé](how-to-add-tool-first-time.md) un projet Azure Migrate.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) l’outil Azure Migrate : Server Assessment.
- Assurez-vous d’avoir configuré une [appliance Azure Migrate](migrate-appliance.md) pour découvrir vos machines locales. Découvrez comment configurer une appliance pour des machines virtuelles [VMware](how-to-set-up-appliance-vmware.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment.


## <a name="create-a-user-account-for-discovery"></a>Créer un compte d’utilisateur pour la découverte

Configurez un compte d’utilisateur pour que Server Assessment puisse accéder à la machine virtuelle à une fin de découverte. Vous pouvez spécifier un compte d’utilisateur.

- **Machines virtuelles Windows** : Le compte d’utilisateur doit être un administrateur local ou un administrateur de domaine.
- **Machines virtuelles Linux** : Le privilège de racine est requis sur le compte. Le compte d’utilisateur a également besoin des deux capacités ci-dessous sur les fichiers/bin/netstat et/bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Ajouter le compte d’utilisateur à l’appliance

Ajoutez le compte d’utilisateur à l’appliance.

1. Ouvrez l’application de gestion de l’appliance. 
2. Accédez au panneau **Fournir les détails de vCenter**.
3. Dans **Découvrir l’application et les dépendances sur les machines virtuelles**, cliquez sur **Ajouter les informations d’identification**.
3. Choisissez le **système d’exploitation**, fournissez un nom convivial pour le compte, puis le **nom d’utilisateur**/**mot de passe**.
6. Cliquez sur **Enregistrer**.
7. Cliquez sur **Enregistrer et démarrer la découverte**.

    ![Ajouter un compte d’utilisateur de machine virtuelle](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Démarrer la découverte de dépendance

Choisissez les machines sur lesquelles vous souhaitez activer la découverte de dépendance.

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Cliquez sur l’icône **Analyse des dépendances**.
3. Cliquez sur **Ajouter des serveurs**.
3. Dans la page **Ajouter des serveurs**, choisissez l’appliance qui découvre les machines appropriées.
4. Dans la liste des machines, opérez votre sélection.
5. Cliquez sur **Ajouter des serveurs**.

    ![Démarrer la découverte de dépendance](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Vous pouvez visualiser les dépendances environ six heures après le démarrage de la découverte des dépendances.

## <a name="visualize-dependencies"></a>Visualiser les dépendances

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Recherchez la machine que vous souhaitez afficher.
3. Dans la colonne **Dépendances**, cliquez sur **Afficher les dépendances**.
4. Modifiez la période pendant laquelle vous souhaitez afficher la carte à l’aide de la liste déroulante **Durée**.
5. Développez le groupe **Client** pour afficher la liste des machines qui ont une dépendance sur la machine sélectionnée.
6. Développez le groupe **Port** pour répertorier les machines qui ont une dépendance de la machine sélectionnée.
7. Pour accéder à la carte d’une machine dépendante, cliquez sur le nom de la machine > **Charger la carte des serveurs**.

    ![Développer un groupe de ports du serveur et charger une carte des serveurs](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Développer un groupe de clients ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Développez la machine sélectionnée pour afficher les détails au niveau processus pour chaque dépendance.

    ![Développer un serveur pour afficher les processus](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Les informations de processus pour une dépendance ne sont pas toujours disponibles. Si elles ne sont pas disponible, la dépendance est représentée par le processus marqué comme « Processus inconnu ».

## <a name="stop-dependency-discovery"></a>Arrêter la découverte de dépendance

Choisissez les machines sur lesquelles vous souhaitez arrêter la découverte de dépendance.

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Cliquez sur l’icône **Analyse des dépendances**.
3. Cliquez sur **Supprimer des serveurs**.
3. Dans la page **Supprimer des serveurs**, choisissez l’**appliance** qui découvre les machines virtuelles sur lesquelles vous souhaitez arrêter la découverte de dépendance.
4. Dans la liste des machines, opérez votre sélection.
5. Cliquez sur **Supprimer des serveurs**.


## <a name="next-steps"></a>Étapes suivantes

[Grouper les machines](how-to-create-a-group.md) à des fins d’évaluation.
