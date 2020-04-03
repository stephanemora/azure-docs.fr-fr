---
title: Effectuer une migration des machines virtuelles AWS vers Azure avec le service Azure Site Recovery | Microsoft Docs
description: Cet article décrit comment migrer des machines virtuelles Windows s’exécutant dans Amazon Web Services (AWS) vers Azure en utilisant Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 929bc0695bda2e64f77f7e9286e06cee787822ba
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388965"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrer des machines virtuelles Amazon Web Services (AWS) vers Azure

Ce tutoriel vous montre comment migrer des machines virtuelles Amazon Web Services (AWS) vers des machines virtuelles Azure à l’aide d’Azure Site Recovery. Durant la migration d’instances AWS EC2 vers Azure, les machines virtuelles sont traitées comme des ordinateurs locaux physiques. Dans ce tutoriel, vous allez apprendre à :


> [!TIP]
> Vous devez maintenant utiliser le service Azure Migrate, et non le service Azure Site Recovery, pour migrer des machines virtuelles AWS vers Azure. [Plus d’informations](../migrate/tutorial-migrate-physical-virtual-machines.md)


> [!div class="checklist"]
> * Vérifier la configuration requise
> * Préparer les ressources Azure
> * Préparer les instances AWS EC2 pour la migration
> * Déployer un serveur de configuration
> * Activer la réplication pour les machines virtuelles
> * Tester le basculement pour vérifier que tout fonctionne bien
> * Exécuter un basculement unique vers Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Prérequis
- Assurez-vous que les machines virtuelles que vous voulez migrer exécutent une version du système d’exploitation prise en charge. Les versions prises en charge sont les suivantes : 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - Version 64 bits de Windows Server 2008 R2 SP1 ou version ultérieure
  - Red Hat Enterprise Linux 6.4 à 6.10, 7.1 à 7.6 (instances virtualisées HVM uniquement) *(Les instances exécutant des pilotes RedHat PV ne sont pas prises en charge.)*
  - CentOS 6.4 à 6.10, 7.1 à 7.6 (instances virtualisées HVM uniquement)
 
- Le service Mobilité doit être installé sur chaque machine virtuelle que vous souhaitez répliquer. 

    > [!IMPORTANT]
    > Site Recovery installe ce service automatiquement quand vous activez la réplication pour la machine virtuelle. Pour une installation automatique, vous devez préparer un compte sur les instances EC2 qui sera utilisé par Site Recovery pour accéder à la machine virtuelle. Vous pouvez utiliser un compte local ou de domaine. 
    > - Pour les machines virtuelles Linux, le compte doit être root sur le serveur Linux source. 
    > - Pour les machines virtuelles Windows, si vous n’utilisez pas un compte de domaine, désactivez le contrôle d’accès des utilisateurs distants sur la machine locale :
    >
    >      Dans le Registre, sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** et définissez sa valeur sur **1**.

- Une instance EC2 distincte que vous pouvez utiliser comme serveur de configuration Site Recovery. Cette instance doit exécuter Windows Server 2012 R2.

## <a name="prepare-azure-resources"></a>Préparer les ressources Azure

Vous devez disposer de quelques ressources prêtes dans Azure, que les instances EC2 migrées vont utiliser. Il s’agit d’un compte de stockage, d’un coffre et d’un réseau virtuel.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

Les images des machines répliquées sont conservées dans le stockage Azure. Les machines virtuelles Azure sont créées à partir du stockage quand vous basculez du site local vers Azure.

