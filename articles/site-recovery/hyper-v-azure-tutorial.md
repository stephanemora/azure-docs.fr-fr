---
title: Configurer la récupération d’urgence des machines virtuelles Hyper-V locales (sans VMM) dans Azure avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment configurer la récupération d’urgence de machines virtuelles Hyper-V locales (sans VMM) dans Azure avec le service Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: da643a4d7a1dc74385b3854c1952af5ba93bd241
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358100"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Configurer la récupération d’urgence de machines virtuelles Hyper-V locales vers Azure

c

> [!div class="checklist"]
> * Sélectionner la source et la cible de réplication.
> * Configurer l’environnement de réplication source, y compris les composants Site Recovery locaux, et l’environnement de réplication cible.
> * Créer une stratégie de réplication.
> * Activer la réplication pour une machine virtuelle.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour obtenir des instructions détaillées, consultez l’article de la section Procédures dans la table des matières de Site Recovery.

## <a name="before-you-begin"></a>Avant de commencer
Il s’agit du troisième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents :

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer un serveur Hyper-V local](tutorial-prepare-on-premises-hyper-v.md)



## <a name="select-a-replication-goal"></a>Sélectionner un objectif de réplication

1. Dans **Archivages de Recovery Services**, sélectionnez l’archivage. Nous avons préparé le coffre-fort **ContosoVMVault** au cours du tutoriel précédent.
2. Dans **Prise en main**, cliquez sur **Site Recovery**. Cliquez ensuite sur **Préparer l’infrastructure**
3. Dans **Objectif de protection** > **Où se trouvent vos machines ?**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines ?**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées ?**, sélectionnez **Oui, avec Hyper-V**.
6. Dans **Utilisez-vous System Center VMM pour gérer vos hôtes Hyper-V**, sélectionnez **Non**. Cliquez ensuite sur **OK**.

    ![Objectif de réplication](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Confirmer la planification d’un déploiement

1. Dans **Planification du déploiement**, si vous planifiez un déploiement de grande envergure, téléchargez l’outil Planificateur de déploiement pour Hyper-V à partir du lien figurant dans la page. [En savoir plus](hyper-v-deployment-planner-overview.md) sur la planification du déploiement Hyper-V.
2. Pour ce tutoriel, nous n’avons pas besoin de l’outil Planificateur de déploiement. Dans **Avez-vous effectué la planification du déploiement ?**, sélectionnez **Je le ferai plus tard**. Cliquez ensuite sur **OK**.

    ![Planification de déploiement](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Pour configurer l’environnement source, créez un site Hyper-V et ajoutez des hôtes Hyper-V contenant les machines virtuelles à répliquer sur le site. Puis vous téléchargez et installez le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur chaque hôte et inscrivez le site Hyper-V dans le coffre. 

1. Sous **Préparer l’infrastructure**, cliquez sur **Source**.
2. Dans **Préparer la source**, cliquez sur **+Site Hyper-V**.
3. Dans **Créer un site Hyper-V**, spécifiez le nom du site. Nous utilisons **ContosoHyperVSite**.

    ![Site Hyper-V](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. Une fois le site créé, dans **Préparer la source** > **Étape 1 : Sélectionner le site Hyper-V**, sélectionnez le site que vous avez créé.
4. Cliquez sur **+Serveur Hyper-V**.

    ![Serveur Hyper-V](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. Téléchargez le programme d’installation du fournisseur Microsoft Azure Site Recovery.
6. Téléchargez la clé d’inscription du coffre. Vous devez avoir cette clé pour installer le fournisseur. Une fois générée, la clé reste valide pendant 5 jours.

    ![Télécharger le fournisseur](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installer le fournisseur

Installez le fichier de configuration téléchargé (AzureSiteRecoveryProvider.exe) sur chaque hôte Hyper-V à ajouter au site Hyper-V. Le programme d’installation installe le fournisseur Azure Site Recovery et l’agent Recovery Services sur chaque hôte Hyper-V.

1. Exécutez le fichier de configuration.
2. Dans l’Assistant Installation du fournisseur Azure Site Recovery > **Microsoft Update**, choisissez d’utiliser Microsoft Update pour rechercher les mises à jour du fournisseur.
3. Dans **Installation**, acceptez l’emplacement d’installation par défaut pour le fournisseur et l’agent, et cliquez sur **Installer**.
4. Après l’installation, dans l’Assistant Inscription de Microsoft Azure Site Recovery > **Paramètres de coffre**, cliquez sur **Parcourir** et, dans **Fichier de clé**, sélectionnez le fichier de clé de coffre que vous avez téléchargé. 
5. Spécifiez l’abonnement Azure Site Recovery, le nom du coffre (**ContosoVMVault**) et le site Hyper-V (**ContosoHyperVSite**) auquel appartient le serveur Hyper-V.
6. Dans **Paramètres de proxy**, sélectionnez **Se connecter directement à Azure Site Recovery sans proxy**.
7. Dans **Inscription**, une fois que le serveur est inscrit dans le coffre, cliquez sur **Terminer**.

Les métadonnées du serveur Hyper-V sont récupérées par Azure Site Recovery et le serveur s’affiche dans **Infrastructure Site Recovery** > **Hôtes Hyper-V**. Ce processus peut prendre jusqu’à 30 minutes.        

#### <a name="install-on-hyper-v-core-server"></a>Installer sur un serveur Hyper-V (installation minimale)

Si vous exécutez un serveur Hyper-V (installation minimale), téléchargez le fichier d’installation et procédez comme suit :

1. Extrayez les fichiers d’AzureSiteRecoveryProvider.exe dans un répertoire local en exécutant

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2.  Exécutez « .\setupdr.exe /i ». Les résultats sont journalisés dans %Programdata%\ASRLogs\DRASetupWizard.log.

3.  Inscrivez le serveur avec cette commande :

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```
 

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles. 

1. Cliquez sur **Préparer l’infrastructure** > **Cible**.
2. Sélectionnez l’abonnement et le groupe de ressources **ContosoRG** où créer les machines virtuelles Azure après le basculement.
3. Sélectionnez le modèle de déploiement **Resource Manager**.

Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.


## <a name="set-up-a-replication-policy"></a>Configurer une stratégie de réplication

1. Cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie. Nous utilisons **ContosoReplicationPolicy**.
3. Pour ce tutoriel, nous allons laisser les paramètres par défaut.
    - **Fréquence de copie** indique la fréquence de réplication des données delta (après la réplication initiale), toutes les cinq minutes par défaut.
    - **Rétention des points de récupération** indique que les points de récupération vont être conservés pendant deux heures.
    - **Fréquence des instantanés de cohérence des applications** indique que les points de récupération contenant des instantanés de cohérence des applications sont créés toutes les heures.
    - **Heure de début de la réplication initiale** indique que la réplication initiale démarre immédiatement.
4. Après avoir créé la stratégie, cliquez sur **OK**. Quand vous créez une stratégie, elle est automatiquement associée au site Hyper-V spécifié (dans notre tutoriel, il s’agit de **ContosoHyperVSite**).

    ![Stratégie de réplication](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>Activer la réplication


1. Dans **Répliquer l’application**, cliquez sur **Source**. 
2. Dans **Source**, sélectionnez le site **ContosoHyperVSite**. Cliquez ensuite sur **OK**.
3. Dans **Cible**, vérifiez qu’Azure est la cible, et vérifiez l’abonnement du coffre et le modèle de déploiement **Resource Manager**.
4. Si vous utilisez les paramètres du tutoriel, sélectionnez le compte de stockage **contosovmsacct1910171607** créé dans le tutoriel précédent pour les données répliquées, et le réseau **ContosoASRnet** où trouver les machines virtuelles Azure après le basculement.
5. Dans **Machines virtuelles** > **Sélectionner**, sélectionnez les machines virtuelles à répliquer. Cliquez ensuite sur **OK**.

   Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Lorsque le travail de **finalisation de la protection** est terminé, la réplication initiale est également terminée et la machine virtuelle est prête à être basculée.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Effectuer un test de récupération d’urgence](tutorial-dr-drill-azure.md)
