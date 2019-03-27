---
title: Préparer Azure pour la récupération d’urgence des machines locales avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment préparer Azure pour la récupération d’urgence des machines locales à l’aide d’Azure Site Recovery.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/03/2019
ms.author: mayg
ms.custom: MVC
ms.openlocfilehash: 5168fc28952631f00c2415d6bc171a130dc85dfd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838559"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Préparer les ressources Azure pour la récupération d’urgence de machines locales

 [Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.

Cet article est le premier tutoriel d’une série qui vous montre comment configurer la récupération d’urgence pour des machines virtuelles locales. Il ne s’applique que si vous protégez des machines virtuelles VMware locales, des machines virtuelles Hyper-V ou des serveurs physiques.

> [!NOTE]
> Les tutoriels sont conçus pour vous montrer le chemin de déploiement le plus simple pour un scénario. Ils utilisent les options par défaut lorsque cela est possible et n’affichent pas tous les paramètres et chemins d’accès possibles. Pour obtenir des instructions détaillées, reportez-vous à la section **Conseils pratiques** du scénario correspondant.

Cet article décrit comment préparer les composants Azure pour répliquer des machines virtuelles (Hyper-V ou VMware) ou des serveurs physiques Windows/Linux locaux sur Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifiez que votre compte Azure dispose des autorisations de réplication.
> * Créez un coffre Recovery Services. Un coffre conserve des métadonnées et des informations de configuration pour les machines virtuelles et les autres composants de réplication.
> * Configurez un réseau Azure Quand les machines virtuelles Azure sont créées après le basculement, elles sont jointes à ce réseau Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="verify-account-permissions"></a>Vérifier les autorisations du compte

Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication pour une nouvelle machine virtuelle, vous devez avoir l’autorisation d’effectuer les actions ci-après :

- Créer une machine virtuelle dans le groupe de ressources sélectionné
- Créer une machine virtuelle dans le réseau virtuel sélectionné
- Écrire dans un compte de stockage.
- Écrire sur le disque managé.

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

Quand les machines virtuelles Azure sont créées à partir de disques managés après le basculement, elles sont jointes à ce réseau.

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
- [En savoir plus](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) sur les disques managés.



## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Préparer l’infrastructure VMware locale pour la récupération d’urgence dans Azure](tutorial-prepare-on-premises-vmware.md)
