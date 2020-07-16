---
title: Configurer l’analyse des dépendances sans agent dans l’outil d’évaluation de serveur Azure Migrate
description: Configurez l’analyse des dépendances sans agent dans l’outil d’évaluation de serveur Azure Migrate.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: dc2ea0656198927cc8ae58533d296a2bedc37c13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84771374"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analyser les dépendances des machines (sans agent)

Cet article explique comment configurer l’analyse des dépendances sans agent dans l’outil Azure Migrate : évaluation de serveur. L’[analyse des dépendances](concepts-dependency-visualization.md) vous permet d’identifier et de comprendre les dépendances entre les machines pour l’évaluation et la migration vers Azure.


> [!IMPORTANT]
> La visualisation des dépendances sans agent est actuellement en préversion pour les machines virtuelles VMware découvertes avec l’outil Azure Migrate : Server Assessment.
> Les fonctionnalités peuvent être limitées ou incomplètes.
> Cette préversion est couverte par le support client et peut être utilisée pour les charges de travail de production.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Limites actuelles

- Dans la vue d’analyse des dépendances, actuellement vous ne pouvez pas ajouter ou supprimer de serveur dans un groupe.
- Aucune carte des dépendances pour un groupe de serveurs n’est disponible actuellement.
- Les données de dépendances ne peuvent pas être téléchargées sous forme de tableau.

## <a name="before-you-start"></a>Avant de commencer

- [Passez en revue](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) les systèmes d’exploitation pris en charge et les autorisations requises.
- Vérifiez les points suivants :
    - Vous disposez d’un projet Azure Migrate. Si ce n’est pas le cas, [créez-en un](how-to-add-tool-first-time.md) maintenant.
    - Vous avez [ajouté](how-to-assess.md) l’outil Azure Migrate : Server Assessment au projet.
    - Vous avez configuré une [appliance Azure Migrate](migrate-appliance.md) pour découvrir les machines locales. Vous avez [configuré une appliance](how-to-set-up-appliance-vmware.md) pour les machines virtuelles VMware. L’appliance découvre les machines locales, puis envoie les métadonnées et les données de performances à Azure Migrate : évaluation de serveur.
- Vérifiez que VMware Tools (version supérieure à 10.2) est installé sur chaque machine virtuelle que vous souhaitez analyser.


## <a name="create-a-user-account-for-discovery"></a>Créer un compte d’utilisateur pour la découverte

Configurez un compte d’utilisateur pour que Server Assessment puisse accéder à la machine virtuelle afin de découvrir les dépendances. [Apprenez-en davantage](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) sur les exigences relatives aux comptes pour les machines virtuelles Windows et Linux.


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
4. Dans la page **Ajouter des serveurs**, choisissez l’appliance qui découvre les machines appropriées.
5. Dans la liste des machines, opérez votre sélection.
6. Cliquez sur **Ajouter des serveurs**.

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

## <a name="export-dependency-data"></a>Exporter les données de dépendance

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Cliquez sur l’icône **Analyse des dépendances**.
3. Cliquez sur **Exporter les dépendances d’application**.
4. Dans la page **Exporter les dépendances d’application**, choisissez l’appliance qui découvre les machines appropriées.
5. Sélectionnez l’heure de début et l’heure de fin. Notez que vous pouvez télécharger les données uniquement pour les 30 derniers jours.
6. Cliquez sur **Exporter la dépendance**.

Les données de dépendances sont exportées et téléchargées dans un format CSV. Le fichier téléchargé contient les données de dépendances sur toutes les machines activées pour l’analyse des dépendances. 

![Exporter les dépendances](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Informations sur les dépendances

Chaque ligne du fichier CSV exporté correspond à une dépendance observée dans le créneau spécifié. 

Le tableau suivant récapitule les champs du fichier CSV exporté. Notez que les champs de nom de serveur, d’application et de processus sont uniquement renseignés pour les serveurs sur lesquels l’analyse des dépendances sans agent est activée.

**Nom du champ** | **Détails**
--- | --- 
Créneau | Créneau pendant lequel la dépendance a été observée. <br/> Les données de dépendances sont actuellement capturées sur des créneaux de six heures.
Nom du serveur source | Nom de la machine source 
Application source | Nom de l’application sur la machine source 
Processus source | Nom du processus sur la machine source 
Nom du serveur de destination | Nom de la machine de destination
IP de destination | Adresse IP de la machine de destination
Application de destination | Nom de l’application sur la machine de destination
Processus de destination | Nom du processus sur la machine de destination 
Port de destination | Numéro de port sur la machine de destination


## <a name="stop-dependency-discovery"></a>Arrêter la découverte de dépendance

Choisissez les machines sur lesquelles vous souhaitez arrêter la découverte de dépendance.

1. Dans **Azure Migrate : Server Assessment**, cliquez sur **Serveurs découverts**.
2. Cliquez sur l’icône **Analyse des dépendances**.
3. Cliquez sur **Supprimer des serveurs**.
3. Dans la page **Supprimer des serveurs**, choisissez l’**appliance** qui découvre les machines virtuelles sur lesquelles vous souhaitez arrêter la découverte de dépendance.
4. Dans la liste des machines, opérez votre sélection.
5. Cliquez sur **Supprimer des serveurs**.


## <a name="next-steps"></a>Étapes suivantes

[Grouper des machines](how-to-create-a-group.md) pour l’évaluation.
