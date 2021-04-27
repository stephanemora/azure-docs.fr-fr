---
title: Architecture de l’appliance Azure Migrate
description: Présente une vue d’ensemble de l’appliance Azure Migrate utilisée dans la découverte, l’évaluation et la migration de serveurs.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715007"
---
# <a name="azure-migrate-appliance-architecture"></a>Architecture de l’appliance Azure Migrate

Cet article décrit l’architecture et les processus de l’appliance Azure Migrate. L’appliance Azure Migrate est une appliance légère déployée localement pour découvrir les serveurs à migrer vers Azure.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

L’appliance Azure Migrate est utilisée dans les scénarios suivants.

**Scénario** | **Outil** | **Usage**
--- | --- | ---
**Découverte et évaluation des serveurs s’exécutant dans un environnement VMware** | Azure Migrate: découverte et évaluation | Découvrir les serveurs s’exécutant dans votre environnement VMware<br/><br/> Effectuez la découverte de l’inventaire logiciel installé, l’analyse des dépendances sans agent, et découvrez les instances et bases de données SQL Server.<br/><br/> Collectez les métadonnées de configuration et de performances du serveur pour les évaluations.
**Migration sans agent de serveurs s’exécutant dans un environnement VMware** | Azure Migrate : Migration de serveurs | Découvrez les serveurs s’exécutant dans votre environnement VMware.<br/><br/> Répliquez des serveurs sans installer d’agents sur ceux-ci.
**Découverte et évaluation des serveurs s’exécutant dans un environnement Hyper-V** | Azure Migrate: découverte et évaluation | Découvrez les serveurs s’exécutant dans votre environnement Hyper-V.<br/><br/> Collectez les métadonnées de configuration et de performances du serveur pour les évaluations.
**Découverte et évaluation des serveurs physiques ou virtualisés locaux** |  Azure Migrate: découverte et évaluation |  Découvrez les serveurs physiques ou virtualisés locaux.<br/><br/> Collectez les métadonnées de configuration et de performances du serveur pour les évaluations.

## <a name="deployment-methods"></a>Méthodes de déploiement

L'appliance peut être déployée à l’aide de deux méthodes :

