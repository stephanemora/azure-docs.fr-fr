---
title: Créer des ressources pour une utilisation avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment préparer Azure pour la réplication de machines locales à l’aide d’Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/02/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852c854de9feb9bcc98fc89aa9340b93f2c4e8d3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
---
# <a name="prepare-azure-resources-for-replication-of-on-premises-machines"></a>Préparer des ressources Azure pour la réplication de machines locales

 [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Ce didacticiel décrit comment préparer les composants Azure pour répliquer des machines virtuelles (Hyper-V ou VMware) ou des serveurs physiques Windows/Linux locaux sur Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifiez que votre compte Azure dispose des autorisations de réplication.
> * Créez un compte de stockage Azure. Les données répliquées y sont stockées.
> * Créez un coffre Recovery Services.
> * Définissez un réseau Azure. Quand les machines virtuelles Azure sont créées après le basculement, elles sont jointes à ce réseau Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](http://portal.azure.com).

## <a name="verify-account-permissions"></a>Vérifier les autorisations du compte

Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication pour une nouvelle machine virtuelle, vous devez avoir l’autorisation d’effectuer les actions ci-après :

- Créer une machine virtuelle dans le groupe de ressources sélectionné
- Créer une machine virtuelle dans le réseau virtuel sélectionné
- Écrire dans le compte de stockage sélectionné

Pour effectuer ces tâches, le rôle prédéfini de contributeur de machines virtuelles doit être assigné à votre compte. De plus, pour gérer les opérations Site Recovery dans un coffre, le rôle prédéfini de contributeur Site Recovery doit être assigné à votre compte.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.

Les images des machines répliquées sont conservées dans le stockage Azure. Les machines virtuelles Azure sont créées à partir du stockage quand vous basculez du site local vers Azure.

1. Dans le menu [Portail Azure](https://portal.azure.com), sélectionnez **Nouveau** > **Stockage** > **Compte de stockage**.
2. Dans **Créer un compte de stockage**, entrez un nom correspondant au compte. Pour ces didacticiels, utilisez le nom **contosovmsacct1910171607**. Le nom doit être unique dans Azure, avoir entre 3 et 24 caractères, et contenir uniquement des nombres et des lettres minuscules.
3. Dans **Modèle de déploiement**, sélectionnez **Resource Manager**.
4. Dans **Type de compte**, sélectionnez **Usage général**. Dans **Performances**, sélectionnez **Standard**. Ne sélectionnez pas Stockage Blob.
5. Dans **Réplication**, sélectionnez la valeur par défaut **Stockage géo-redondant avec accès en lecture** pour la redondance de stockage.
6. Dans **Abonnement**, sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.
7. Dans **Groupe de ressources**, entrez un nouveau groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Pour ces didacticiels, utilisez le nom **ContosoRG**.
8. Dans **Emplacement**, sélectionnez un emplacement géographique pour votre compte de stockage. Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services. Pour ces didacticiels, utilisez la région **Europe de l’Ouest**.

   ![Créez un compte de stockage.](media/tutorial-prepare-azure/create-storageacct.png)

9. Sélectionnez **Créer** pour créer le compte de stockage.

## <a name="create-a-vault"></a>création d'un coffre

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Surveillance + Gestion** > **Backup and Site Recovery**.
2. Dans **Name**, entrez un nom convivial pour identifier le coffre. Pour ce didacticiel, utilisez **ContosoVMVault**.
3. Dans **Groupe de ressources**, sélectionnez le groupe de ressources existant nommé **contosoRG**.
4. Dans **Emplacement**, spécifiez la région Azure **Europe de l’Ouest** qui est utilisée dans cette série de didacticiels.
5. Pour accéder rapidement au coffre à partir du tableau de bord, sélectionnez **Épingler au tableau de bord** > **Créer**.

   ![Créer un coffre](./media/tutorial-prepare-azure/new-vault-settings.png)

   Le nouveau coffre apparaît dans **Tableau de bord** > **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Quand les machines virtuelles Azure sont créées à partir du stockage après le basculement, elles sont jointes à ce réseau.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
2. Laissez **Resource Manager** sélectionné comme modèle de déploiement. Resource Manager est le modèle de déploiement préféré. Puis effectuez les étapes suivantes :

   a. Dans **Nom**, entrez un nom de réseau. Le nom doit être unique au sein du groupe de ressources Azure. Utilisez le nom **ContosoASRnet**.

   b. Dans **Groupe de ressources**, utilisez le groupe de ressources existant **contosoRG**.

   c. Dans **Plage d’adresses**, entrez la plage d’adresses réseau **10.0.0.0/24**.

   d. Pour ce didacticiel, vous n’avez pas besoin d’un sous-réseau.

   e. Dans **Abonnement**, sélectionnez l’abonnement dans lequel créer le réseau.

   f. Dans **Emplacement**, sélectionnez **Europe de l’Ouest**. Ce réseau doit se trouver dans la même région que le coffre Recovery Services.

3. Sélectionnez **Créer**.

   ![Créez un réseau virtuel](media/tutorial-prepare-azure/create-network.png)

   La création du réseau virtuel prend quelques secondes. Une fois qu’il est créé, vous le voyez dans le tableau de bord du portail Azure.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Préparer l’infrastructure VMware locale pour la récupération d’urgence dans Azure](tutorial-prepare-on-premises-vmware.md)
