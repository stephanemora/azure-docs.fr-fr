---
title: Découvrir les applications, rôles et fonctionnalités sur des serveurs locaux avec Azure Migrate
description: Apprenez à découvrir les applications, rôles et fonctionnalités sur des serveurs locaux à l’aide d’Azure Migrate Server Assessment.
ms.topic: article
ms.date: 11/20/2019
ms.openlocfilehash: 7c3a6360e051b8cb694393695e75578ce2c135ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846318"
---
# <a name="discover-machine-apps-roles-and-features"></a>Détection des applications, des rôles et des fonctionnalités de la machine

Cet article décrit comment découvrir les applications, rôles et fonctionnalités sur des serveurs locaux à l’aide d’Azure Migrate : Server Assessment.

La découverte de l’inventaire des applications et des rôles/fonctionnalités sur vos machines locales vous aide à identifier et planifier votre migration vers Azure en prenant en compte vos charges de travail.

> [!NOTE]
> La détection d’applications est actuellement prise en charge pour les machines virtuelles VMware uniquement et est limitée à la détection uniquement. Nous n’offrons pas encore d’évaluation basée sur les applications. Évaluation basée sur des ordinateurs pour les machines virtuelles VMware, les machines virtuelles Hyper-V et les serveurs physiques locaux.

La découverte d’applications à l’aide de l’évaluation de serveur d’Azure Migrate est sans agent. Rien n’a besoin d’être installé sur les ordinateurs et machines virtuelles. L’évaluation de serveur utilise l’appliance Azure Migrate pour effectuer la détection avec les informations d’identification de l’invité de l’ordinateur. L’appliance accède à distance aux machines VMware à l’aide des API VMware.


## <a name="before-you-start"></a>Avant de commencer

1. Assurez-vous que vous avez [créé](how-to-add-tool-first-time.md) un projet Azure Migrate.
2. Vérifiez que vous avez [ajouté](how-to-assess.md) l’outil d’évaluation de serveur Azure Migrate à un projet.
4. Vérifiez la [configuration requise pour VMware](migrate-support-matrix-vmware.md#vmware-requirements) pour découvrir et évaluer les machines virtuelles VMware avec l’appliance Azure Migrate.
5. Vérifiez la [configuration requise](migrate-appliance.md) pour le déploiement de l’appliance Azure Migrate.
6. [Vérifiez la prise en charge et les exigences](migrate-support-matrix-vmware.md#application-discovery) pour la découverte des applications.

## <a name="prepare-for-app-discovery"></a>Préparer la détection d’applications

1. [Préparez le déploiement de l’appliance](tutorial-prepare-vmware.md). La préparation comprend la vérification des paramètres de l’appliance et la configuration d’un compte qui sera utilisé par l’appliance pour accéder à vCenter Server.
2. Vérifiez que vous disposez d’un compte d’utilisateur (un pour chacun des serveurs Windows et Linux) avec les autorisations d’administrateur pour les machines sur lesquelles vous souhaitez découvrir des applications, des rôles et des fonctionnalités.
3. [Déployez l’appliance Azure Migrate](how-to-set-up-appliance-vmware.md) pour démarrer la découverte. Pour déployer l’appliance, vous téléchargez et importez un modèle OVA dans VMware pour créer l’appliance en tant que machine virtuelle VMware. Vous configurez l’appliance, puis l’inscrivez auprès d’Azure Migrate.
2. Lorsque vous déployez l’appliance, pour démarrer la découverte continue, vous spécifiez les éléments suivants :
    - Le nom du vCenter Server auquel vous souhaitez vous connecter.
    - Les informations d’identification que vous avez créées pour que l’appliance se connecte à vCenter Server.
    - Les informations d’identification de compte que vous avez créées pour que l’appliance se connecte aux machines virtuelles Windows/Linux.

Une fois que l’appliance a été déployée et que vous avez fourni les informations d’identification, l’appliance démarre la découverte continue des métadonnées de la machine virtuelle et des données de performances, ainsi que la découverte des applications, des fonctionnalités et des rôles.  La durée de la découverte d’application dépend du nombre de machines virtuelles dont vous disposez. La découverte d’applications pour 500 machines virtuelles prend généralement une heure.

## <a name="review-and-export-the-inventory"></a>Examiner et exporter l’inventaire

Une fois la détection terminée, si vous avez fourni des informations d’identification pour la découverte d’application, vous pouvez passer en revue et exporter l’inventaire des applications dans le portail Azure.

1. Dans la page **Azure Migrate - Serveurs** > **Azure Migrate : Évaluation de serveur**, cliquez sur le nombre affiché pour ouvrir la page **Serveurs découverts**.

    > [!NOTE]
    > À ce stade, vous pouvez également configurer le mappage des dépendances pour les ordinateurs découverts, afin de pouvoir visualiser les dépendances entre les machines que vous souhaitez évaluer. [Plus d’informations](how-to-create-group-machine-dependencies.md)

2. Dans **Applications découvertes**, cliquez sur le nombre affiché.
3. Dans **Inventaire des applications**, vous pouvez passer en revue les applications, les fonctionnalités et les rôles découverts.
4. Pour exporter l’inventaire, dans **Serveurs détectés**, cliquez sur **Exporter l’inventaire des applications**.

L’inventaire des applications est exporté et téléchargé au format Excel. La feuille **Inventaire des applications** affiche toutes les applications découvertes sur toutes les machines.

## <a name="next-steps"></a>Étapes suivantes

- [Créez une évaluation](how-to-create-assessment.md) pour la migration lift-and-shift des serveurs découverts.
- Évaluez une base de données SQL Server avec [Azure Migrate : Évaluation de base de données](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017).
