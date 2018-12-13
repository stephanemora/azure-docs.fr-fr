---
title: Préparer Azure pour la récupération d’urgence des machines locales avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment préparer Azure pour la récupération d’urgence des machines locales à l’aide d’Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5c5699d6e25b1f12f87e389011d044315baccd4d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838869"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Préparer les ressources Azure pour la récupération d’urgence de machines locales

 [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Cet article est le premier tutoriel d’une série qui vous montre comment configurer la récupération d’urgence pour des machines virtuelles locales. Il ne s’applique que si vous protégez des machines virtuelles VMware locales, des machines virtuelles Hyper-V ou des serveurs physiques.

> [!NOTE]
> Les tutoriels sont conçus pour vous montrer le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour obtenir des instructions détaillées, reportez-vous à la section **Conseils pratiques** du scénario correspondant.

Cet article décrit comment préparer les composants Azure pour répliquer des machines virtuelles (Hyper-V ou VMware) ou des serveurs physiques Windows/Linux locaux sur Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifiez que votre compte Azure dispose des autorisations de réplication.
> * Créez un compte de stockage Azure. Les images des machines répliquées y sont stockées.
> * Créez un coffre Recovery Services. Un coffre conserve des métadonnées et des informations de configuration pour les machines virtuelles et les autres composants de réplication.
> * Configurez un réseau Azure Quand les machines virtuelles Azure sont créées après le basculement, elles sont jointes à ce réseau Azure.

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

Les images des machines répliquées sont conservées dans le stockage Azure. Les machines virtuelles Azure sont créées à partir du stockage quand vous basculez du site local vers Azure. Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services. Nous utilisons la région Europe Ouest dans ce tutoriel.

1. Dans le menu [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage - blob, fichier, table, file d’attente**.
2. Dans **Créer un compte de stockage**, entrez un nom correspondant au compte. Pour ces didacticiels, nous utilisons **contosovmsacct1910171607**. Le nom que vous sélectionnez doit être unique dans Azure, avoir entre 3 et 24 caractères, et contenir uniquement des nombres et des lettres minuscules.
3. Dans **Modèle de déploiement**, sélectionnez **Resource Manager**.
4. Dans **Type de compte**, sélectionnez **Stockage (Usage général v1)**. Ne sélectionnez pas Stockage Blob.
5. Dans **Réplication**, sélectionnez la valeur par défaut **Stockage géo-redondant avec accès en lecture** pour la redondance de stockage. Laissez **Transfert sécurisé requis** en tant que **Désactivé**.
6. Dans **Performances**, sélectionnez **Standard**, et dans **Niveau d’accès**, choisissez l’option par défaut **Chaud**.
7. Dans **Abonnement**, sélectionnez l’abonnement dans lequel vous souhaitez créer le compte de stockage.
8. Dans **Groupe de ressources**, entrez un nouveau groupe de ressources. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Pour ces tutoriels, nous utilisons **ContosoRG**.
9. Dans **Emplacement**, sélectionnez un emplacement géographique pour votre compte de stockage. 

   ![Créez un compte de stockage.](media/tutorial-prepare-azure/create-storageacct.png)

9. Sélectionnez **Créer** pour créer le compte de stockage.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Stockage** > **Backup and Site Recovery (OMS)**.
2. Dans **Name**, entrez un nom convivial pour identifier le coffre. Pour cette série de didacticiels, nous utilisons **ContosoVMVault**.
3. Dans **Groupe de ressources**, nous utilisons **contosoRG**.
4. Dans **Emplacement**. Nous utilisons **Europe Ouest**.
5. Pour accéder rapidement au coffre à partir du tableau de bord, sélectionnez **Épingler au tableau de bord** > **Créer**.

   ![Créer un coffre](./media/tutorial-prepare-azure/new-vault-settings.png)

   Le nouveau coffre apparaît dans **Tableau de bord** > **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Quand les machines virtuelles Azure sont créées à partir du stockage après le basculement, elles sont jointes à ce réseau.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
2. Laissez **Gestionnaire des ressources** sélectionné en tant que modèle de déploiement.
3. Dans **Nom**, entrez un nom de réseau. Le nom doit être unique au sein du groupe de ressources Azure. Dans ce tutoriel, nous utilisons **ContosoASRnet**.
4. Spécifiez le groupe de ressources dans lequel le réseau sera créé. Nous utilisons le groupe de ressources existant **contosoRG**.
5. Dans **Plage d’adresses**, entrez la plage du réseau **10.0.0.0/24**. Dans ce réseau, nous utilisons pas de sous-réseau.
6. Dans **Abonnement**, sélectionnez l’abonnement dans lequel créer le réseau.
7. Dans **Emplacement**, sélectionnez **Europe Ouest**. Ce réseau doit se trouver dans la même région que le coffre Recovery Services.
8. Nous laissons les options par défaut de la protection DDoS de base, sans point de terminaison de service sur le réseau.
9. Cliquez sur **Créer**.

   ![Créez un réseau virtuel](media/tutorial-prepare-azure/create-network.png)

   La création du réseau virtuel prend quelques secondes. Une fois qu’il est créé, vous le voyez dans le tableau de bord du portail Azure.

## <a name="useful-links"></a>Liens utiles

- [En savoir plus](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sur les réseaux Azure.
- [En savoir plus](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts) sur les types de stockage Azure.
- [En savoir plus](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs#read-access-geo-redundant-storage) sur la redondance du stockage et le [transfert sécurisé](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) pour le stockage.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Préparer l’infrastructure VMware locale pour la récupération d’urgence dans Azure](tutorial-prepare-on-premises-vmware.md)