1. Dans le menu de gauche du [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage**.
2. Entrez un nom pour votre compte de stockage. Dans ces tutoriels, nous utilisons le nom **awsmigrated2017**. Le nom doit :
    - Être unique dans Azure
    - Contenir entre 3 et 24 caractères
    - Ne contenir que des chiffres et des lettres minuscules
3. Conservez les valeurs par défaut pour **Modèle de déploiement**, **Type de compte**, **Performances** et **Transfert sécurisé requis**.
5. Pour **Réplication**, sélectionnez la valeur par défaut **RA-GRS**.
6. Sélectionnez l’abonnement que vous voulez utiliser pour ce tutoriel.
7. Pour **Groupe de ressources**, sélectionnez **Créer**. Dans cet exemple, nous utilisons **migrationRG** pour le nom du groupe de ressources.
8. Pour **Emplacement**, sélectionnez **Europe Ouest**.
9. Sélectionnez **Créer** pour créer le compte de stockage.

### <a name="create-a-vault"></a>Création d'un coffre

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Recherchez puis sélectionnez **Coffres Recovery Services**.
2. Dans la page Coffres Recovery Services, sélectionnez **Ajouter**.
3. Pour le **Nom**, entrez **myVault**.
4. Dans **Abonnement**, sélectionnez l’abonnement que vous voulez utiliser.
4. Pour le **Groupe de ressources**, sélectionnez **Existant** puis **migrationRG**.
5. Pour **Emplacement**, sélectionnez **Europe Ouest**.
5. Sélectionnez **Épingler au tableau de bord** pour pouvoir accéder rapidement au nouveau coffre à partir du tableau de bord.
7. Sélectionnez **Créer** lorsque vous avez terminé.

Pour afficher le nouveau coffre, accédez à **Tableau de bord** > **Toutes les ressources**. Le nouveau coffre apparaît également dans la page principale **Coffres Recovery Services**.

### <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

Quand les machines virtuelles Azure sont créées après la migration (basculement), elles sont jointes à ce réseau Azure.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Mise en réseau** >
   **Réseau virtuel**.
3. Dans **Nom**, entrez **myMigrationNetwork**.
4. Conservez la valeur par défaut pour **Espace d’adressage** (vous devez entrer une valeur).
5. Dans **Abonnement**, sélectionnez l’abonnement que vous voulez utiliser.
6. Pour le **Groupe de ressources**, sélectionnez **Existant** puis **migrationRG**.
7. Pour **Emplacement**, sélectionnez **Europe Ouest**.
8. Sous **Sous-réseau**, conservez les valeurs par défaut pour le **Nom** et la **Plage d’adresses IP** (vous devez entrer une valeur).
9. Ajoutez des instructions pour les paramètres de protection DDoS.
10. Laissez l’option **Points de terminaison de Service** désactivée.
11. Ajoutez des instructions pour les paramètres du pare-feu.
12. Sélectionnez **Créer** lorsque vous avez terminé.

## <a name="prepare-the-infrastructure"></a>Préparer l’infrastructure

Dans la page de votre coffre dans le portail Azure, dans la section **Bien démarrer**, sélectionnez **Site Recovery** puis sélectionnez sur **Préparer l’infrastructure**. Effectuez ensuite les tâches suivantes.

### <a name="1-protection-goal"></a>1 : Objectif de protection

Dans la page **Objectif de protection**, sélectionnez les valeurs suivantes :

|    |  |
|---------|-----------|
| Où se trouvent vos machines ? |Sélectionnez **Locale**.|
| Sur quelle cible souhaitez-vous répliquer vos machines ? |Sélectionnez **Vers Azure**.|
| Effectuez-vous une migration ? | Sélectionnez **Oui**, puis cochez la case en regard de **Je comprends, mais je souhaite continuer avec Azure Site Recovery.**
| Vos machines sont-elles virtualisées ? |Sélectionnez **Non virtualisée / Autre**.|

Quand vous avez terminé, sélectionnez **OK** pour passer à la section suivante.

### <a name="2-select-deployment-planning"></a>2 : Sélectionner la planification d’un déploiement

Dans **Avez-vous effectué la planification du déploiement ?** , sélectionnez **Je le ferai plus tard**, puis sélectionnez **OK**.

### <a name="3-prepare-source"></a>3 : Préparer la source

Dans la page **Préparer la source**, sélectionnez **+ Serveur de configuration**.

1. Utilisez une instance EC2 exécutant Windows Server 2012 R2 pour créer un serveur de configuration et l’inscrire auprès de votre coffre de récupération.
2. Configurez le proxy sur la machine virtuelle de l’instance EC2 que vous utilisez comme serveur de configuration, afin qu’il puisse accéder aux [URL de service](site-recovery-support-matrix-to-azure.md).
3. Téléchargez [Installation unifiée de Microsoft Azure Site Recovery](https://aka.ms/unifiedinstaller_wus). Vous pouvez le télécharger sur votre machine locale, puis le copier sur la machine virtuelle que vous utilisez comme serveur de configuration.
4. Sélectionnez le bouton **Télécharger** pour télécharger la clé d’inscription du coffre. Copiez le fichier téléchargé sur la machine virtuelle que vous utilisez comme serveur de configuration.
5. Sur la machine virtuelle, cliquez avec le bouton droit sur le programme d’installation que vous avez téléchargé pour l’installation unifiée de Microsoft Azure Site Recovery, et sélectionnez **Exécuter en tant qu’administrateur**.

    1. Dans **Avant de commencer**, sélectionnez **Installer le serveur de configuration et le serveur de processus**, puis sélectionnez **Suivant**.
    2. Dans **Licence de logiciel tiers**, sélectionnez **J’accepte le contrat de licence tiers**, puis **Suivant**.
    3. Dans **Inscription**, sélectionnez **Parcourir** et accédez à l’endroit où vous avez placé le fichier de clé d’inscription du coffre. Sélectionnez **Suivant**.
    4. Dans **Paramètres Internet**, sélectionnez **Se connecter directement à Azure Site Recovery sans serveur proxy**, puis cliquez sur **Suivant**.
    5. La page **Vérification de la configuration requise** effectue des vérifications pour plusieurs éléments. Quand cela est terminé, cliquez sur **Suivant**.
    6. Dans **Configuration MySQL**, spécifiez les mots de passe nécessaires, puis cliquez sur **Suivant**.
    7. Dans la page **Détails de l’environnement**, sélectionnez **Non**. Vous n’avez pas besoin de protéger les machines VMware. Ensuite, sélectionnez **Suivant**.
    8. Dans **Emplacement d’installation**, cliquez sur **Suivant** pour accepter la valeur par défaut.
    9. Dans **Sélection du réseau**, cliquez sur **Suivant** pour accepter la valeur par défaut.
    10. Dans **Résumé**, sélectionnez **Installer**.
    11. **Progression de l’installation** vous montre des informations sur le processus d’installation. Quand cela est terminé, cliquez sur **Terminer**. Une fenêtre affiche un message concernant un redémarrage. Sélectionnez **OK**. Ensuite, une fenêtre affiche un message concernant la phrase secrète de la connexion du serveur de configuration. Copiez la phrase secrète dans votre Presse-papiers et enregistrez-la dans un endroit sûr.
6. Sur la machine virtuelle, exécutez cspsconfigtool.exe pour créer un ou plusieurs comptes de gestion sur le serveur de configuration. Vérifiez que les comptes de gestion disposent des autorisations d’administrateur sur les instances EC2 que vous voulez migrer.

Quand vous avez terminé la configuration du serveur de configuration, revenez au portail et sélectionnez le serveur que vous avez créer pour le **serveur de configuration**. Sélectionnez **OK** pour passer à l’étape 3 : Préparer la cible.

### <a name="4-prepare-target"></a>4 : Préparer la cible

Dans cette section, vous entrez des informations sur les ressources que vous avez créées dans [Préparer les ressources Azure](#prepare-azure-resources), plus tôt dans ce tutoriel.

1. Dans **Abonnement**, sélectionnez l’abonnement Azure que vous avez utilisé pour le didacticiel [Préparer Azure](tutorial-prepare-azure.md).
2. Sélectionnez **Resource Manager** comme modèle de déploiement.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes de stockage Azure compatibles. Il doit s’agir des ressources que vous avez créées dans [Préparer les ressources Azure](#prepare-azure-resources), plus tôt dans ce tutoriel.
4. Quand vous avez terminé, sélectionnez **OK**.

### <a name="5-prepare-replication-settings"></a>5 : Préparer les paramètres de réplication

Vous devez créer une stratégie de réplication avant de pouvoir activer la réplication.

1. Sélectionnez **Créer et associer**.
2. Dans **Nom**, entrez **myReplicationPolicy**.
3. Conservez les autres paramètres par défaut puis sélectionnez **OK** pour créer la stratégie. La nouvelle stratégie est automatiquement associée au serveur de configuration.

Lorsque vous en avez terminé avec les cinq sections sous **Préparer l’infrastructure**, sélectionnez **OK**.

## <a name="enable-replication"></a>Activer la réplication

Activez la réplication pour chaque machine virtuelle que vous voulez migrer. Quand la réplication est activée, Site Recovery installe automatiquement le service Mobilité.

1. Accédez au [portail Azure](https://portal.azure.com).
1. Dans la page correspondant à votre coffre, sous **Bien démarrer**, sélectionnez **Site Recovery**.
2. Sous **Pour les ordinateurs locaux et les machines virtuelles Azure**, sélectionnez **Étape 1 : Répliquer l’application**. Remplissez les pages de l’Assistant avec les informations suivantes. Sélectionnez **OK** sur chaque page lorsque vous avez terminé :
   - 1 : Configurer la source

     |  |  |
     |-----|-----|
     | Source : | Sélectionnez **Locale**.|
     | Emplacement source :| Entrez le nom de votre instance EC2 du serveur de configuration.|
     |Type de machine : | Sélectionner **Machines physiques**.|
     | Serveur de processus : | Sélectionnez le serveur de configuration dans la liste déroulante.|

   - 2 : Configurer la cible

     |  |  |
     |-----|-----|
     | Cible : | Conservez la valeur par défaut.|
     | Abonnement : | Sélectionnez l’abonnement que vous avez utilisé.|
     | Groupe de ressources de post-basculement :| Utilisez le groupe de ressources que vous avez créé dans [Préparer les ressources Azure](#prepare-azure-resources).|
     | Modèle de déploiement post-basculement : | Sélectionnez **Gestionnaire des ressources**.|
     | Compte de stockage : | Sélectionnez le compte de stockage que vous avez créé dans [Préparer les ressources Azure](#prepare-azure-resources).|
     | Réseau Azure : | Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées**.|
     | Réseau Azure post-basculement : | Choisissez le réseau que vous avez créé dans [Préparer les ressources Azure](#prepare-azure-resources).|
     | Sous-réseau : | Sélectionnez la **valeur par défaut** dans la liste déroulante.|

   - 3 : Sélectionner les machines physiques

     Sélectionnez **Machine physique**, puis entrez les valeurs pour le **Nom**, l’**Adresse IP** et le **Type de système d’exploitation** de l’instance EC2 que vous voulez migrer. Sélectionnez **OK**.

   - 4 : Configurer les propriétés

     Sélectionnez le compte que vous avez créé sur le serveur de configuration puis sélectionnez **OK**.

   - 5 : Configurer les paramètres de réplication

     Vérifiez que la stratégie de réplication sélectionnée dans la liste déroulante est **myReplicationPolicy**, puis cliquez sur **OK**.

3. Quand l’Assistant est terminé, cliquez sur **Activer la réplication**.

Pour suivre la progression du travail **Activer la protection**, accédez à **Surveillance et rapports** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.        

Quand vous activez la réplication pour une machine virtuelle, vous devrez peut-être attendre 15 minutes ou plus avant que les modifications ne prennent effet et qu’elles apparaissent dans le portail.

## <a name="run-a-test-failover"></a>Exécuter un test de basculement

Quand vous effectuez un test de basculement, les événements suivants se produisent :

- Une vérification des prérequis est effectuée pour garantir que toutes les conditions nécessaires pour le basculement sont en place.
- Le basculement traite les données pour permettre la création d’une machine virtuelle Azure. Si vous avez sélectionné le dernier point de récupération, un point de récupération est créé à partir des données.
- Une machine virtuelle Azure est créée en utilisant les données traitées à l’étape précédente.

Dans le portail, effectuez le test de basculement :

1. Dans la page correspondant à votre coffre, accédez à **Éléments protégés** > **Éléments répliqués**. Sélectionnez la machine virtuelle, puis **Test de basculement**.
2. Sélectionnez un point de récupération à utiliser pour le basculement :
    - **Dernier point traité** : bascule la machine virtuelle vers le dernier point de récupération qui a été traité par Site Recovery. L’horodatage est affiché. Cette option, avec laquelle aucun temps n’est passé à traiter les données, offre un objectif de délai de récupération faible (RTO).
    - **Dernier point de cohérence des applications** : cette option bascule toutes les machines virtuelles vers le dernier point de récupération de cohérence des applications. L’horodatage est affiché.
    - **Personnalisé** : sélectionnez n’importe quel point de récupération.

3. Dans **Tester le basculement**, sélectionnez le réseau Azure cible auquel les machines virtuelles Azure seront connectées après le basculement. Il doit s’agir du réseau que vous avez créé dans [Préparer les ressources Azure](#prepare-azure-resources).
4. Sélectionnez **OK** pour commencer le basculement. Pour suivre la progression, sélectionnez la machine virtuelle pour afficher ses propriétés. Ou vous pouvez sélectionner le travail **Test de basculement** sur la page de votre coffre. Pour ce faire, sélectionnez **Analyse et rapports** > **Travaux** >  **Travaux Site Recovery**.
5. Une fois le basculement terminé, la machine virtuelle Azure répliquée apparaît dans le portail Azure. Pour afficher la machine virtuelle, sélectionnez **Machines virtuelles**. Assurez-vous que la machine virtuelle est de la taille appropriée, qu’elle est connectée au réseau approprié et qu’elle est en cours d’exécution.
6. Vous devriez à présent pouvoir vous connecter à la machine virtuelle répliquée dans Azure.
7. Pour supprimer les machines virtuelles créées lors du test de basculement, sélectionnez **Nettoyer le test de basculement** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire. Le traitement prend 8 à 10 minutes pour s’exécuter.

## <a name="migrate-to-azure"></a>Migrer vers Azure

Effectuez un basculement réel pour les instances EC2 pour les migrer vers des machines virtuelles Azure :

1. Dans **Éléments protégés** > **Éléments répliqués**, sélectionnez les instances AWS, puis **Basculement**.
2. Dans **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Sélectionnez le dernier point de récupération, puis lancez le basculement. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
1. Assurez-vous que la machine virtuelle apparaît dans **Éléments répliqués**.
2. Cliquez avec le bouton droit sur chaque machine virtuelle, puis sélectionnez **Effectuer la migration**. Cette opération effectue les actions suivantes :

   - Ceci termine le processus de migration, arrête la réplication pour la machine virtuelle AWS et arrête la facturation Site Recovery pour la machine virtuelle.
   - Cette étape nettoie les données de réplication. Elle ne supprime pas les machines virtuelles migrées. 

     ![Terminer la migration](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *N’annulez pas un basculement en cours*. Avant le démarrage du basculement, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.  


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert comment migrer des instances AWS EC2 vers des machines virtuelles Azure. Pour plus d’informations sur les machines virtuelles Azure, consultez les didacticiels sur les machines virtuelles Windows.

> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Windows](../virtual-machines/windows/tutorial-manage-vm.md)
