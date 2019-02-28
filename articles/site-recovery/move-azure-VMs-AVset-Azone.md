---
title: Utiliser le service Azure Site Recovery pour déplacer des machines virtuelles Azure IaaS vers une autre région Azure en tant que machines virtuelles rattachées à une zone | Microsoft Docs
description: Utilisez Azure Site Recovery pour déplacer des machines virtuelles Azure IaaS vers une autre région Azure en tant que machines virtuelles rattachées à une zone.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 77c38089a4f9c6518d9736df7f3020c66ad17b3b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877042"
---
# <a name="move-azure-vms-into-availability-zones"></a>Déplacer des machines virtuelles Azure vers des zones de disponibilité
Les zones de disponibilité dans Azure protègent les applications et les données contre les défaillances de centre de données. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un centre de refroidissement et d’un réseau indépendants. Pour garantir la résilience, il existe un minimum de trois zones distinctes dans toutes les régions activées. La séparation physique des Zones de disponibilité dans une région protège les applications et les données des défaillances dans le centre de données. Avec les Zones de disponibilité, Azure propose des contrats de niveau de service de durée de fonctionnement des machines virtuelles de pointe de 99,99 %. Les zones de disponibilité sont prises en charge dans certaines régions listées [ici](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). 

Si vous avez déployé vos machines virtuelles en tant qu’instance unique dans une région spécifique, et souhaitez maintenant améliorer la disponibilité des machines virtuelles existantes en les déplaçant vers une zone de disponibilité, cela est possible avec Azure Site Recovery. Il y a deux options :

- Déplacer des machines virtuelles à instance unique vers des zones de disponibilité dans une région cible
- Déplacer des machines virtuelles d’un groupe à haute disponibilité vers des zones de disponibilité dans une région cible

> [!IMPORTANT]
> Actuellement, Azure Site Recovery prend en charge le déplacement des machines virtuelles d’une région à une autre, mais pas le déplacement au sein d’une région. 

## <a name="verify-prerequisites"></a>Vérifier la configuration requise

- Vérifiez que la région cible [prend en charge les zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones). Assurez-vous aussi de choisir une [combinaison région source/région cible prise en charge](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) et de sélectionner la région cible appropriée.
- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](azure-to-azure-architecture.md).
- Examinez les [exigences et les limites de prise en charge](azure-to-azure-support-matrix.md).
- Vérifiez les autorisations du compte : Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication d’une machine virtuelle et copier éventuellement des données vers la cible à l’aide d’Azure Site Recovery, vous devez avoir les autorisations suivantes :

    1. Des autorisations pour créer une machine virtuelle dans les ressources Azure. Le rôle prédéfini « Contributeur de machines virtuelles » a ces autorisations, incluant :
        - L’autorisation de créer une machine virtuelle dans le groupe de ressources sélectionné
        - L’autorisation de créer une machine virtuelle dans le réseau virtuel sélectionné
        - L’autorisation d’écrire dans le compte de stockage sélectionné

    2. Vous devez également avoir l’autorisation de gérer les opérations Azure Site Recovery. Le rôle « Collaborateur Site Recovery » a toutes les autorisations nécessaires pour gérer les opérations Site Recovery dans un coffre Recovery Services.

## <a name="prepare-the-source-vms"></a>Préparer les machines virtuelles sources

1. Vos machines virtuelles doivent utiliser des disques managés pour pouvoir être déplacées vers une zone de disponibilité à l’aide de Site Recovery. Vous pouvez convertir des machines virtuelles Windows existantes qui utilisent des disques non managés afin qu’elles utilisent des disques managés. Pour cela, suivez les étapes décrites [ici](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks). Vérifiez au préalable que le groupe à haute disponibilité est configuré en tant que groupe managé. 
2. Assurez-vous que tous les certificats racines les plus récents sont présents sur les machines virtuelles Azure à déplacer. À défaut, la copie de données vers la région cible ne peut pas être activée en raison des contraintes de sécurité.

3. Pour les machines virtuelles Windows, installez-y toutes les mises à jour de Windows les plus récentes afin que tous les certificats racines approuvés s’y trouvent. Dans un environnement déconnecté, suivez les processus Windows Update et de mise à jour de certificat standard en vigueur pour votre organisation.

4. Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.
5. N’utilisez pas de proxy d’authentification dans le but de contrôler la connectivité réseau pour les machines virtuelles que vous voulez déplacer.

6. Si la machine virtuelle que vous voulez déplacer n’a pas d’accès à Internet et utilise un proxy de pare-feu pour contrôler l’accès sortant, vérifiez la configuration requise détaillée [ici](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

7. Identifiez la topologie du réseau source et toutes les ressources que vous utilisez actuellement, entre autres les équilibreurs de charge, les groupes de sécurité réseau, l’IP publique, etc., pour la vérification.

## <a name="prepare-the-target-region"></a>Préparer la région cible

1. Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible utilisée pour la récupération d’urgence. Contactez le support technique pour activer le quota nécessaire au besoin.

2. Assurez-vous que votre abonnement dispose de suffisamment de ressources pour prendre en charge des machines virtuelles de tailles correspondant à vos machines virtuelles source. Si vous l’utilisez pour copier des données vers la cible, Site Recovery choisit une machine virtuelle cible de la même taille ou de la taille la plus proche possible.

3. Veillez à créer une ressource cible pour chaque composant identifié dans la topologie du réseau source. Cette étape est importante pour garantir que vos machines virtuelles, après leur déplacement final vers la région cible, offriront toutes les fonctionnalités et capacités qui étaient disponibles dans la région source.

    > [!NOTE]
    > Azure Site Recovery détecte et crée automatiquement un réseau virtuel et un compte de stockage quand vous activez la réplication de la machine virtuelle source. Vous pouvez aussi précréer ces ressources et les affecter à la machine virtuelle au moment de l’étape d’activation de la réplication. En revanche, pour toutes les autres ressources, comme mentionné ci-dessous, vous devez les créer manuellement dans la région cible.

     Consultez la documentation suivante pour créer les ressources réseau courantes dont vous avez besoin, en fonction de la configuration de la machine virtuelle source.

    - [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Équilibreurs de charge] (https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials
        
     - [Adresse IP publique](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Pour tous les autres composants réseau, reportez-vous à cette [documentation](https://docs.microsoft.com/azure/#pivot=products&panel=network) sur la mise en réseau. 

> [!IMPORTANT]
> Veillez à utiliser un équilibreur de charge redondant interzone dans la cible. Vous trouverez plus d’informations [ici](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).

4. Vous devez [créer un réseau hors production](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellement dans la région cible si vous souhaitez tester la configuration avant d’effectuer le déplacement final vers la région cible. Cette pratique est recommandée dans la mesure où elle limite les interférences avec l’environnement de production.


> [!NOTE]
> Les étapes suivantes s’appliquent à une seule machine virtuelle. Si vous souhaitez les appliquer à plusieurs machines virtuelles, accédez au coffre Recovery Services, cliquez sur + Répliquer et sélectionnez toutes les machines virtuelles concernées.

## <a name="enable-replication"></a>Activer la réplication
Les étapes ci-dessous vous montrent comment utiliser Azure Site Recovery pour activer la réplication de données dans la région cible, avant d’effectuer leur déplacement final vers les zones de disponibilité.

1. Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle que vous souhaitez déplacer vers les zones de disponibilité.
2. Dans **Opérations**, cliquez sur **Récupération d’urgence**.
3. Dans **Configurer la récupération d’urgence** > **Région cible**, sélectionnez la région cible vers laquelle vous allez effectuer la réplication. Vérifiez que cette région [prend en charge](https://docs.microsoft.com/azure/availability-zones/az-overview#regions-that-support-availability-zones) les zones de disponibilité.
![enable-rep-1.PNG](media/azure-vms-to-zones/enable-rep-1.PNG)

1. Sélectionnez **Suivant : Paramètres avancés**
2. Choisissez les valeurs appropriées pour l’abonnement, le groupe de ressources de machine virtuelle et le réseau virtuel cibles.
3. Dans la section **Disponibilité**, choisissez la zone de disponibilité vers laquelle vous souhaitez déplacer la machine virtuelle. 
> [!NOTE]
> Si vous ne voyez pas l’option Groupe à haute disponibilité ou Zone de disponibilité, vérifiez que les [prérequis](#prepare-the-source-vms) sont remplis et que la [préparation](#prepare-the-source-vms) des machines virtuelles sources est terminée.

   ![enable-rep-2.PNG](media/azure-vms-to-zones/enable-rep-2.PNG)

7. Cliquez sur Activer la réplication. Cette opération démarre un travail consistant à activer la réplication pour la machine virtuelle.

## <a name="verify-settings"></a>Vérifier les paramètres

Une fois le travail de réplication terminé, vous pouvez vérifier l’état de la réplication, modifier les paramètres de réplication et tester le déploiement.

1. Dans le menu Machine virtuelle, cliquez sur **Récupération d’urgence**.
2. Vous pouvez vérifier l’intégrité de la réplication, les points de récupération qui ont été créés ainsi que les régions sources et cibles sur la carte.

   ![État de la réplication](media/azure-to-azure-quickstart/replication-status.png)

## <a name="test-the-configuration"></a>Tester la configuration

1. Dans le menu de la machine virtuelle, cliquez sur **Reprise d’activité après sinistre**.
2. Cliquez sur l’icône **Test de basculement**.
3. Dans **Test de basculement**, sélectionnez un point de récupération à utiliser pour le basculement :

   - **Dernier point traité** : bascule la machine virtuelle vers le dernier point de récupération qui a été traité par le service Site Recovery. L’horodatage est affiché. Cette option, qui n’implique aucun traitement de données, offre un objectif de délai de récupération faible.
   - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché.
   - **Personnalisé** : sélectionnez n’importe quel point de récupération.

3. Sélectionnez le réseau virtuel Azure cible vers lequel vous voulez déplacer les machines virtuelles Azure afin d’y tester la configuration. 

> [!IMPORTANT]
> Nous vous recommandons d’utiliser un réseau machines virtuelles Azure distinct pour faire le test de basculement, et pas le réseau de production dans la région cible vers laquelle vous comptez déplacer vos machines virtuelles au final.

4. Pour démarrer le test du déplacement, cliquez sur **OK**. Pour suivre la progression, cliquez sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez également cliquer sur le travail **Test de basculement** dans le nom du coffre > **Paramètres** > **Travaux** > **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Vérifiez que la machine virtuelle est en cours d’exécution, qu’elle est correctement dimensionnée et qu’elle est connectée au réseau approprié.
6. Si vous souhaitez supprimer la machine virtuelle qui a été créée pour les besoins du test du déplacement, cliquez sur **Nettoyer le test de basculement** sur l’élément répliqué. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Effectuer et valider le déplacement vers la région cible

1.  Dans le menu de la machine virtuelle, cliquez sur **Reprise d’activité après sinistre**.
2. Cliquez sur l’icône **Basculement**.
3. Dans **Basculement**, sélectionnez **Dernier**. 
4. Sélectionnez **Arrêtez la machine avant de commencer le basculement**. Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**. 
5. Une fois que la tâche est terminée, vérifiez que la machine virtuelle apparaît bien dans la région Azure cible choisie.
6. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Valider**. Cette étape termine le processus de déplacement vers la région cible. Attendez la fin de la tâche de validation.

## <a name="discard-the-resource-in-the-source-region"></a>Supprimer les ressources dans la région source 

1. Accédez à la machine virtuelle.  Cliquez sur **Désactiver la réplication**.  Cela met fin au processus de copie des données pour la machine virtuelle.  

> [!IMPORTANT]
> Il est important d’effectuer cette étape pour arrêter la facturation liée à la réplication Azure Site Recovery une fois le déplacement terminé. Les paramètres de réplication source sont automatiquement nettoyés. Notez que l’extension Site Recovery installée dans le cadre de la réplication n’est pas supprimée et doit être supprimée manuellement. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez amélioré la disponibilité d’une machine virtuelle Azure en la déplaçant vers un groupe à haute disponibilité ou une zone de disponibilité. Vous pouvez maintenant configurer la reprise d’activité après sinistre pour la machine virtuelle que vous avez déplacée.

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence après la migration](azure-to-azure-quickstart.md)


