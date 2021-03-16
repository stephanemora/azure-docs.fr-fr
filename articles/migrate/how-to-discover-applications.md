---
title: Découvrir des applications sur des serveurs locaux avec Azure Migrate
description: Apprenez à découvrir les applications, rôles et fonctionnalités sur des serveurs locaux à l’aide d’Azure Migrate Server Assessment.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: 8266b585881546b37bbb21b82780ab26d85dada7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048078"
---
# <a name="discover-installed-applications-roles-and-features-software-inventory-and-sql-server-instances-and-databases"></a>Découvrir les applications, rôles et fonctionnalités installés (inventaire logiciel), ainsi que des instances et bases de données SQL Server

Cet article explique comment découvrir des applications, rôles et fonctionnalités installés (inventaire logiciel), ainsi que des instances et bases de données sur des serveurs s’exécutant dans votre environnement VMware, à l’aide de l’outil d’évaluation de serveur Azure Migrate.

L’établissement de l’inventaire logiciel permet d’identifier et de personnaliser un chemin de migration vers Azure pour vos charges de travail. L’inventaire logiciel utilise l’appliance Azure Migrate pour effectuer la découverte en utilisant les informations d’identification du serveur. Il fonctionne complètement sans agent (aucun agent n’est installé sur les serveurs pour collecter ces données).

> [!NOTE]
> L’inventaire logiciel est actuellement en préversion uniquement pour les serveurs s’exécutant dans un environnement VMware, et est limité à la découverte. Actuellement, nous n’offrons pas d’évaluation basée sur les applications.<br/> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

## <a name="before-you-start"></a>Avant de commencer

