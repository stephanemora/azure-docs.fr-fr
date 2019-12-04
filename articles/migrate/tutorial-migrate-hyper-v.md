---
title: Migrer des machines virtuelles Hyper-V vers Azure avec la migration de serveurs Azure Migrate | Microsoft Docs
description: Découvrir comment migrer des machines virtuelles Hyper-V locales vers Azure avec la migration de serveurs Azure Migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: be5d519269739f09b4a4264292f578b1d7051d26
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196310"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migrer des machines virtuelles Hyper-V vers Azure 

Cet article explique comment effectuer une migration sans agent de machines virtuelles Hyper-V locales vers Azure avec l’outil Azure Migrate Server Migration.

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure de vos applications et charges de travail locales, ainsi que de machines virtuelles cloud privées/publiques. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration, ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.

Ce tutoriel est le troisième d’une série qui montre comment évaluer et migrer des machines virtuelles Hyper-V vers Azure à l’aide d’Azure Migrate Server Assessment et Server Migration. Ce tutoriel vous montre comment effectuer les opérations suivantes :


> [!div class="checklist"]
> * Préparer Azure et votre environnement Hyper-V local
> * Configurer l’environnement source et déployer une appliance de réplication.
> * Configurez l’environnement cible.
> * Activez la réplication.
> * Exécuter une migration de test pour vérifier que tout fonctionne comme prévu.
> * Exécuter une migration complète vers Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

