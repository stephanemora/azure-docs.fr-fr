---
title: Migrer des machines virtuelles VMware à l’aide de la migration de serveurs Azure Migrate sans agent
description: Découvrez comment exécuter la migration sans agent des machines virtuelles VMware avec Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 825d6ff16a1f51fa476541ee10fea5f8a1c2972e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304206"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>Migrer des machines virtuelles VMware vers Azure (sans agent)

Cet article explique comment effectuer une migration sans agent de machines virtuelles VMware locales vers Azure avec l’outil Azure Migrate Server Migration.

[Azure Migrate](migrate-services-overview.md) offre un hub central pour suivre la découverte, l’évaluation et la migration vers Azure d’applications et de charges de travail locales, ainsi que d’instances de machines virtuelles AWS/GCP. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.

Ce tutoriel est le troisième d’une série qui montre comment évaluer et migrer des machines virtuelles VMware vers Azure à l’aide d’Azure Migrate Server Assessment et Server Migration. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Préparer les machines virtuelles pour la migration
> * Ajouter l’outil Azure Migrate Server Migration
> * Découvrir les machines virtuelles que vous souhaitez migrer
> * Démarrer la réplication des machines virtuelles
> * Exécuter une migration de test pour vérifier que tout fonctionne comme prévu.
> * Exécuter une migration de machine virtuelle complète

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.

## <a name="migration-methods"></a>Méthodes de migration

Vous pouvez migrer des machines virtuelles VMware vers Azure à l’aide de l’outil de migration de serveur Azure Migrate. Cet outil offre deux options pour la migration de machines virtuelles VMware :

- Migration à l’aide de la réplication sans agent. Migrez des machines virtuelles sans avoir à installer quoi que ce soit.
- Migration avec un agent pour la réplication. Installez un agent sur la machine virtuelle pour la réplication.

Pour savoir quelle migration effectuer, sans agent ou basée sur un agent, passez en revue les articles suivants :