- Assurez-vous que vous avez [créé un projet Azure Migrate](./create-manage-projects.md) auquel vous avez ajouté l’outil d’évaluation de serveur Azure Migrate.
- Examinez la [configuration requise pour VMware](migrate-support-matrix-vmware.md#vmware-requirements) pour dresser l’inventaire logiciel.
- Examinez la [configuration requise de l’appliance](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) avant de configurer celle-ci.
- Examinez la [configuration requise pour la découverte des applications](migrate-support-matrix-vmware.md#application-discovery-requirements) avant de lancer un inventaire logiciel des serveurs.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Déployer et configurer l’appliance Azure Migrate

1. [Examinez](migrate-appliance.md#appliance---vmware) la configuration requise pour le déploiement de l’appliance Azure Migrate.
2. Passez en revue les URL Azure auxquelles l’appliance devra accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et les [clouds Government](migrate-appliance.md#government-cloud-urls).
3. [Passez en revue les données](migrate-appliance.md#collected-data---vmware) que l’appliance collecte pendant la découverte et l’évaluation.
4. [Notez](migrate-support-matrix-vmware.md#port-access-requirements) les conditions d’accès aux ports pour l’appliance.
5. [Déployez l’appliance Azure Migrate](how-to-set-up-appliance-vmware.md) pour démarrer la découverte. Pour déployer l’appliance, vous téléchargez et importez un modèle OVA dans VMware afin de créer un serveur s’exécutant dans votre vCenter Server. Après avoir déployé l’appliance, vous devez l’inscrire auprès du projet Azure Migrate et la configurer pour lancer la découverte.
6. Quand vous configurez l’appliance, vous devez spécifier les éléments suivants dans le gestionnaire de configuration de l’appliance :
    - détails du vCenter Server auquel vous souhaitez vous connecter ;
    - informations d’identification de vCenter Server étendues pour découvrir les serveurs dans votre environnement VMware ;
    - informations d’identification du serveur, qui peuvent être des informations d’identification de domaine/Windows (hors domaine)/Linux (hors domaine). [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.

## <a name="verify-permissions"></a>Vérification des autorisations

- Vous devez [créer un compte vCenter Server en lecture seule](./tutorial-discover-vmware.md#prepare-vmware) pour la découverte et l’évaluation. Le compte en lecture seule nécessite des privilèges pour **Machines virtuelles** > **Opérations d’invité** afin de pouvoir interagir avec les serveurs pour l’établissement de l’inventaire logiciel.
- Vous pouvez ajouter plusieurs informations d’identification de domaine et hors domaine (Windows/Linux) sur le gestionnaire de configuration de l’appliance pour la découverte d’application. Vous avez besoin d’un compte d’utilisateur invité pour les serveurs Windows et d’un compte d’utilisateur standard/normal (accès non-sudo) pour tous les serveurs Linux. [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.

### <a name="add-credentials-and-initiate-discovery"></a>Ajouter les informations d’identification et lancer la découverte

1. Ouvrez le gestionnaire de configuration d’appliance, puis procédez aux vérifications préalable et à l’inscription de l’appliance.
2. Accédez au panneau **Gérer les informations d’identification et les sources de découverte**.
1.  À l’**Étape 1 : Fournir les informations d’identification de vCenter Server**, cliquez sur **Ajouter des informations d’identification** afin de fournir les informations d’identification du compte vCenter Server que l’appliance utilisera pour découvrir les serveurs s’exécutant sur le vCenter Server.
1. À l’**Étape 2 : Fournir les détails de vCenter Server**, cliquez sur **Ajouter une source de découverte** afin de sélectionner le nom convivial des informations d’identification dans la liste déroulante, puis spécifiez l’**adresse IP/nom de domaine complet** de l’instance vCenter Server. :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panneau 3 du gestionnaire de configuration de l’appliance pour les détails de vCenter Server":::
1. À l’**Étape 3 : Fournir les informations d’identification de serveur pour effectuer l’inventaire logiciel, l’analyse des dépendances sans agent et la découverte des instances et bases de données SQL Server**, cliquez sur **Ajouter les informations d’identification** pour fournir plusieurs informations d’identification de serveur afin de lancer un inventaire logiciel.
1. Cliquez sur **Démarrer la découverte** pour lancer la découverte du vCenter Server.

 Une fois la découverte de vCenter Server terminée, l’appliance lance la découverte des applications, rôles et fonctionnalités installés (inventaire logiciel). Sa durée dépend du nombre de serveurs découverts. Pour 500 serveurs, environ une heure s’écoule avant que l’inventaire découvert apparaisse dans le portail Azure Migrate.

## <a name="review-and-export-the-inventory"></a>Examiner et exporter l’inventaire

Une fois l’inventaire logiciel terminé, vous pouvez l’examiner et l’exporter dans le portail Azure.

1. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Évaluation de serveur**, cliquez sur le nombre affiché pour ouvrir la page **Serveurs découverts**.

    > [!NOTE]
    > À ce stade, vous pouvez également activer l’analyse des dépendances pour les serveurs découverts, afin de pouvoir visualiser les dépendances entre les serveurs que vous souhaitez évaluer. [En savoir plus](concepts-dependency-visualization.md) sur l’analyse des dépendances.

2. Dans la colonne **Applications découvertes** , cliquez sur le nombre affiché pour examiner les applications, rôles et fonctionnalités découverts.
4. Pour exporter l’inventaire, dans **Serveurs détectés**, cliquez sur **Exporter l’inventaire des applications**.

L’inventaire des applications est exporté et téléchargé au format Excel. La feuille **Inventaire des applications** affiche toutes les applications découvertes sur toutes les serveurs.

## <a name="discover-sql-server-instances-and-databases"></a>Découvrir les instances et bases de données SQL Server

- La découverte d’applications identifie également les instances SQL Server s’exécutant dans votre environnement VMware.
- Si vous n’avez pas fourni les informations d’identification d’authentification Windows ou SQL Server sur le gestionnaire de configuration de l’appliance, ajoutez les informations d’identification afin que l’appliance puisse les utiliser pour se connecter aux instances SQL Server respectives.

Une fois connectée, l’appliance recueille les données de configuration et de performances des instances et bases de données SQL Server. Les données de configuration SQL Server sont mises à jour toutes les 24 heures, et les données de performances capturées toutes les 30 secondes. Par conséquent, jusqu’à 24 heures peuvent s’écouler avant la mise à jour sur le portail de toute modification des propriétés de l’instance et des bases de données SQL Server, telles que l’état de la base de données, le niveau de compatibilité, etc.

## <a name="next-steps"></a>Étapes suivantes

- [Créez une évaluation](how-to-create-assessment.md) pour les serveurs découverts.
- [Évaluer des serveurs SQL Server](./tutorial-assess-sql.md) en vue de leur migration vers Azure SQL.