1. [Examiner](hyper-v-migration-architecture.md) l’architecture de la migration Hyper-V.
2. [Suivre le premier tutoriel](tutorial-prepare-hyper-v.md) de cette série pour configurer Azure et Hyper-V pour la migration. Dans le premier tutoriel, vous avez effectué les opérations suivantes :
    - [Préparer Azure](tutorial-prepare-hyper-v.md#prepare-azure) pour la migration.
    - [Préparer l’environnement local](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) pour la migration.
3. Nous vous recommandons d’essayer d’évaluer des machines virtuelles Hyper-V à l’aide d’Azure Migrate Server Assessment avant de les migrer vers Azure. Pour ce faire, [suivez le deuxième tutoriel](tutorial-assess-hyper-v.md) de cette série. Bien qu’il soit recommandé de tester l’exécution d’une évaluation, vous n’êtes pas obligé d’en exécuter une avant de migrer des machines virtuelles.
4. Vérifier que le rôle Contributeur de machines virtuelles est attribué à votre compte Azure pour que vous soyez autorisé à effectuer les opérations suivantes :

    - Créer une machine virtuelle dans le groupe de ressources sélectionné
    - Créer une machine virtuelle dans le réseau virtuel sélectionné
    - Écrire sur un disque managé Azure.   
5. [Configurez un réseau Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Quand vous effectuez une migration vers Azure, les machines virtuelles Azure créées sont jointes à un réseau Azure que vous spécifiez lors de la configuration de la migration.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Ajouter l’outil Azure Migrate Server Migration

Si vous n’avez pas suivi le deuxième tutoriel pour évaluer les machines virtuelles Hyper-V, vous devez [suivre ces instructions](how-to-add-tool-first-time.md) pour configurer un projet Azure Migrate et ajouter l’outil Azure Migrate Server Migration au projet.

Si vous avez suivi le deuxième tutoriel et que vous avez déjà configuré un projet Azure Migrate, ajoutez l’outil Azure Migrate Server Migration comme suit :

1. Dans le projet Azure Migrate, cliquez sur **Vue d’ensemble**. 
2. Dans **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.
3. Dans **Outils de migration**, sélectionnez **Cliquez ici pour ajouter un outil de migration lorsque vous êtes prêt à effectuer la migration**.

    ![Sélectionner un outil](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Dans la liste des outils, sélectionnez **Azure Migrate : Server Migration** > **Ajouter un outil**

    ![Outil Server Migration](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate

Azure Migrate Server Migration exécute une appliance de machines virtuelles Hyper-V légère.

- L’appliance effectue la découverte des machines virtuelles et envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate Server Migration.
- La même appliance est également utilisée par l’outil Azure Migrate Server Assessment.

Pour configurer l’appliance :
- Si vous avez suivi le deuxième tutoriel pour évaluer des machines virtuelles Hyper-V, vous avez déjà configuré l’appliance au cours de ce tutoriel.
- Si vous n’avez pas suivi ce tutoriel, vous devez configurer l’appliance maintenant. Pour ce faire, procédez comme suit : 

    - Téléchargez un disque dur virtuel Hyper-V compressé à partir du portail Azure.
    - Créez l’appliance et vérifiez qu’elle peut se connecter à Azure Migrate Server Assessment. 
    - Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.

    Suivez les instructions indiquées dans [cet article](how-to-set-up-appliance-hyper-v.md) pour configurer l’appliance.

## <a name="prepare-hyper-v-hosts"></a>Préparer les hôtes Hyper-V

1. Dans le projet Azure Migrate > **Serveurs**, dans **Azure Migrate : Server Migration**, cliquez sur **Découvrir**.
2. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec Hyper-V**.
3. Dans **Région cible**, sélectionnez la région Azure vers laquelle vous souhaitez migrer les machines.
6. Sélectionnez **Confirmer que la région cible pour la migration est « nom_région »** .
7. Cliquez sur **Créer des ressources**. Un coffre Azure Site Recovery est créé en arrière-plan.
    - Si vous avez déjà configuré la migration avec Azure Migrate Server Migration, cette option ne s’affiche pas car les ressources ont été configurées précédemment.
    - Après avoir cliqué sur ce bouton, vous ne pouvez plus changer la région cible de ce projet.
    - Toutes les migrations suivantes sont effectuées dans cette région.
    
8. Dans **Préparer les serveurs hôtes Hyper-V**, téléchargez le fournisseur de réplication Hyper-V et le fichier de clé d’inscription.
    - La clé d’inscription est nécessaire pour inscrire l’hôte Hyper-V auprès d’Azure Migrate Server Migration.
    - Une fois générée, la clé reste valide pendant 5 jours.

    ![Télécharger le fournisseur et la clé](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copiez le fichier de configuration du fournisseur et le fichier de clé d’inscription sur chaque hôte Hyper-V (ou nœud de cluster) exécutant des machines virtuelles que vous voulez répliquer.
5. Exécutez le fichier de configuration du fournisseur sur chaque hôte, comme décrit dans la procédure suivante.
6. Après avoir installé le fournisseur sur les hôtes, dans **Découvrir des machines**, cliquez sur **Finaliser l’inscription**.

    ![Finaliser l’inscription](./media/tutorial-migrate-hyper-v/finalize-registration.png)

La finalisation de l’inscription peut prendre jusqu’à 15 minutes, jusqu’à ce que les machines virtuelles découvertes apparaissent dans Azure Migrate Server Migration. À mesure que des machines virtuelles sont découvertes, le nombre de **Serveurs découverts** augmente.

![Serveurs découverts](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Inscrire des hôtes Hyper-V

Installez le fichier de configuration téléchargé (AzureSiteRecoveryProvider.exe) sur chaque hôte Hyper-V pertinent.

1. Exécutez le fichier de configuration du fournisseur sur chaque l’hôte ou nœud de cluster.
2. Dans l’Assistant Configuration du fournisseur > **Microsoft Update**, choisissez d’utiliser Microsoft Update pour rechercher les mises à jour du fournisseur.
3. Dans **Installation**, acceptez l’emplacement d’installation par défaut pour le fournisseur et l’agent, et sélectionnez **Installer**.
4. Après l’installation, dans l’Assistant Inscription > **Paramètres du coffre**, sélectionnez **Parcourir** et, dans **Fichier de clé**, sélectionnez le fichier de clé de coffre que vous avez téléchargé.
5. Dans **Paramètres du proxy**, indiquez la façon dont le fournisseur exécuté sur l’hôte se connecte à Internet.
    - Si l’appliance se trouve derrière un serveur proxy, vous devez spécifier les paramètres du proxy.
    - Spécifiez le nom du proxy sous la forme **http://ip-address** ou **http://FQDN** . Les serveurs proxy HTTPS ne sont pas pris en charge.
   

6. Vérifiez que le fournisseur peut atteindre les [URL nécessaires](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. Dans **Inscription**, une fois que l’hôte est inscrit, cliquez sur **Terminer**.

## <a name="replicate-hyper-v-vms"></a>Répliquer des machines virtuelles Hyper-V

Une fois la découverte terminée, vous pouvez commencer la réplication des machines virtuelles Hyper-V vers Azure.

> [!NOTE]
> Vous pouvez répliquer ensemble jusqu’à 10 machines. Si vous avez besoin d’en répliquer davantage, répliquez-les simultanément par lots de 10.

1. Dans le projet Azure Migrate > **Serveurs**, **Azure Migrate : Server Migration**, cliquez sur **Répliquer**.
2. Dans **Répliquer** > **Paramètres de la source** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec Hyper-V**. Cliquez ensuite sur **Suivant : Machines virtuelles**.
3. Dans **Machines virtuelles**, sélectionnez les machines à répliquer.
    - Si vous avez exécuté une évaluation des machines virtuelles, vous pouvez appliquer les recommandations relatives au dimensionnement et au type de disque (Premium/Standard) des machines virtuelles qui apparaissent dans les résultats de l’évaluation. Pour ce faire, dans **Importer les paramètres de migration à partir d’une évaluation Azure Migrate ?** , sélectionnez l’option **Oui**.
    - Si vous n’avez pas exécuté d’évaluation ou que vous ne souhaitez pas utiliser les paramètres de l’évaluation, sélectionnez l’option **Non**.
    - Si vous avez choisi d’utiliser l’évaluation, sélectionnez le groupe de machines virtuelles et le nom de l’évaluation.

        ![Sélectionner l’évaluation](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Dans **Machines virtuelles**, recherchez les machines virtuelles en fonction des besoins et cochez chaque machine virtuelle que vous souhaitez migrer. Cliquez ensuite sur **Suivant : Paramètres de la cible**.

    ![Sélectionner les machines virtuelles](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Dans **Paramètres de la cible**, sélectionnez la région cible vers laquelle vous allez effectuer la migration, l’abonnement, puis le groupe de ressources dans lequel les machines virtuelles Azure résideront après la migration.
7. Dans **Compte de stockage de réplication**, sélectionnez le compte de stockage Azure dans lequel les données répliquées seront stockées dans Azure.
8. Dans **Réseau virtuel**, sélectionnez le réseau virtuel/sous-réseau Azure auquel les machines virtuelles Azure seront jointes après la migration.
9. Dans **Azure Hybrid Benefit** :

    - Sélectionnez **Non** si vous ne souhaitez pas appliquer Azure Hybrid Benefit. Cliquez ensuite sur **Suivant**.
    - Sélectionnez **Oui** si vous avez des machines Windows Server couvertes par des abonnements Software Assurance ou Windows Server actifs et que vous souhaitez appliquer l’avantage aux machines que vous migrez. Cliquez ensuite sur **Suivant**.

    ![Paramètres de la cible](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Dans **Capacité de calcul**, vérifiez le nom, la taille, le type de disque du système d’exploitation et le groupe à haute disponibilité de la machine virtuelle. Les machines virtuelles doivent satisfaire aux [exigences d’Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Taille de la machine virtuelle** : si vous utilisez les recommandations de l’évaluation, la liste déroulante Taille de la machine virtuelle contient la taille recommandée. Sinon, Azure Migrate choisit une taille qui correspond à la taille la plus proche dans l’abonnement Azure. Vous pouvez également choisir une taille manuelle dans **Taille de la machine virtuelle Azure**. 
    - **Disque de système d’exploitation** : spécifiez le disque du système d’exploitation (démarrage) pour la machine virtuelle. Le disque du système d’exploitation est le disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation. 
    - **Groupe à haute disponibilité** : si la machine virtuelle doit se trouver dans un groupe à haute disponibilité Azure après la migration, spécifiez-le ici. Ce groupe doit figurer dans le groupe de ressources cible que vous spécifiez pour la migration.

    ![Paramètres de capacité de calcul de la machine virtuelle](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Dans **Disques**, indiquez si les disques de machine virtuelle doivent être répliqués sur Azure, puis sélectionnez le type de disque (SSD/HDD standard ou disques managés Premium) dans Azure. Cliquez ensuite sur **Suivant**.
    - Vous pouvez exclure des disques de la réplication.
    - Si vous excluez des disques, ils ne seront pas présents sur la machine virtuelle Azure après la migration. 

    ![Disques](./media/tutorial-migrate-hyper-v/disks.png)

10. Dans **Passer en revue et démarrer la réplication**, passez en revue les paramètres, puis cliquez sur **Répliquer** pour démarrer la réplication initiale pour les serveurs.

> [!NOTE]
> Vous pouvez mettre à jour les paramètres de réplication à tout moment avant le démarrage de la réplication, dans **Gérer** > **Réplication des machines**. Vous ne pouvez pas changer les paramètres après le démarrage de la réplication.

### <a name="provisioning-for-the-first-time"></a>Provisionnement initial

S’il s’agit de la première machine virtuelle que vous répliquez dans le projet Azure Migrate, Azure Migrate Server Migration provisionne automatiquement ces ressources dans le même groupe de ressources que le projet.

- **Service Bus** : Azure Migrate Server Migration utilise Service Bus pour envoyer des messages d’orchestration de réplication à l’appliance.
- **Compte de stockage de la passerelle** : Server Migration utilise le compte de stockage de la passerelle pour stocker des informations d’état relatives aux machines virtuelles en cours de réplication.
- **Compte de stockage de journal** : l’appliance Azure Migrate charge les journaux de réplication pour les machines virtuelles vers un compte de stockage de journal. Azure Migrate applique les informations de réplication aux disques managés de réplica.
- **Coffre de clés** : l’appliance Azure Migrate utilise le coffre de clés afin de gérer les chaînes de connexion pour Service Bus, et des clés d’accès pour les comptes de stockage utilisés dans la réplication. Vous devez avoir configuré les autorisations dont le coffre de clés a besoin pour accéder au compte de stockage lors de la préparation. [Passez en revue ces autorisations](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Suivre et superviser


- Quand vous cliquez sur **Répliquer**, un travail Démarrer la réplication commence. 
- Une fois le travail Démarrer la réplication terminé, les machines commencent leur réplication initiale vers Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les changements incrémentiels apportés aux disques locaux sont répliqués régulièrement vers Azure.

Vous pouvez suivre l’état du travail dans les notifications du portail.

Vous pouvez superviser l’état de la réplication en cliquant sur **Réplication de serveurs** dans **Azure Migrate : Server Migration**.
![Superviser la réplication](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Exécuter un test de migration


Quand la réplication différentielle commence, vous pouvez exécuter une migration de test avec les machines virtuelles avant d’exécuter une migration complète vers Azure. Avant la migration, nous vous recommandons vivement d’effectuer cette opération au moins une fois pour chaque machine.

- L’exécution d’une migration de test permet de vérifier que la migration fonctionne comme prévu, sans impact sur les machines locales qui restent opérationnelles et poursuivent la réplication. 
- Une migration de test simule la migration en créant une machine virtuelle Azure à l’aide de données répliquées (il s’agit en général d’une migration vers un réseau virtuel hors production dans votre abonnement Azure).
- Vous pouvez utiliser la machine virtuelle Azure de test répliquée pour valider la migration, effectuer des tests d’applications et résoudre les éventuels problèmes avant la migration complète.

Effectuez une migration de test de la façon suivante :


1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Tester les serveurs migrés**.

     ![Tester les serveurs migrés](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Cliquez avec le bouton droit sur la machine virtuelle à tester, puis cliquez sur **Migration de test**.

    ![Migration de test](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Dans **Migration de test**, sélectionnez le réseau virtuel Azure dans lequel la machine virtuelle Azure se trouvera après la migration. Nous vous recommandons d’utiliser un réseau virtuel hors production.
4. Le travail **Migration de test** démarre. Supervisez le travail dans les notifications du portail.
5. Une fois la migration terminée, affichez la machine virtuelle Azure migrée dans **Machines virtuelles** dans le portail Azure. Le nom de la machine a le suffixe **-Test**.
6. Une fois le test terminé, cliquez avec le bouton droit sur la machine virtuelle Azure dans **Réplication des machines**, puis cliquez sur **Nettoyer la migration de test**.

    ![Nettoyer la migration](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migrer des machines virtuelles

Après avoir vérifié que la migration de test fonctionne comme prévu, vous pouvez migrer des machines locales.

1. Dans le projet Azure Migrate > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Réplication de serveurs**.

    ![Réplication de serveurs](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. Dans **Réplication des machines**, cliquez avec le bouton droit sur la machine virtuelle > **Migrer**.
3. Dans **Migrer** > **Arrêter les machines virtuelles et effectuer une migration planifiée sans perte de données**, sélectionnez **Oui** > **OK**.
    - Par défaut, Azure Migrate arrête la machine virtuelle locale et exécute une réplication à la demande pour synchroniser tout changement apporté à la machine virtuelle depuis la dernière réplication. Cela permet d’éviter toute perte de données.
    - Si vous ne souhaitez pas arrêter la machine virtuelle, sélectionnez **Non**.
4. Un travail de migration démarre pour la machine virtuelle. Suivez le travail dans les notifications Azure.
5. Une fois le travail terminé, vous pouvez afficher et gérer la machine virtuelle à partir de la page **Machines virtuelles**.

## <a name="complete-the-migration"></a>Effectuer la migration

1. Une fois la migration terminée, cliquez avec le bouton droit sur la machine virtuelle > **Arrêter la migration**. Cette action arrête la réplication pour la machine locale et nettoie les informations d’état de la réplication de la machine virtuelle.
2. Installez l’agent [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) ou [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) de machine virtuelle Azure sur les machines migrées.
3. Effectuez les éventuels ajustements post-migration de l’application, comme la mise à jour des chaînes de connexion de base de données et les configurations du serveur web.
4. Effectuez les tests finaux de réception de l’application et de la migration sur l’application migrée qui s’exécute maintenant dans Azure.
5. Réduisez le trafic vers l’instance de machine virtuelle Azure migrée.
6. Supprimez les machines virtuelles locales de votre inventaire des machines virtuelles locales.
7. Supprimez les machines virtuelles locales des sauvegardes locales.
8. Mettez à jour la documentation interne en y mentionnant le nouvel emplacement et la nouvelle adresse IP des machines virtuelles Azure. 

## <a name="post-migration-best-practices"></a>Bonnes pratiques après la migration

- Pour une meilleure résilience :
    - Sécurisez les données en sauvegardant les machines virtuelles Azure avec le service Sauvegarde Azure. [Plus d’informations](../backup/quick-backup-vm-portal.md)
    - Conservez les charges de travail en cours d’exécution et disponibles en continu en répliquant des machines virtuelles Azure vers une région secondaire avec Site Recovery. [Plus d’informations](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Pour renforcer la sécurité :
    - Verrouillez et limitez l’accès du trafic entrant avec l’[administration juste-à-temps d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Limitez le trafic réseau vers les points de terminaison de gestion avec des [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Déployez [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) pour sécuriser les disques, et protégez les données contre le vol et les accès non autorisés.
    - Découvrez plus d’informations sur la [sécurisation des ressources IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) et visitez [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Pour la surveillance et la gestion :
-  Envisagez de déployer [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) pour surveiller l’utilisation et les coûts des ressources.


## <a name="next-steps"></a>Étapes suivantes

Examinez le [parcours de migration cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) dans le framework d’adoption du cloud Azure.
