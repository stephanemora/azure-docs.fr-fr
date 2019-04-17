---
title: Préparer Azure pour la récupération d’urgence des machines locales avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment préparer Azure pour la récupération d’urgence des machines locales à l’aide d’Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6e826bd965281d60cb6d73f325fbc5a7a06da234
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358491"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Préparer les ressources Azure pour la récupération d’urgence de machines locales

Cet article explique comment préparer les ressources et les composants Azure pour pouvoir configurer la reprise d’activité des machines virtuelles VMware, des machines virtuelles Hyper-V ou des serveurs physiques Windows/Linux locaux sur Azure, à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Cet article est le premier tutoriel d’une série qui vous montre comment configurer la récupération d’urgence pour des machines virtuelles locales. 


Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifiez que le compte Azure dispose d’autorisations de réplication.
> * Créez un coffre Recovery Services. Un coffre conserve des métadonnées et des informations de configuration pour les machines virtuelles et les autres composants de réplication.
> * Configurer un réseau virtuel Azure (VNet). Une fois les machines virtuelles Azure créées après le basculement, elles sont jointes à ce réseau.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour obtenir des instructions détaillées, consultez l’article de la section Procédures dans la table des matières de Site Recovery.

## <a name="before-you-start"></a>Avant de commencer

- Examinez l’architecture de la reprise d’activité des [machines virtuelles VMware](vmware-azure-architecture.md), des [machines virtuelles Hyper-V](hyper-v-azure-architecture.md) et des [serveurs physiques](physical-azure-architecture.md).
- Lisez les questions courantes sur [VMware](vmware-azure-common-questions.md) et Hyper-V (hyper-v-azure-common-questions.md)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer. Puis, connectez-vous au [portail Azure](https://portal.azure.com).


## <a name="verify-account-permissions"></a>Vérifier les autorisations du compte

Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement et vous disposez des autorisations nécessaires. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication pour une nouvelle machine virtuelle, vous devez avoir l’autorisation d’effectuer les actions ci-après :

- Créer une machine virtuelle dans le groupe de ressources sélectionné
- Créer une machine virtuelle dans le réseau virtuel sélectionné
- Écrire sur un compte de stockage Azure.
- Écrire sur un disque managé Azure.

Pour effectuer ces tâches, le rôle prédéfini de contributeur de machines virtuelles doit être assigné à votre compte. De plus, pour gérer les opérations Site Recovery dans un coffre, le rôle prédéfini de contributeur Site Recovery doit être assigné à votre compte.


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

1. Dans le portail Azure, cliquez sur **Créer une ressource** et recherchez **Recovery Services** dans la Place de marché.
2. Cliquez sur **Backup and Site Recovery (OMS)**, puis dans la page éponyme, cliquez sur **Créer**. 
1. Dans le **coffre Recovery Services** > **Nom**, entrez un nom convivial permettant d’identifier le coffre. Pour cette série de didacticiels, nous utilisons **ContosoVMVault**.
2. Dans **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un. Pour ce tutoriel, nous utilisons **contosoRG**.
3. Dans **Emplacement**, sélectionnez la région dans laquelle le coffre doit se trouver. Nous utilisons **Europe Ouest**.
4. Pour accéder rapidement au coffre à partir du tableau de bord, sélectionnez **Épingler au tableau de bord** > **Créer**.

   ![Créer un coffre](./media/tutorial-prepare-azure/new-vault-settings.png)

   Le nouveau coffre apparaît dans **Tableau de bord** > **Toutes les ressources** et dans la page principale **Coffres Recovery Services**.

## <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Les machines locales sont répliquées sur des disques managés Azure. Durant le basculement, les machines virtuelles Azure sont créées à partir de ces disques managés et jointes au réseau Azure spécifié dans cette procédure.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
2. Laissez **Resource Manager** sélectionné comme modèle de déploiement.
3. Dans **Nom**, entrez un nom de réseau. Le nom doit être unique au sein du groupe de ressources Azure. Dans ce tutoriel, nous utilisons **ContosoASRnet**.
4. Spécifiez le groupe de ressources dans lequel le réseau sera créé. Nous utilisons le groupe de ressources existant **contosoRG**.
5. Dans **Plage d’adresses**, entrez la plage du réseau. Nous utilisons **10.0.0.0/24** sans utiliser de sous-réseau.
6. Dans **Abonnement**, sélectionnez l’abonnement dans lequel créer le réseau.
7. Dans **Emplacement**, sélectionnez la même région que celle dans laquelle le coffre Recovery Services a été créé. Dans notre tutoriel, il s’agit de la région **Europe Ouest**.  Ce réseau doit se trouver dans la même région que le coffre.
8. Nous laissons les options par défaut de la protection DDoS de base, sans point de terminaison de service sur le réseau.
9. Cliquez sur **Créer**.

   ![Créez un réseau virtuel](media/tutorial-prepare-azure/create-network.png)

La création du réseau virtuel prend quelques secondes. Une fois qu’il est créé, vous le voyez dans le tableau de bord du portail Azure.




## <a name="next-steps"></a>Étapes suivantes

- Pour la reprise d’activité VMware, [préparez l’infrastructure VMware locale](tutorial-prepare-on-premises-vmware.md).
- Pour la reprise d’activité Hyper-V, [préparez les serveurs Hyper-V locaux](hyper-v-prepare-on-premises-tutorial.md).
- Pour la reprise d’activité du serveur physique, [configurez le serveur de configuration et l’environnement source](physical-azure-disaster-recovery.md)
- [En savoir plus](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sur les réseaux Azure.
- [En savoir plus](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) sur les disques managés.