- [Découvrez](server-migrate-overview.md) le fonctionnement de la migration sans agent et [comparez les méthodes de migration](server-migrate-overview.md#compare-migration-methods).
- [Lisez cet article](tutorial-migrate-vmware-agent.md) si vous souhaitez appliquer la méthode basée sur un agent.

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez :

1. [Suivre le premier tutoriel](tutorial-prepare-vmware.md) de cette série pour configurer Azure et VMware pour la migration. Plus précisément, dans ce tutoriel, vous devez :
    - [Préparer Azure](tutorial-prepare-vmware.md#prepare-azure) pour la migration.
    - [Préparer l’environnement local](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) pour la migration.
    
2. Nous vous recommandons de tester l’évaluation de machines virtuelles VMware avec Azure Migrate Server Assessment avant de les migrer vers Azure. Pour configurer l’évaluation, [suivez le deuxième tutoriel](tutorial-assess-vmware.md) de cette série. Si vous ne souhaitez pas évaluer les machines virtuelles, vous pouvez ignorer ce tutoriel. Même s’il est recommandé d’exécuter une évaluation, vous n’êtes pas obligé de le faire avant d’essayer d’effectuer une migration.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Ajouter l’outil Azure Migrate Server Migration

Si vous n’avez pas suivi le deuxième tutoriel pour évaluer les machines virtuelles VMware, vous devez [suivre ces instructions](how-to-add-tool-first-time.md) pour configurer un projet Azure Migrate et sélectionner l’outil Azure Migrate Server Migration. 

Si vous avez suivi le deuxième tutoriel et que vous avez déjà configuré un projet Azure Migrate, ajoutez l’outil Azure Migrate Server Migration comme suit :

1. Dans le projet Azure Migrate, cliquez sur **Vue d’ensemble**. 
2. Dans **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

     ![Évaluer et migrer des serveurs](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Dans **Outils de migration**, sélectionnez **Cliquez ici pour ajouter un outil de migration lorsque vous êtes prêt à effectuer la migration**.

    ![Sélectionner un outil](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Dans la liste des outils, sélectionnez **Azure Migrate : Server Migration** > **Ajouter un outil**

    ![Outil Server Migration](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Configurer l’appliance Azure Migrate

Azure Migrate Server Migration exécute une appliance de machines virtuelles VMware légère. L’appliance effectue la découverte des machines virtuelles et envoie les métadonnées et les données de performances des machines virtuelles à Azure Migrate Server Migration. La même appliance est également utilisée par l’outil Azure Migrate Server Assessment.

Si vous avez suivi le deuxième tutoriel pour évaluer des machines virtuelles VMware, vous avez déjà configuré l’appliance au cours de ce tutoriel. Si vous n’avez pas suivi ce tutoriel, vous devez configurer l’appliance maintenant. Pour ce faire, procédez comme suit : 

- Téléchargez un fichier de modèle OVA, puis importez-le dans vCenter Server.
- Créez l’appliance et vérifiez qu’elle peut se connecter à Azure Migrate Server Assessment. 
- Configurez l’appliance pour la première fois, puis inscrivez-la auprès du projet Azure Migrate.

Suivez les instructions indiquées dans [cet article](how-to-set-up-appliance-vmware.md) pour configurer l’appliance.


## <a name="prepare-vms-for-migration"></a>Préparer les machines virtuelles pour la migration

Azure Migrate nécessite des modifications des machines virtuelles afin d’être sûr de pouvoir les migrer vers Azure.

- Pour certains systèmes d’exploitation, Azure Migrate effectue ces modifications automatiquement. [En savoir plus](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Si vous migrez une machine virtuelle qui n’a pas l’un de ces systèmes d’exploitation, suivez les instructions pour préparer la machine virtuelle.
- Il est important d’effectuer ces modifications avant de commencer la migration. Si vous migrez la machine virtuelle avant d’effectuer la modification, elle risque de ne pas démarrer dans Azure.
- Les modifications de configuration que vous apportez aux machines virtuelles locales sont répliquées dans Azure après l’activation de la réplication pour la machine virtuelle. Pour garantir la réplication des modifications, vérifiez que le point de récupération vers lequel vous effectuez la migration est postérieur au moment où les modifications de configuration ont été apportées localement.


### <a name="prepare-windows-server-vms"></a>Préparer les machines virtuelles Windows Server

**Action** | **Détails** | **Instructions**
--- | --- | ---
Vérifier que les volumes Windows des machines virtuelles Azure utilisent les mêmes affectations de lettre de lecteur que la machine virtuelle locale | Configurez la stratégie SAN sur « Mettre en ligne tous les disques ». | 1. Connectez-vous à la machine virtuelle avec un compte d’administrateur, puis ouvrez une fenêtre de commande.<br/> 2. Tapez **diskpart** pour exécuter l’utilitaire Diskpart.<br/> 3. Tapez **SAN POLICY=OnlineAll**<br/> 4. Tapez Exit pour quitter DiskPart, puis fermez l’invite de commandes.
Activer la console d’accès série Azure pour la machine virtuelle Azure | Cela facilite la résolution des problèmes. Vous n’avez pas besoin de redémarrer la machine virtuelle. La machine virtuelle Azure démarrera à l’aide de l’image de disque, ce qui équivaut à un redémarrage de la nouvelle machine virtuelle. | Suivez [ces instructions](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) pour l’activer.
Installer l’intégration de l’invité Hyper-V | Si vous migrez des ordinateurs Windows Server 2003, installez les services d’intégration d’invité Hyper-V sur le système d’exploitation de la machine virtuelle. | [Plus d’informations](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)
Bureau à distance | Activez Bureau à distance sur la machine virtuelle et vérifiez que le Pare-feu Windows ne bloque pas l’accès Bureau à distance sur les profils réseau. | [Plus d’informations](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access)

### <a name="prepare-linux-vms"></a>Préparer les machines virtuelles Linux

**Action** | **Détails** 
--- | --- | ---
Installer les services d’intégration Linux pour Hyper-V | Ils sont inclus par défaut dans la plupart des nouvelles versions des distributions Linux.
Regénérer l’image init Linux pour qu’elle contienne les pilotes Hyper-V nécessaires | Cela permet de s’assurer que la machine virtuelle démarrera dans Azure, et n’est nécessaire que sur certaines distributions.
Activer la journalisation de la console série Azure | Cela facilite la résolution des problèmes. Vous n’avez pas besoin de redémarrer la machine virtuelle. La machine virtuelle Azure démarrera à l’aide de l’image de disque, ce qui équivaut à un redémarrage de la nouvelle machine virtuelle.<br/> Suivez [ces instructions](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) pour l’activer.
Mettre à jour le fichier de mappage d’appareils | Mettez à jour le fichier de mappage d’appareils contenant les associations entre les volumes et les noms d’appareils, afin d’utiliser des identificateurs d’appareils persistants.
Mettre à jour les entrées fstab | Mettez à jour les entrées fstab pour utiliser des identificateurs de volumes persistants.
Supprimer la règle udev | Supprimez toutes les règles udev qui réservent des noms d’interface basés sur l’adresse mac, etc.
Mettre à jour les interfaces réseau | Mettez à jour les interfaces réseau pour recevoir l’adresse IP basée sur DHCP.
Activer ssh | Vérifiez que ssh est activé et que le service sshd est configuré pour démarrer automatiquement au redémarrage.<br/> Vérifiez que les requêtes de connexion ssh entrantes ne sont pas bloquées par le pare-feu du système d’exploitation ou par des règles de script.

[Consultez cet article](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) pour découvrir les étapes d’exécution d’une machine virtuelle Linux sur Azure, ainsi que des instructions pour certaines des distributions Linux populaires.  


## <a name="replicate-vms"></a>Répliquer des machines virtuelles

Une fois la découverte terminée, vous pouvez commencer la réplication des machines virtuelles VMware sur Azure. 

> [!NOTE]
> Vous pouvez répliquer ensemble jusqu’à 10 machines. Si vous avez besoin d’en répliquer davantage, répliquez-les simultanément par lots de 10. Pour la migration sans agent, vous pouvez exécuter jusqu’à 100 réplications simultanées.

1. Dans le projet Azure Migrate > **Serveurs**, **Azure Migrate : Server Migration**, cliquez sur **Répliquer**.

    ![Répliquer des machines virtuelles](./media/tutorial-migrate-vmware/select-replicate.png)

2. Dans **Répliquer** > **Paramètres de la source** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Oui, avec VMware vSphere**.
3. Dans **Appliance locale**, sélectionnez le nom de l’appliance Azure Migrate que vous avez configurée > **OK**. 

    ![Paramètres de la source](./media/tutorial-migrate-vmware/source-settings.png)

    - Cette étape part du principe que vous avez déjà configuré une appliance quand vous avez terminé le tutoriel.
    - Si vous n’avez pas configuré d’appliance, suivez les instructions de [cet article](how-to-set-up-appliance-vmware.md).

4. Dans **Machines virtuelles**, sélectionnez les machines à répliquer.
    - Si vous avez exécuté une évaluation des machines virtuelles, vous pouvez appliquer les recommandations relatives au dimensionnement et au type de disque (Premium/Standard) des machines virtuelles qui apparaissent dans les résultats de l’évaluation. Pour ce faire, dans **Importer les paramètres de migration à partir d’une évaluation Azure Migrate ?** , sélectionnez l’option **Oui**.
    - Si vous n’avez pas exécuté d’évaluation ou que vous ne souhaitez pas utiliser les paramètres de l’évaluation, sélectionnez l’option **Non**.
    - Si vous avez choisi d’utiliser l’évaluation, sélectionnez le groupe de machines virtuelles et le nom de l’évaluation.

    ![Sélectionner l’évaluation](./media/tutorial-migrate-vmware/select-assessment.png)

5. Dans **Machines virtuelles**, recherchez les machines virtuelles en fonction des besoins et cochez chaque machine virtuelle que vous souhaitez migrer. Cliquez ensuite sur **Suivant : Paramètres de la cible**.

    ![Sélectionner les machines virtuelles](./media/tutorial-migrate-vmware/select-vms.png)

6. Dans **Paramètres de la cible**, sélectionnez l’abonnement et la région cible vers laquelle vous allez migrer, puis spécifiez le groupe de ressources dans lequel les machines virtuelles Azure résideront après la migration. Dans **Réseau virtuel**, sélectionnez le réseau virtuel/sous-réseau Azure auquel les machines virtuelles Azure seront jointes après la migration.
7. Dans **Azure Hybrid Benefit** :

    - Sélectionnez **Non** si vous ne souhaitez pas appliquer Azure Hybrid Benefit. Cliquez ensuite sur **Suivant**.
    - Sélectionnez **Oui** si vous avez des machines Windows Server couvertes par des abonnements Software Assurance ou Windows Server actifs et que vous souhaitez appliquer l’avantage aux machines que vous migrez. Cliquez ensuite sur **Suivant**.

    ![Paramètres de la cible](./media/tutorial-migrate-vmware/target-settings.png)

8. Dans **Capacité de calcul**, vérifiez le nom, la taille, le type de disque du système d’exploitation et le groupe à haute disponibilité de la machine virtuelle. Les machines virtuelles doivent satisfaire aux [exigences d’Azure](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **Taille de la machine virtuelle** : si vous utilisez les recommandations de l’évaluation, la liste déroulante Taille de la machine virtuelle contient la taille recommandée. Sinon, Azure Migrate choisit une taille qui correspond à la taille la plus proche dans l’abonnement Azure. Vous pouvez également choisir une taille manuelle dans **Taille de la machine virtuelle Azure**. 
    - **Disque de système d’exploitation** : spécifiez le disque du système d’exploitation (démarrage) pour la machine virtuelle. Le disque du système d’exploitation est le disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation. 
    - **Groupe à haute disponibilité** : si la machine virtuelle doit se trouver dans un groupe à haute disponibilité Azure après la migration, spécifiez-le ici. Ce groupe doit figurer dans le groupe de ressources cible que vous spécifiez pour la migration.

    ![Paramètres de capacité de calcul de la machine virtuelle](./media/tutorial-migrate-vmware/compute-settings.png)

9. Dans **Disques**, indiquez si les disques de machine virtuelle doivent être répliqués sur Azure, puis sélectionnez le type de disque (SSD/HDD standard ou disques managés Premium) dans Azure. Cliquez ensuite sur **Suivant**.
    - Vous pouvez exclure des disques de la réplication.
    - Si vous excluez des disques, ils ne seront pas présents sur la machine virtuelle Azure après la migration. 

    ![Disques](./media/tutorial-migrate-vmware/disks.png)

10. Dans **Passer en revue et démarrer la réplication**, passez en revue les paramètres, puis cliquez sur **Répliquer** pour démarrer la réplication initiale pour les serveurs.

> [!NOTE]
> Vous pouvez mettre à jour les paramètres de réplication à tout moment avant le démarrage de la réplication, dans **Gérer** > **Réplication des machines**. Vous ne pouvez pas changer les paramètres après le démarrage de la réplication.

### <a name="provisioning-for-the-first-time"></a>Provisionnement initial

S’il s’agit de la première machine virtuelle que vous répliquez dans le projet Azure Migrate, Azure Migrate Server Migration provisionne automatiquement ces ressources dans le même groupe de ressources que le projet.

- **Service Bus** : Azure Migrate Server Migration utilise Service Bus pour envoyer des messages d’orchestration de réplication à l’appliance.
- **Compte de stockage de la passerelle** : Server Migration utilise le compte de stockage de la passerelle pour stocker des informations d’état relatives aux machines virtuelles en cours de réplication.
- **Compte de stockage de journal** : l’appliance Azure Migrate charge les journaux de réplication pour les machines virtuelles vers un compte de stockage de journal. Azure Migrate applique les informations de réplication aux disques managés de réplica.
- **Coffre de clés** : l’appliance Azure Migrate utilise le coffre de clés afin de gérer les chaînes de connexion pour Service Bus, et des clés d’accès pour les comptes de stockage utilisés dans la réplication. Vous devez avoir configuré les autorisations dont le coffre de clés a besoin pour accéder au compte de stockage lors de la préparation. [Passez en revue ces autorisations](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault).   


## <a name="track-and-monitor"></a>Suivre et superviser


- Quand vous cliquez sur **Répliquer**, un travail Démarrer la réplication commence. 
- Une fois le travail Démarrer la réplication terminé, les machines commencent leur réplication initiale vers Azure.
- Lors de la réplication initiale, une capture instantanée de machine virtuelle est créée. Les données de disque de la capture instantanée sont répliquées sur les disques managés de réplica dans Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les modifications incrémentielles apportées aux disques locaux sont répliquées régulièrement sur les disques de réplica dans Azure.

Vous pouvez suivre l’état du travail dans les notifications du portail.

Vous pouvez superviser l’état de la réplication en cliquant sur **Réplication de serveurs** dans **Azure Migrate : Server Migration**.
![Superviser la réplication](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Exécuter un test de migration


Quand la réplication différentielle commence, vous pouvez exécuter une migration de test avec les machines virtuelles avant d’exécuter une migration complète vers Azure. Avant la migration, nous vous recommandons vivement d’effectuer cette opération au moins une fois pour chaque machine.

- L’exécution d’une migration de test permet de vérifier que la migration fonctionne comme prévu, sans impact sur les machines locales qui restent opérationnelles et poursuivent la réplication. 
- Une migration de test simule la migration en créant une machine virtuelle Azure à l’aide de données répliquées (il s’agit en général d’une migration vers un réseau virtuel hors production dans votre abonnement Azure).
- Vous pouvez utiliser la machine virtuelle Azure de test répliquée pour valider la migration, effectuer des tests d’applications et résoudre les éventuels problèmes avant la migration complète.

Effectuez une migration de test de la façon suivante :


1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Tester les serveurs migrés**.

     ![Tester les serveurs migrés](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Cliquez avec le bouton droit sur la machine virtuelle à tester, puis cliquez sur **Migration de test**.

    ![Migration de test](./media/tutorial-migrate-vmware/test-migrate.png)

3. Dans **Migration de test**, sélectionnez le réseau virtuel Azure dans lequel la machine virtuelle Azure se trouvera après la migration. Nous vous recommandons d’utiliser un réseau virtuel hors production.
4. Le travail **Migration de test** démarre. Supervisez le travail dans les notifications du portail.
5. Une fois la migration terminée, affichez la machine virtuelle Azure migrée dans **Machines virtuelles** dans le portail Azure. Le nom de la machine a le suffixe **-Test**.
6. Une fois le test terminé, cliquez avec le bouton droit sur la machine virtuelle Azure dans **Réplication des machines**, puis cliquez sur **Nettoyer la migration de test**.

    ![Nettoyer la migration](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Migrer des machines virtuelles

Après avoir vérifié que la migration de test fonctionne comme prévu, vous pouvez migrer des machines locales.

1. Dans le projet Azure Migrate > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Réplication de serveurs**.

    ![Réplication de serveurs](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Dans **Réplication des machines**, cliquez avec le bouton droit sur la machine virtuelle > **Migrer**.
3. Dans **Migrer** > **Arrêter les machines virtuelles et effectuer une migration planifiée sans perte de données**, sélectionnez **Oui** > **OK**.
    - Par défaut, Azure Migrate arrête la machine virtuelle locale et exécute une réplication à la demande pour synchroniser tout changement apporté à la machine virtuelle depuis la dernière réplication. Cela permet d’éviter toute perte de données.
    - Si vous ne souhaitez pas arrêter la machine virtuelle, sélectionnez **Non**.
4. Un travail de migration démarre pour la machine virtuelle. Suivez le travail dans les notifications Azure.
5. Une fois le travail terminé, vous pouvez afficher et gérer la machine virtuelle à partir de la page **Machines virtuelles**.

## <a name="complete-the-migration"></a>Effectuer la migration

1. Une fois la migration terminée, cliquez avec le bouton droit sur la machine virtuelle > **Arrêter la réplication**. Cette action arrête la réplication pour la machine locale et nettoie les informations d’état de la réplication de la machine virtuelle.
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
