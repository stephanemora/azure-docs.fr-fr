---
title: Grouper des machines dans Azure Migrate avec la visualisation des dépendances sans agent
description: Décrit comment créer des groupes à l’aide de dépendances de machines sans agent.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: hamusa
ms.openlocfilehash: ff267aabe2f9e4cec38c307fe4382a84ba6d62df
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76288951"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Configurer la visualisation des dépendances sans agent pour l’évaluation

Cet article explique comment configurer le mappage de dépendances sans agent dans Azure Migrate : Server Assessment. 

> [!IMPORTANT]
> La visualisation des dépendances sans agent est actuellement en préversion pour les machines virtuelles VMware Azure détectées à l’aide d’une appliance Azure Migrate.
> Certaines fonctionnalités peuvent être limitées ou non prises en charge. Cette préversion est couverte par le support client et peut être utilisée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>À propos du mappage des dépendances

Le mappage des dépendances vous aide à visualiser les dépendances entre les machines que vous voulez évaluer et migrer. En général, vous utilisez le mappage des dépendances lorsque vous souhaitez évaluer les machines avec des niveaux de confiance élevés.

- Dans Azure Migrate : Server Assessment, vous rassemblez les machines en groupes à des fins d’évaluation. Les groupes se composent généralement de machines que vous souhaitez migrer simultanément et, dans un tel contexte, le mappage des dépendances vous permet de vérifier les dépendances de ces machines afin de les regrouper de manière précise.
- Grâce au mappage, vous pouvez découvrir les systèmes interdépendants devant migrer en même temps. Vous pouvez également déterminer si un système en cours d’exécution continue de servir les utilisateurs ou si une mise hors service peut être envisagée au lieu de la migration.
- La visualisation des dépendances permet de s’assurer que rien n’est laissé de côté et d’éviter les pannes inopinées lors de la migration.

## <a name="about-agentless-visualization"></a>À propos de la visualisation sans agent

La visualisation des dépendances sans agent ne nécessite pas d’installation d’agents des machines. Une capture des données de connexion TCP des machines pour lesquelles elle est activée suffit.

- Une fois la découverte des dépendances lancée, l’appliance recueille les données des machines à un intervalle d’interrogation de cinq minutes.
- Les données suivantes sont collectées :
    - Connexions TCP
    - Noms des processus ayant des connexions actives
    - Noms des applications installées exécutant les processus ci-dessus
    - Non. de connexions détectées à chaque intervalle d’interrogation

## <a name="current-limitations"></a>Limites actuelles

- La visualisation des dépendances sans agent n’est actuellement disponible que pour des machines virtuelles.
- Pour le moment, vous ne pouvez pas ajouter ou supprimer un serveur d’un groupe dans la vue d’analyse des dépendances.
- La carte des dépendances pour un groupe de serveurs n’est pas disponible actuellement.
- Actuellement, les données de dépendance ne peuvent pas être téléchargées sous forme de tableau.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé](how-to-add-tool-first-time.md) un projet Azure Migrate.
- L’analyse des dépendances sans agent est actuellement disponible uniquement pour les machines VMware.
- Si vous avez déjà créé un projet, assurez-vous que vous avez [ajouté](how-to-assess.md) Azure Migrate : Server Assessment.
- Assurez-vous d’avoir découvert vos machines VMware dans Azure Migrate. Pour ce faire, configurez une appliance Azure Migrate pour [VMware](how-to-set-up-appliance-vmware.md). L’appliance découvre les machines locales et envoie les métadonnées et les données de performances à Azure Migrate : Server Assessment. [Plus d’informations](migrate-appliance.md)
- [Consultez les conditions requises](migrate-support-matrix-vmware.md#agentless-dependency-visualization) pour configurer la visualisation des dépendances sans agent.



## <a name="create-a-user-account-for-discovery"></a>Créer un compte d’utilisateur pour la découverte

Configurez un compte d’utilisateur disposant des autorisations requises pour que Server Assessment puisse accéder à la machine virtuelle aux fins de la découverte. Vous pouvez spécifier un compte d’utilisateur.

- **Autorisation requise sur les machines virtuelles Windows** : Le compte d’utilisateur nécessite un accès « invité ».
- **Autorisation requise sur les machines virtuelles Linux** : Le privilège de racine est requis sur le compte. Le compte d’utilisateur a également besoin des deux capacités ci-dessous sur les fichiers/bin/netstat et/bin/ls : CAP_DAC_READ_SEARCH et CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>Ajouter le compte d’utilisateur à l’appliance

Vous devez ajouter le compte d’utilisateur à l’appliance.

Ajoutez le compte comme suit :

1. Ouvrez l’application de gestion de l’appliance. Accédez au panneau **Fournir les détails de vCenter**.
2. Dans la section **Découvrir les applications et les dépendances sur les machines virtuelles**, cliquez **Ajouter les informations d’identification**
3. Choisissez le **Système d’exploitation**.
4. Fournissez un nom convivial pour le compte.
5. Entrez le **Nom d’utilisateur** et le **Mot de passe**
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

Vous serez en mesure de visualiser les dépendances 6 heures après le démarrage de la découverte des dépendances.

## <a name="visualize-dependencies"></a>Visualiser les dépendances

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Recherchez la machine pour laquelle vous souhaitez afficher le mappage des dépendances.
3. Cliquez sur **Afficher les dépendances** dans la colonne **Dépendances**.
4. Modifiez la période pendant laquelle vous souhaitez afficher la carte à l’aide de la liste déroulante **Durée**.
5. Développez le groupe **Client** pour afficher la liste des machines qui ont une dépendance sur la machine sélectionnée.
6. Développez le groupe **Port** pour répertorier les machines qui ont une dépendance de la machine sélectionnée.
7. Pour accéder à la carte d’une machine dépendante, cliquez sur le nom de celle-ci, puis sur **Charger la carte des serveurs**

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

[Regrouper les machines](how-to-create-a-group.md)
