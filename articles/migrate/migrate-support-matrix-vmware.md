---
title: Prise en charge de l’évaluation VMware dans Azure Migrate
description: Découvrez la prise en charge de l’évaluation VMware dans Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029002"
---
# <a name="support-matrix-for-vmware-assessment"></a>Tableau de prise en charge pour l’évaluation VMware 

Cet article résume les paramètres et les limites de l’évaluation de machines virtuelles VMware avec [Azure Migrate : Évaluation de serveur](migrate-services-overview.md#azure-migrate-server-migration-tool). Si vous recherchez des informations sur la migration de machines virtuelles VMware vers Azure, consultez le [tableau de prise en charge de la migration](migrate-support-matrix-vmware-migration.md).

## <a name="overview"></a>Vue d’ensemble

Pour évaluer des machines locales pour une migration vers Azure avec cet article, vous ajoutez l’outil Azure Migrate : Server Assessment à un projet Azure Migrate. Vous déployez l’[appliance Azure Migrate](migrate-appliance.md). L’appliance découvre en permanence les machines locales et envoie les données de configuration et de performances à Azure. Après la découverte des machines, vous rassemblez les machines découvertes dans des groupes et effectuez l’évaluation d’un groupe.


## <a name="limitations"></a>Limites

**Support** | **Détails**
--- | ---
**Limites d’évaluation**| Découvrez et évaluez jusqu’à 35 000 machines virtuelles VMware dans un même [projet](migrate-support-matrix.md#azure-migrate-projects).
**Limites de projet** | Vous pouvez créer plusieurs projets dans un abonnement Azure. Un projet peut inclure à la fois des machines virtuelles VMware et Hyper-V, et des serveurs physiques, jusqu’aux limites d’évaluation.
**Découverte** | L’appliance Azure Migrate peut découvrir jusqu’à 10 000 machines virtuelles VMware sur un vCenter Server.
**Évaluation** | Vous pouvez ajouter jusqu’à 35 000 machines dans un groupe unique.<br/><br/> Vous pouvez évaluer jusqu’à 35,000 machines virtuelles par évaluation.

[Apprenez-en davantage](concepts-assessment-calculation.md) sur les évaluations.


## <a name="application-discovery"></a>Découverte des applications

En plus de découvrir les machines, Azure Migrate : Server Assessment peut découvrir les applications, rôles et fonctionnalités exécutées sur les machines. La découverte de votre inventaire d’applications vous permet d’identifier et de planifier un chemin de migration adapté à vos charges de travail locales. 

**Support** | **Détails**
--- | ---
Découverte | La découverte est sans agent, en utilisant les informations d’identification de l’invité de la machine, et en accédant à distance à des machines à l’aide des appels WMI et SSH.
Machines prises en charge | Machines virtuelles VMware locales.
Système d’exploitation des machines | Toutes les versions de Windows et Linux
Informations d'identification | Prend actuellement en charge l’utilisation d’une information d’identification pour tous les serveurs Windows et d’une information d’identification pour tous les serveurs Linux.<br/><br/> Vous créez un compte d’utilisateur invité pour les machines virtuelles Windows et un compte d’utilisateur standard/normal (accès non-sudo) pour toutes les machines virtuelles Linux.
limites | Pour la découverte d’application, vous pouvez en découvrir jusqu’à 10 000 par appliance. 

## <a name="vmware-requirements"></a>Configuration requise pour VMware

**VMware** | **Détails**
--- | ---
**vCenter Server** | Les machines que vous souhaitez découvrir et évaluer doivent être gérées par vCenter Server version 5.5, 6.0, 6.5 ou 6.7.
**Autorisations (évaluation)** | Compte vCenter Server en lecture seule.
**Autorisations (découverte d’application)** | Compte vCenter Server disposant d’un accès en lecture seule, et privilèges activés pour les machines virtuelles > opérations d’invité.
**Autorisations (visualisation des dépendances)** | Compte vCenter Server disposant d’un accès en lecture seule, et privilèges activés pour **Machines virtuelles** > **Opérations d’invité**.


## <a name="azure-migrate-appliance-requirements"></a>Conditions requises de l’appliance Azure Migrate

Azure Migrate utilise l’[appliance Azure Migrate](migrate-appliance.md) pour la découverte et l’évaluation. L’appliance pour VMware est déployée à l’aide d’un modèle OVA importé dans vCenter Server. 

- En savoir plus sur les [conditions requises de l’appliance](migrate-appliance.md#appliance---vmware) pour VMware.
- En savoir plus sur les [URL](migrate-appliance.md#url-access) auxquelles l’appliance doit accéder.

## <a name="port-access"></a>Accès au port

**Appareil** | **Connection**
--- | ---
Appliance | Connexions entrantes sur le port TCP 3389 pour permettre des connexions Bureau à distance avec l’appliance.<br/><br/> Connexions entrantes sur le port 44368 pour accéder à distance à l’application de gestion de l’appliance via l’URL : ```https://<appliance-ip-or-name>:44368``` <br/><br/>Connexions sortantes sur les ports 443, 5671 et 5672 pour envoyer les métadonnées de découverte et de performances à Azure Migrate.
Serveur vCenter | Connexions entrantes sur le port TCP 443 pour permettre à l’appliance de collecter les métadonnées de configuration et de performances pour les évaluations. <br/><br/> L’appliance se connecte à vCenter sur le port 443 par défaut. Si le serveur vCenter écoute sur un autre port, vous pouvez modifier le port lors de la configuration de la découverte.

## <a name="dependency-visualization"></a>Visualisation de dépendance

La visualisation des dépendances vous permet de visualiser les dépendances entre les machines que vous voulez utiliser et migrer. En général, vous utilisez le mappage des dépendances lorsque vous souhaitez évaluer les machines avec des niveaux de confiance élevés. Pour les machines virtuelles VMware, la visualisation des dépendances est prise en charge comme suit :

- **Visualisation des dépendances sans agent** : Cette option est actuellement en préversion. Il n’est pas nécessaire d’installer des agents sur les machines.
    - Une capture des données de connexion TCP des machines pour lesquelles elle est activée suffit. Une fois la découverte des dépendances lancée, l’appliance recueille les données des machines à un intervalle d’interrogation de cinq minutes.
    - Les données suivantes sont collectées :
        - Connexions TCP
        - Noms des processus ayant des connexions actives
        - Noms des applications installées exécutant les processus ci-dessus
        - Non. de connexions détectées à chaque intervalle d’interrogation
- **Visualisation des dépendances basée sur les agents** : Pour utiliser la visualisation des dépendances basée sur les agents, vous devez télécharger et installer les agents suivants sur chaque machine locale que vous souhaitez analyser.
    - Installez Microsoft Monitoring Agent (MMA) sur chaque machine. [En savoir plus](how-to-create-group-machine-dependencies.md#install-the-mma) sur l’installation de l’agent MMA.
    - Installez l’agent de dépendances sur chaque machine. [En savoir plus](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) sur l’installation de l’agent de dépendances.
    - En outre, si certaines de vos machines sont dépourvues de connexion Internet, vous devez télécharger et installer la passerelle Log Analytics sur ces machines.


## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue](best-practices-assessment.md) les meilleures pratiques pour la création d’évaluations.
- [Préparer l’évaluation](tutorial-prepare-vmware.md) VMware.