- L’appliance peut être déployée à l’aide d’un modèle pour les serveurs s’exécutant dans un environnement VMware ou Hyper-V ([modèle OVA pour VMware](how-to-set-up-appliance-vmware.md) ou [VHD pour Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Si vous ne souhaitez pas utiliser de modèle, vous pouvez déployer l’appliance pour un environnement VMware ou Hyper-V à l’aide d’un [script d’installation PowerShell](deploy-appliance-script.md).
- Dans Azure Government, vous devez déployer l’appliance à l’aide d’un script d’installation PowerShell. Reportez-vous aux étapes de déploiement décrites [ici](deploy-appliance-script-government.md).
- Pour des serveurs physiques ou virtualisés locaux ou cloud, vous déployez toujours l’appliance à l’aide d’un script d’installation PowerShell. Découvrez les étapes de déploiement [ici](how-to-set-up-appliance-physical.md).
- Les liens de téléchargement sont disponibles dans les tableaux ci-dessous.

## <a name="appliance-services"></a>Services de l’appliance

L’appliance offre les services suivants :

- **Gestionnaire de configuration de l’appliance** : application web configurable avec les détails de la source pour démarrer la découverte et l’évaluation des serveurs. 
- **Agent de découverte** : collecte les métadonnées de configuration du serveur utilisables pour créer des évaluations locales.
- **Agent d’évaluation** : collecte les métadonnées de performances du serveur utilisables pour créer des évaluations basées sur les performances.
- **Service de mise à jour automatique** : tient à jour tous les agents en cours d’exécution sur l’appliance. S’exécute automatiquement une fois toutes les 24 heures.
- **Agent DRA** : orchestre la réplication des serveurs et coordonne la communication entre les serveurs répliquées et Azure. Utilisée seulement lors de la réplication de serveurs sur Azure avec une migration sans agent.
- **Passerelle** : envoie des données répliquées vers Azure. Utilisée seulement lors de la réplication de serveurs sur Azure avec une migration sans agent.
- **Agent de découverte et d’évaluation SQL** : envoie les métadonnées de configuration et de performances des instances et bases de données SQL Server à Azure.

> [!Note]
> Les trois derniers services ne sont disponibles que dans l’appliance utilisée pour la découverte et l’évaluation des serveurs s’exécutant dans votre environnement VMware.<br/> La découverte et l’évaluation d’instances et de bases de données SQL Server s’exécutant dans votre environnement VMware sont actuellement en préversion. Pour tester cette fonctionnalité, utilisez [**ce lien**](https://aka.ms/AzureMigrate/SQL) afin de créer un projet dans la région **Australie Est**. Si vous avez déjà un projet en Australie Est et si vous souhaitez tester cette fonctionnalité, vérifiez que vous avez effectué ces [**prérequis**](how-to-discover-sql-existing-project.md) dans le portail.

## <a name="discovery-and-collection-process"></a>Processus de découverte et de collecte

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Architecture des appliances":::

L’appliance communique avec les sources de découverte à l’aide du processus suivant.

**Processus** | **Appliance VMware** | **Appliance Hyper-V** | **Appliance physique**
---|---|---|---
**Démarrer la découverte** | L’appliance communique avec le serveur vCenter sur le port TCP 443 par défaut. Si vCenter Server écoute sur un port différent, vous pouvez configurer celui-ci dans le gestionnaire de configuration de l’appliance. | L’appliance communique avec les hôtes Hyper-V sur le port WinRM 5985 (HTTP). | L’appliance communique avec les serveurs Windows sur le port WinRM 5985 (HTTP), et avec les serveurs Linux sur le port 22 (TCP).
**Collecter les métadonnées de configuration et de performances** | L’appliance collecte les métadonnées des serveurs s’exécutant sur vCenter Server à l’aide d’API vSphere en se connectant sur le port 443 (port par défaut) ou sur tout autre port que vCenter Server écoute. | L’appliance collecte les métadonnées des serveurs s’exécutant sur des hôtes Hyper-V à l’aide d’une session Common Information Model (CIM) avec des hôtes sur le port 5985.| L’appliance collecte les métadonnées à partir de serveurs Windows à l’aide d’une session Common Information Model (CIM) avec des serveurs sur le port 5985, et à partir de serveurs Linux à l’aide de la connectivité SSH sur le port 22.
**Envoyer les données de détection** | L’appliance envoie les données collectées à l’outil de découverte et d’évaluation et à l’outil de migration de serveur d’Azure Migrate sur le port SSL 443.<br/><br/>  L’appliance peut se connecter à Azure via Internet ou via le peering privé ExpressRoute ou les circuits de peering Microsoft. | L’appliance envoie les données collectées à l’outil de découverte et d’évaluation d’Azure Migrate sur le port SSL 443.<br/><br/> L’appliance peut se connecter à Azure via Internet ou via le peering privé ExpressRoute ou les circuits de peering Microsoft. | L’appliance envoie les données collectées à l’outil de découverte et d’évaluation d’Azure Migrate sur le port SSL 443.<br/><br/> L’appliance peut se connecter à Azure via Internet ou via le peering privé ExpressRoute ou les circuits de peering Microsoft. 
**Fréquence de collecte de données** | Les métadonnées de configuration sont collectées et envoyées toutes les 30 minutes. <br/><br/> Les métadonnées de performances sont collectées toutes les 20 secondes, et agrégées pour envoyer un point de données à Azure toutes les 10 minutes. <br/><br/> Les données d’inventaire logiciel sont envoyées à Azure une fois toutes les 12 heures. <br/><br/> Les données de dépendance sans agent sont collectées toutes les 5 minutes, agrégées sur l’appliance, et envoyées à Azure toutes les 6 heures. <br/><br/> Les données de configuration SQL Server sont mises à jour toutes les 24 heures, et les données de performances capturées toutes les 30 secondes.| Les métadonnées de configuration sont collectées et envoyées toutes les 30 minutes. <br/><br/> Les métadonnées de performances sont collectées toutes les 30 secondes, et agrégées pour envoyer un point de données à Azure toutes les 10 minutes.|  Les métadonnées de configuration sont collectées et envoyées toutes les 30 minutes. <br/><br/> Les métadonnées de performances sont collectées toutes les 5 minutes, et agrégées pour envoyer un point de données à Azure toutes les 10 minutes.
**Évaluer et migrer** | Vous pouvez créer des évaluations à partir des métadonnées collectées par l’appliance à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.<br/><br/>En outre, vous pouvez commencer à migrer des serveurs s’exécutant dans votre environnement VMware en utilisant l’outil de migration de serveur Azure Migrate pour orchestrer la réplication de serveur sans agent.| Vous pouvez créer des évaluations à partir des métadonnées collectées par l’appliance à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate. | Vous pouvez créer des évaluations à partir des métadonnées collectées par l’appliance à l’aide de l’outil de découverte et d’évaluation d’Azure Migrate.

## <a name="next-steps"></a>Étapes suivantes

[Passez en revue](migrate-appliance.md) la matrice de prise en charge de l’appliance.