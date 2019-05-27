---
title: Définissez la récupération d’urgence pour les machines virtuelles de Hyper-V locales dans des clouds VMM vers Azure avec Site Recovery | Microsoft Docs
description: Apprenez comment configurer la récupération d’urgence de machines virtuelles de Hyper-V locales dans des clouds System Center VMM vers Azure à l’aide de Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 8c3c9347a027cccfaef6def84bfdc4c83555e98a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966492"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>Configurer la récupération d’urgence dans Azure de machines virtuelles Hyper-V locales hébergées dans des clouds VMM

Cet article décrit comment activer la réplication des machines virtuelles de Hyper-V locales gérées par System Center Virtual Machine Manager (VMM), récupération d’urgence vers Azure à l’aide de la [Azure Site Recovery](site-recovery-overview.md) service. Si vous n’utilisez pas VMM, [suivez ce didacticiel](hyper-v-azure-tutorial.md) à la place.

Il s’agit du troisième tutoriel d’une série qui montre comment configurer la reprise d’activité sur Azure pour des machines virtuelles VMware locales. Dans le didacticiel précédent, nous [préparé l’environnement Hyper-V en local](hyper-v-prepare-on-premises-tutorial.md) pour la récupération d’urgence vers Azure.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Sélectionner la source et la cible de réplication.
> * Configurer l’environnement de réplication source, y compris les composants de Site Recovery en local et l’environnement de réplication cible.
> * Configurer le mappage de réseau pour le mappage entre les réseaux VMM VM et réseaux virtuels Azure.
> * Créer une stratégie de réplication.
> * Activer la réplication pour une machine virtuelle.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour obtenir des instructions détaillées, consultez les articles de la **Guides pratiques** section de la [documentation Site Recovery](https://docs.microsoft.com/en-us/azure/site-recovery).

## <a name="before-you-begin"></a>Avant de commencer

Il s’agit du troisième didacticiel d’une série. Il part du principe que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer un serveur Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

1. Dans le portail Azure, accédez à **coffres Recovery Services** et sélectionnez le coffre. Nous avons préparé le coffre-fort **ContosoVMVault** au cours du tutoriel précédent.
2. Dans **mise en route**, sélectionnez **Site Recovery**, puis sélectionnez **préparer l’Infrastructure**.
3. Dans **objectif de Protection** > **sont où se trouvent vos machines ?**, sélectionnez **On-premises**.
4. Dans **Où voulez-vous répliquer vos machines ?**, sélectionnez **Dans Azure**.
5. Dans **vos machines sont-elles virtualisées ?**, sélectionnez **Oui, avec Hyper-V**.
6. Dans **vous utilisez System Center VMM pour gérer vos hôtes Hyper-V ?**, sélectionnez **Oui**.
7.  Sélectionnez **OK**.

    ![Objectif de réplication](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

1. Dans **Planification du déploiement**, si vous planifiez un déploiement de grande envergure, téléchargez l’outil Planificateur de déploiement pour Hyper-V à partir du lien figurant dans la page. [En savoir plus](hyper-v-deployment-planner-overview.md) sur la planification du déploiement Hyper-V.
2. Pour ce didacticiel, nous n’avez pas besoin du Planificateur de déploiement. Dans **avez-vous effectué la planification du déploiement ?**, sélectionnez **je le ferai plus tard**, puis sélectionnez **OK**.

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Lorsque vous configurez l’environnement source, vous installez le fournisseur Azure Site Recovery sur le serveur VMM et inscrivez le serveur dans le coffre. Vous installez l’agent Azure Recovery Services sur chaque hôte Hyper-V.

1. Dans **préparer l’Infrastructure**, sélectionnez **Source**.
2. Dans **préparer la source**, sélectionnez **+ VMM** pour ajouter un serveur VMM. Dans **Ajouter un serveur**, vérifiez que **Serveur System Center VMM** s’affiche dans **Type de serveur**.
3. Téléchargez le programme d’installation du fournisseur Microsoft Azure Site Recovery.
4. Téléchargez la clé d'inscription du coffre. Vous en aurez besoin lorsque vous exécutez l’installation du fournisseur. Une fois générée, la clé reste valide pendant 5 jours.
5. Téléchargez le programme d’installation de l’agent Microsoft Azure Recovery Services.

    ![Fournisseur de téléchargement, de clé d’inscription et de l’agent](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>Installer le fournisseur sur le serveur VMM

1. Dans l’Assistant Installation du fournisseur Azure Site Recovery > **Microsoft Update**, choisissez d’utiliser Microsoft Update pour rechercher les mises à jour du fournisseur.
2. Dans **Installation**, acceptez l’emplacement d’installation par défaut pour le fournisseur et sélectionnez **installer**.
3. Après l’installation, dans l’Assistant Inscription de Microsoft Azure Site Recovery > **paramètres du coffre**, sélectionnez **Parcourir**et dans **fichier de clé**, sélectionnez la clé d’archivage de fichiers qui vous avez téléchargé.
4. Spécifiez l’abonnement Azure Site Recovery et le nom du coffre (**ContosoVMVault**). Spécifiez un nom convivial pour le serveur VMM afin de l’identifier dans le coffre.
5. Dans **Paramètres de proxy**, sélectionnez **Se connecter directement à Azure Site Recovery sans proxy**.
6. Acceptez l’emplacement par défaut du certificat utilisé pour chiffrer les données. Les données chiffrées sont déchiffrées pendant le basculement.
7. Dans **Synchroniser les métadonnées du cloud**, sélectionnez **Synchronisation des métadonnées du cloud dans le portail Site Recovery**. Cette action doit se ne produire qu’une seule fois sur chaque serveur. Sélectionnez ensuite **Inscription**.
8. Une fois que le serveur est inscrit dans le coffre, sélectionnez **Terminer**.

Une fois l’inscription terminée, les métadonnées du serveur sont récupérées par Azure Site Recovery et le serveur VMM s’affiche dans **Infrastructure Site Recovery**.

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>Installer l’agent Recovery Services sur les ordinateurs hôtes Hyper-V

Installez l’agent sur chaque hôte Hyper-V contenant des machines virtuelles que vous souhaitez répliquer.

1. Dans l’Assistant Installation de Microsoft Azure Recovery Services Agent > **vérification des prérequis**, sélectionnez **suivant**. Les composants requis manquants seront installés automatiquement.
2. Dans **paramètres d’Installation**, acceptez l’emplacement d’installation et l’emplacement du cache. Le lecteur de cache doit comporter au moins 5 Go d’espace de stockage. Nous recommandons un lecteur avec au moins 600 Go d’espace libre. Sélectionnez **Installer**.
3. Dans **Installation**, lorsque l’installation est terminée, sélectionnez **fermer** pour terminer l’Assistant.

    ![Installer l’agent](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

1. Sélectionnez **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources (**ContosoRG**) où créer les machines virtuelles Azure après le basculement.
3. Sélectionnez le **Resource Manager** modèle de déploiement.

Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

## <a name="configure-network-mapping"></a>Configurer le mappage réseau

1. Dans **Infrastructure Site Recovery** > **mappages réseau** > **mappage réseau**, sélectionnez le **+ mappage réseau** icône.
2. Dans la zone **Ajouter un mappage réseau**, sélectionnez le serveur VMM source. Sélectionnez **Azure** comme cible.
3. Vérifiez l’abonnement et le modèle de déploiement après le basculement.
4. Dans **Réseau Source**, sélectionnez le réseau de machines virtuelles locales sources.
5. Dans **Réseau cible**, choisissez le réseau Azure dans lequel les machines virtuelles Azure de réplica se trouveront, une fois créées après le basculement. Ensuite, sélectionnez **OK**.

    ![Mappage réseau](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

1. Sélectionnez **préparer l’infrastructure** > **paramètres de réplication** > **+ créer et associer**.
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie. Nous utilisons **ContosoReplicationPolicy**.
3. Laissez les paramètres par défaut et sélectionnez **OK**.
    - **Fréquence de copie** indique que, après la réplication initiale, les données delta répliquera toutes les cinq minutes.
    - **Rétention des points de récupération** indique que chaque point de récupération est conservée pendant deux heures.
    - **Fréquence des instantanés de cohérence des applications** indique que les points de récupération contenant des instantanés de cohérence des applications sont créés toutes les heures.
    - **Heure de début de la réplication initiale** indique que la réplication initiale démarre immédiatement.
    - **Chiffrer les données stockées sur Azure** est la valeur par défaut (**hors**) et indique que les données au repos dans Azure n’est pas chiffrées.
4. Une fois la stratégie est créée, sélectionnez **OK**. Lorsque vous créez une nouvelle stratégie, il est automatiquement associé avec le cloud VMM.

## <a name="enable-replication"></a>Activer la réplication

1. Dans **répliquer l’application**, sélectionnez **Source**.
2. Dans **Source**, sélectionnez le cloud VMM. Ensuite, sélectionnez **OK**.
3. Dans **cible**, vérifiez la cible (Azure), l’abonnement du coffre et sélectionnez le **Resource Manager** modèle.
4. Sélectionnez le **contosovmsacct1910171607** compte de stockage et le **ContosoASRnet** réseau Azure.
5. Dans **machines virtuelles** > **sélectionnez**, sélectionnez la machine virtuelle que vous souhaitez répliquer. Ensuite, sélectionnez **OK**.

   Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Après le **finaliser la Protection** travail terminé, la réplication initiale est terminée, et la machine virtuelle est prête pour le basculement.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Effectuer un test de récupération d’urgence](tutorial-dr-drill-azure.md)
