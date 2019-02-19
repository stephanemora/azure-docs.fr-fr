---
title: Déplacer des machines virtuelles IaaS Azure vers une autre région Azure à l’aide du service Azure Site Recovery | Microsoft Docs
description: Utilisez Azure Site Recovery pour déplacer des machines virtuelles IaaS Azure d’une région Azure à l’autre.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: f6713326045ebd84f1cd484803fbc725ad798d7b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882265"
---
# <a name="move-azure-vms-to-another-region"></a>Déplacer des machines virtuelles Azure vers une autre région

Vous pouvez avoir besoin de déplacer vos machines virtuelles Azure IaaS existantes d’une région à une autre pour diverses raisons, comme améliorer la fiabilité et la disponibilité des machines virtuelles existantes, faciliter la gestion, répondre à des besoins de gouvernance, etc., comme cela est décrit [ici](azure-to-azure-move-overview.md). En plus de l’utilisation du service [Azure Site Recovery](site-recovery-overview.md) pour gérer et orchestrer la reprise d’activité après sinistre des ordinateurs locaux et des machines virtuelles Azure dans le cadre de la continuité d’activité et de la reprise d’activité, vous pouvez utiliser Site Recovery pour gérer le déplacement des machines virtuelles Azure vers une région secondaire.       

Ce tutoriel vous montre comment déplacer des machines virtuelles Azure vers une autre région à l’aide d’Azure Site Recovery. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * [Vérifier la configuration requise](#verify-prerequisites)
> * [Préparer les machines virtuelles sources](#prepare-the-source-vms)
> * [Préparer la région cible](#prepare-the-target-region)
> * [Copier des données vers la région cible](#copy-data-to-the-target-region)
> * [Tester la configuration](#test-the-configuration) 
> * [Effectuer le déplacement](#perform-the-move-to-the-target-region-and-confirm) 
> * [Supprimer les ressources dans la région source](#discard-the-resource-in-the-source-region) 

> [!IMPORTANT]
> Ce document explique comment déplacer des machines virtuelles Azure d’une région à une autre en l’état. Si votre objectif est d’améliorer la disponibilité des machines virtuelles d’un groupe à haute disponibilité en les déplaçant vers une autre région pour les déployer en tant que machines virtuelles rattachées à une zone, consultez le tutoriel fourni [ici](move-azure-VMs-AVset-Azone.md).

## <a name="verify-prerequisites"></a>Vérifier la configuration requise

- Vérifiez que vous disposez de machines virtuelles Azure dans la région Azure à partir de laquelle vous souhaitez effectuer le déplacement.
- Assurez-vous de choisir une [combinaison région source/région cible prise en charge](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) et de sélectionner la région cible appropriée.
- Assurez-vous que vous comprenez [l’architecture et les composants du scénario](azure-to-azure-architecture.md).
- Examinez les [exigences et les limites de prise en charge](azure-to-azure-support-matrix.md).
- Vérifiez les autorisations du compte : Si vous venez de créer votre compte Azure gratuit, vous êtes l’administrateur de votre abonnement. Si vous n’êtes pas administrateur de l’abonnement, collaborez avec l’administrateur pour affecter les autorisations dont vous avez besoin. Pour activer la réplication d’une machine virtuelle et copier essentiellement des données à l’aide d’Azure Site Recovery, vous devez avoir les autorisations suivantes :

    1. Des autorisations pour créer une machine virtuelle dans les ressources Azure. Le rôle prédéfini « Contributeur de machines virtuelles » a ces autorisations, incluant :
        - L’autorisation de créer une machine virtuelle dans le groupe de ressources sélectionné
        - L’autorisation de créer une machine virtuelle dans le réseau virtuel sélectionné
        - L’autorisation d’écrire dans le compte de stockage sélectionné

    2. Vous devez également avoir l’autorisation de gérer les opérations Azure Site Recovery. Le rôle « Collaborateur Site Recovery » a toutes les autorisations nécessaires pour gérer les opérations Site Recovery dans un coffre Recovery Services.

## <a name="prepare-the-source-vms"></a>Préparer les machines virtuelles sources

1. Assurez-vous que tous les certificats racines les plus récents sont présents sur les machines virtuelles Azure à déplacer. À défaut, la copie de données vers la région cible ne peut pas être activée en raison des contraintes de sécurité.

    - Pour les machines virtuelles Windows, installez-y toutes les mises à jour de Windows les plus récentes afin que tous les certificats racines approuvés s’y trouvent. Dans un environnement déconnecté, suivez les processus Windows Update et de mise à jour de certificat standard en vigueur pour votre organisation.
    - Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle.
2. N’utilisez pas de proxy d’authentification dans le but de contrôler la connectivité réseau pour les machines virtuelles que vous voulez déplacer.
3. Si la machine virtuelle que vous voulez déplacer n’a pas d’accès à Internet et utilise un proxy de pare-feu pour contrôler l’accès sortant, vérifiez la configuration requise détaillée [ici](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Identifiez la topologie du réseau source et toutes les ressources que vous utilisez actuellement, entre autres les équilibreurs de charge, les groupes de sécurité réseau, l’IP publique, etc.

## <a name="prepare-the-target-region"></a>Préparer la région cible

1. Vérifiez que votre abonnement Azure vous permet de créer des machines virtuelles dans la région cible utilisée pour la récupération d’urgence. Contactez le support pour activer le quota requis.

2. Assurez-vous que votre abonnement dispose de suffisamment de ressources pour prendre en charge des machines virtuelles de tailles correspondant à vos machines virtuelles source. Si vous l’utilisez pour copier des données vers la cible, Site Recovery choisit une machine virtuelle cible de la même taille ou de la taille la plus proche possible.

3. Veillez à créer une ressource cible pour chaque composant identifié dans la topologie du réseau source. Cette étape est importante pour garantir que vos machines virtuelles, après leur déplacement final vers la région cible, offriront toutes les fonctionnalités et capacités qui étaient disponibles dans la région source.

    > [!NOTE]
    > Azure Site Recovery détecte et crée automatiquement un réseau virtuel quand vous activez la réplication de la machine virtuelle source. Vous pouvez aussi précréer un réseau et l’affecter à la machine virtuelle dans le flux utilisateur pour activer la réplication. En revanche, pour toutes les autres ressources, comme mentionné ci-dessous, vous devez les créer manuellement dans la région cible.

     Consultez la documentation suivante pour créer les ressources réseau courantes dont vous avez besoin, en fonction de la configuration de la machine virtuelle source.

    - [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Équilibreurs de charge](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Adresse IP publique](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Pour tous les autres composants réseau, reportez-vous à cette [documentation](https://docs.microsoft.com/azure/#pivot=products&panel=network) sur la mise en réseau. 

4. Vous devez [créer un réseau hors production](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) manuellement dans la région cible si vous souhaitez tester la configuration avant d’effectuer le déplacement final vers la région cible. Cette pratique est recommandée dans la mesure où elle limite les interférences avec l’environnement de production.
    
## <a name="copy-data-to-the-target-region"></a>Copier des données vers la région cible
Suivez les étapes ci-dessous pour copier les données vers la région cible à l’aide d’Azure Site Recovery.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Créez le coffre dans n’importe quelle région, à l’exception de la région source.

1. Connectez-vous au [portail Azure](https://portal.azure.com) > **Recovery Services**.
2. Cliquez sur **Créer une ressource** > **Outils de gestion** > **Backup and Site Recovery**.
3. Dans **Nom**, indiquez le nom convivial **ContosoVMVault**. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié.
4. Créez un groupe de ressources **ContosoRG**.
5. Spécifiez une région Azure. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Tarification de Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Dans Coffres Recovery Services, cliquez sur **Vue d’ensemble** > **ConsotoVMVault** > **+ Répliquer**.
7. Dans **Source**, sélectionnez **Azure**.
8. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
9. Sélectionnez le modèle de déploiement Resource Manager. Sélectionnez ensuite **Abonnement source** et **Groupe de ressources source**.
10. Cliquez sur **OK** pour enregistrer les paramètres.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Activez la réplication des machines virtuelles Azure et démarrez la copie des données.

Site Recovery récupère une liste des machines virtuelles associées à l’abonnement et au groupe de ressources.

1. À l’étape suivante, Sélectionnez la machine virtuelle que vous souhaitez déplacer. Cliquez ensuite sur **OK**.
3. Dans **Paramètres**, cliquez sur **Récupération d’urgence**.
4. Dans **Configurer la récupération d’urgence** > **Région cible**, sélectionnez la région cible vers laquelle vous allez effectuer la réplication.
5. Pour ce didacticiel, acceptez les autres paramètres par défaut.
6. Cliquez sur **Activer la réplication**. Cette opération démarre un travail consistant à activer la réplication pour la machine virtuelle.

    ![activer la réplication](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>Tester la configuration


1. Accédez au coffre puis, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle que vous souhaitez déplacer vers la région cible et cliquez sur l’icône **+ Test de basculement**.
2. Dans **Test de basculement**, sélectionnez un point de récupération à utiliser pour le basculement :

   - **Dernier point traité** : bascule la machine virtuelle vers le dernier point de récupération qui a été traité par le service Site Recovery. L’horodatage est affiché. Cette option, qui n’implique aucun traitement de données, offre un objectif de délai de récupération faible.
   - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché.
   - **Personnalisé** : sélectionnez n’importe quel point de récupération.

3. Sélectionnez le réseau virtuel Azure cible vers lequel vous voulez déplacer les machines virtuelles Azure afin de tester la configuration. 

> [!IMPORTANT]
> Nous vous recommandons d’utiliser un réseau machines virtuelles Azure distinct pour faire le test de basculement, et pas le réseau de production vers lequel vous comptez déplacer vos machines virtuelles au final. Ce réseau a été configuré quand vous avez activé la réplication.

4. Pour démarrer le test du déplacement, cliquez sur **OK**. Pour suivre la progression, cliquez sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez également cliquer sur le travail **Test de basculement** dans le nom du coffre > **Paramètres** > **Travaux** > **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure de réplication apparaît dans le portail Azure > **Machines virtuelles**. Vérifiez que la machine virtuelle est en cours d’exécution, qu’elle est correctement dimensionnée et qu’elle est connectée au réseau approprié.
6. Si vous souhaitez supprimer la machine virtuelle qui a été créée pour les besoins du test du déplacement, cliquez sur **Nettoyer le test de basculement** sur l’élément répliqué. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Effectuez et validez le déplacement vers la région cible.

1.  Accédez au coffre puis, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle et ensuite sur **Basculement**.
2. Dans **Basculement**, sélectionnez **Dernier**. 
3. Sélectionnez **Arrêtez la machine avant de commencer le basculement**. Site Recovery tente d’arrêter la machine virtuelle source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**. 
4. Une fois que la tâche est terminée, vérifiez que la machine virtuelle apparaît bien dans la région Azure cible choisie.
5. Dans **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Valider**. Cette étape termine le processus de déplacement vers la région cible. Attendez la fin de la tâche de validation.

## <a name="discard-the-resource-in-the-source-region"></a>Supprimer les ressources dans la région source 

1. Accédez à la machine virtuelle.  Cliquez sur **Désactiver la réplication**.  Cela met fin au processus de copie des données pour la machine virtuelle.  

> [!IMPORTANT]
> Il est important d’effectuer cette étape pour arrêter la facturation liée à la réplication Azure Site Recovery.

Si vous n’envisagez pas de réutiliser les ressources sources, effectuez les étapes suivantes.

1. Supprimez toutes les ressources réseau appropriées dans la région source (celles que vous avez listées à l’étape 4 dans [Préparer les machines virtuelles sources](#prepare-the-source-vms)). 
2. Supprimez le compte de stockage associé dans la région source.



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez déplacé une machine virtuelle Azure vers une autre région. Vous pouvez maintenant configurer la reprise d’activité après sinistre pour la machine virtuelle que vous avez déplacée.

> [!div class="nextstepaction"]
> [Configurer la récupération d’urgence après la migration](azure-to-azure-quickstart.md)

