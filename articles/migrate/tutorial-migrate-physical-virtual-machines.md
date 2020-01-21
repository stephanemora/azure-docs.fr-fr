---
title: Migrer des machines physiques locales ou des machines virtualisées vers Azure avec Azure Migrate Server Migration | Microsoft Docs
description: Cet article explique comment effectuer la migration de machines physiques locales ou de machines virtualisées vers Azure avec Azure Migrate Server Migration.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a6e33770f93c365d5ccd034803c7c7f247d528a
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028803"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Migrer des serveurs physiques ou virtualisés vers Azure 

Cet article explique comment migrer des serveurs physiques ou virtualisés vers Azure. L’outil Azure Migrate Server Migration permet d’effectuer la migration de serveurs physiques ou virtualisés, à l’aide de la réplication basée sur l’agent. Avec cet outil, vous pouvez effectuer la migration d’une large gamme de machines vers Azure :

- Serveurs physiques locaux
- Machines virtuelles virtualisées par des plateformes telles que Xen ou KVM
- Machines virtuelles Hyper-V ou VMware. Une telle migration s’avère utile si, pour une raison ou une autre, vous ne parvenez pas à utiliser le flux de migration standard proposé par Azure Migrate Server Migration pour les migrations [Hyper-V](tutorial-migrate-hyper-v.md), les migrations [sans agent VMware](tutorial-migrate-vmware.md) ou les migrations [VMware basées sur l’agent](tutorial-migrate-vmware-agent.md).
- Machines virtuelles exécutées dans des clouds privés
- Machines virtuelles exécutées dans des clouds publics tels qu’Amazon Web Services (AWS) ou Google Cloud Platform (GCP).


[Azure Migrate](migrate-services-overview.md) offre un hub central permettant de suivre la découverte, l’évaluation et la migration vers Azure de vos applications et charges de travail locales, ainsi que de vos instances de machines virtuelles cloud. Le hub fournit des outils Azure Migrate pour l’évaluation et la migration ainsi que des offres de fournisseurs de logiciels indépendants (ISV) tiers.


Dans ce tutoriel, vous allez apprendre à :
> [!div class="checklist"]
> * Préparer Azure pour la migration à l’aide de l’outil Azure Migrate Server Migration
> * Vérifier les conditions requises pour les machines qui doivent faire l’objet de la migration, et préparer une machine pour l’appliance de réplication Azure Migrate utilisée pour découvrir et effectuer la migration des machines vers Azure
> * Ajouter l’outil Azure Migrate Server Migration dans le hub Azure Migrate
> * Configurer l’appliance de réplication
> * Installer Mobility Service sur chaque machine qui doit faire l’objet d’une migration
> * Activez la réplication.
> * Exécuter une migration de test pour vérifier que tout fonctionne comme prévu.
> * Exécuter une migration complète vers Azure.

> [!NOTE]
> Les tutoriels vous montrent le chemin de déploiement le plus simple pour un scénario donné afin que vous puissiez configurer rapidement une preuve de concept. Ils utilisent des options par défaut, le cas échéant, et ne montrent pas tous les paramètres et chemins possibles. Pour obtenir des instructions détaillées, consultez les articles de guide pratique pour Azure Migrate.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) avant de commencer.


## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce didacticiel, vous devez :

1. [Passer en revue](migrate-architecture.md) l’architecture de migration.
2. Vérifier que le rôle Contributeur de machines virtuelles est attribué à votre compte Azure pour que vous soyez autorisé à effectuer les opérations suivantes :

    - Créer une machine virtuelle dans le groupe de ressources sélectionné
    - Créer une machine virtuelle dans le réseau virtuel sélectionné
    - Écrire sur un disque managé Azure. 

3. [Configurez un réseau Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Lorsque vous effectuez une réplication sur Azure, des machines virtuelles Azure sont créées et jointes au réseau Azure que vous avez spécifié lors de la configuration de la migration.


## <a name="prepare-azure"></a>Préparer Azure

Configurez les autorisations Azure avant de pouvoir migrer avec Azure Migrate Server Migration.

- **Créez un projet** : votre compte Azure doit être autorisé à créer un projet Azure Migrate. 
- **Inscrivez l’appliance de réplication Azure Migrate** : l’appliance de réplication crée et inscrit une application Azure Active Directory dans votre compte Azure. Vous devez déléguer les autorisations.
- **Créez un coffre de clés** : pour migrer des machines, Azure Migrate crée un coffre de clés dans le groupe de ressources afin de gérer les clés d’accès du compte de stockage de réplication de votre abonnement. Pour créer le coffre, vous devez disposer d’autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate. 


### <a name="assign-permissions-to-create-project"></a>Attribuer des autorisations pour créer un projet

1. Dans le portail Azure, ouvrez l’abonnement, puis sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes.
3. Vous devez disposer des autorisations de **Contributeur** ou de **Propriétaire**.
    - Si vous venez de créer un compte Azure gratuit, vous êtes le propriétaire de votre abonnement.
    - Si vous n’êtes pas le propriétaire de l’abonnement, demandez au propriétaire de vous attribuer le rôle.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Affecter des autorisations pour inscrire l’appliance de réplication

Pour la migration basée sur un agent, déléguez des autorisations pour Azure Migrate Server Migration afin de créer et d’inscrire une application Azure AD dans votre compte. Pour affecter des autorisations, utilisez l’une des méthodes suivantes :

- L’administrateur général ou le locataire peuvent accorder des autorisations aux utilisateurs du locataire pour créer et inscrire des applications Azure AD.
- L’administrateur général ou le locataire peuvent attribuer au compte le rôle Développeur d’applications (qui dispose des autorisations appropriées).

Il est intéressant de noter que :

- Les applications n’ont aucune autre autorisation d’accès sur l’abonnement que celles décrites ci-dessus.
- Vous avez uniquement besoin de ces autorisations pour inscrire une nouvelle appliance de réplication. Vous pouvez supprimer les autorisations une fois l’appliance de réplication configurée. 


#### <a name="grant-account-permissions"></a>Accorder des autorisations au compte

L’administrateur général ou le locataire peuvent octroyer des autorisations de la façon suivante :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

    ![Autorisations Azure AD](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

#### <a name="assign-application-developer-role"></a>Attribuer le rôle Développeur d’applications 

L’administrateur général ou le locataire peuvent attribuer à un compte le rôle Développeur d’applications. [Plus d’informations](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="assign-permissions-to-create-key-vault"></a>Affecter des autorisations pour créer un coffre de clés

Affectez des autorisations d’attribution de rôle sur le groupe de ressources dans lequel réside le projet Azure Migrate, comme ceci :

1. Dans le groupe de ressources du portail Azure, sélectionnez **Contrôle d’accès (IAM)** .
2. Dans **Vérifier l’accès**, recherchez le compte approprié, puis cliquez dessus pour voir les autorisations correspondantes. Vous avez besoin d’autorisations **Propriétaire** (ou **Contributeur** et **Administrateur de l’accès utilisateur**).
3. Si vous ne disposez pas des autorisations nécessaires, demandez-les au propriétaire du groupe de ressources. 

## <a name="prepare-for-migration"></a>Préparation de la migration

### <a name="check-machine-requirements-for-migration"></a>Vérifier les conditions requises pour la migration des machines

Vérifiez que les machines sont conformes aux exigences de migration vers Azure. 

> [!NOTE]
> La migration basée sur un agent avec Azure Migrate Server Migration est basée sur les fonctionnalités du service Azure Site Recovery. Certaines exigences peuvent pointer vers la documentation de Site Recovery.

1. [Vérifiez](migrate-support-matrix-physical-migration.md#physical-server-requirements) les conditions requises des serveurs physiques.
2. Vérifiez les paramètres des machines virtuelles. Les machines virtuelles locales que vous répliquez vers Azure doivent respecter les [conditions requises des machines virtuelles Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Préparer une machine pour l’appliance de réplication

Azure Migrate Server Migration utilise une appliance de réplication pour répliquer les machines dans Azure. L’appliance de réplication exécute les composants suivants :

- **Serveur de configuration** : Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.
- **Serveur de traitement**: Le serveur de processus fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à une mise en cache, une compression et un chiffrement, puis les envoie à un compte de stockage de cache dans Azure. 

Avant de commencer, vous devez préparer une machine Windows Server 2016 pour qu’elle héberge l’appliance de réplication. La machine doit être conforme à [ces exigences](migrate-replication-appliance.md). L’appliance ne doit pas être installée sur une machine source que vous souhaitez protéger.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Ajouter l’outil Azure Migrate Server Migration

Configurez un projet Azure Migrate, puis ajoutez-y l’outil Azure Migrate Server Migration.

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Azure Migrate**.
2. Sous **Services**, sélectionnez **Azure Migrate**.
3. Dans **Vue d’ensemble**, cliquez sur **Évaluer et migrer des serveurs**.
4. Sous **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Évaluer et migrer des serveurs**.

    ![Découvrir et évaluer des serveurs](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Dans **Découvrir, évaluer et migrer des serveurs**, cliquez sur **Ajouter des outils**.
6. Dans **Projet de migration**, sélectionnez votre abonnement Azure, puis créez un groupe de ressources si vous n’en avez pas.
7. Dans **Détails du projet**, spécifiez le nom du projet et la géographie dans laquelle vous souhaitez créer le projet, puis cliquez sur **Suivant**.

    ![Créer un projet Azure Migrate](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Vous pouvez créer un projet Azure Migrate dans les zones géographiques suivantes.

    **Zone géographique** | **Région**
    --- | ---
    Asia | Asie Sud-Est
    Europe | Europe Nord ou Europe Ouest
    États-Unis | USA Est ou Centre-USA Ouest

    La zone géographique spécifiée pour le projet est utilisée uniquement pour stocker les métadonnées collectées à partir des machines virtuelles locales. Vous pouvez sélectionner n’importe quelle région cible pour la migration réelle.
8. Dans **Sélectionner un outil d’évaluation**, sélectionnez **Ignorer l’ajout d’un outil d’évaluation pour l’instant** > **Suivant**.
9. Dans **Sélectionner un outil de migration**, sélectionnez **Azure Migrate : Server Migration** > **Suivant**.
10. Dans **Vérifier + ajouter des outils**, passez en revue les paramètres, puis cliquez sur **Ajouter des outils**.
11. L’outil ajouté apparaît alors dans le projet Azure Migrate > **Serveurs** > **Outils de migration**.

## <a name="set-up-the-replication-appliance"></a>Configurer l’appliance de réplication

La première étape de la migration consiste à configurer l’appliance de réplication. Vous téléchargez le fichier du programme d’installation de l’appliance, puis vous l’exécutez sur la [machine que vous avez préparée](#prepare-a-machine-for-the-replication-appliance). Une fois l’appliance installée, vous devez l’inscrire auprès d’Azure Migrate Server Migration.


### <a name="download-the-replication-appliance-installer"></a>Télécharger le programme d’installation de l’appliance de réplication

1. Dans le projet Azure Migrate > **Serveurs**, dans ***Azure Migrate : Server Migration**, cliquez sur **Découvrir**.

    ![Détection des machines virtuelles](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Dans **Découvrir des machines** > **Vos machines sont-elles virtualisées ?** , cliquez sur **Non virtualisé/autre**.
4. Dans **Région cible**, sélectionnez la région Azure vers laquelle vous souhaitez migrer les machines.
5. Sélectionnez **Confirmer que la région cible pour la migration est « nom_région »** .
6. Cliquez sur **Créer des ressources**. Un coffre Azure Site Recovery est créé en arrière-plan.
    - Si vous avez déjà configuré la migration avec Azure Migrate Server Migration, l’option Cible ne peut pas être configurée, car les ressources ont déjà été configurées.
    - Après avoir cliqué sur ce bouton, vous ne pouvez plus changer la région cible de ce projet.
    - Toutes les migrations suivantes sont effectuées dans cette région.

7. Dans **Voulez-vous installer une nouvelle appliance de réplication ?** , sélectionnez **Installer une appliance de réplication**.
9. Dans **Télécharger et installer le logiciel de l’appliance de réplication**, téléchargez le programme d’installation de l’appliance et la clé d’inscription. Vous avez besoin de la clé pour inscrire l’appliance. Une fois téléchargée, la clé reste valide pendant 5 jours.

    ![Télécharger le fournisseur](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Copiez le fichier de configuration de l’appliance et le fichier de clé sur la machine Windows Server 2016 que vous avez créée pour l’appliance.
11. Exécutez le fichier de configuration de l’appliance de réplication en suivant la procédure ci-dessous.
12. Une fois l’appliance installée et redémarrée, dans **Découvrir des machines**, sélectionnez la nouvelle appliance dans **Sélectionner un serveur de configuration**, puis cliquez sur **Finaliser l’inscription**. L’option Finaliser l’inscription exécute quelques tâches finales pour préparer l’appliance de réplication.

    ![Finaliser l’inscription](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Après la finalisation de l’inscription, 15 minutes peuvent être nécessaires pour que les machines découvertes apparaissent dans Azure Migrate Server Migration. À mesure que des machines virtuelles sont découvertes, le nombre de **Serveurs découverts** augmente.

![Serveurs découverts](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>Installer le service Mobilité

Vous devez installer l’agent Mobility Service sur les machines qui feront l’objet de la migration. Les programmes d’installation de l’agent sont disponibles dans l’appliance de réplication. Recherchez le programme d’installation qui convient, puis installez l’agent sur chaque machine devant faire l’objet d’une migration. Procédez comme suit :

1. Connectez-vous à l’appliance de réplication.
2. Accédez au dossier **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Recherchez le programme d’installation correspondant à la version du système d’exploitation de la machine. Vérifiez quels sont les [systèmes d’exploitation pris en charge](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Copiez le fichier du programme d’installation sur la machine qui doit faire l’objet d’une migration.
5. Vérifiez que vous disposez de la phrase secrète qui a été générée lorsque vous avez déployé l’appliance.
    - Stockez le fichier dans un fichier texte temporaire sur la machine.
    - Vous pouvez obtenir la phrase secrète dans l’appliance de réplication. Sur la ligne de commande, exécutez **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** pour afficher la phrase secrète.
    - Ne regénérez pas la phrase secrète. Si vous la regénérez, la connectivité sera perdue et vous devrez réinscrire l’appliance de réplication.


### <a name="install-on-windows"></a>Installer sur Windows

1. Procédez de la façon suivante pour extraire le contenu du fichier du programme d’installation dans un dossier local (par exemple, C:\Temp) de la machine :

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Exécutez le programme d’installation de Mobility Service :
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Inscrivez l’agent auprès de l’appliance de réplication :
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installer sur Linux

1. Procédez de la façon suivante pour extraire le contenu du tarball du programme d’installation dans un dossier local (par exemple, /tmp/MobSvcInstaller) de la machine :
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Exécutez le script du programme d’installation :
    ```
    sudo ./install -r MS -q
    ```
3. Inscrivez l’agent auprès de l’appliance de réplication :
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Répliquer les machines

À présent, sélectionnez les machines pour la migration. 

> [!NOTE]
> Vous pouvez répliquer ensemble jusqu’à 10 machines. Si vous avez besoin d’en répliquer davantage, répliquez-les simultanément par lots de 10.

1. Dans le projet Azure Migrate > **Serveurs**, **Azure Migrate : Server Migration**, cliquez sur **Répliquer**.

    ![Répliquer des machines virtuelles](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Dans **Répliquer** > **Paramètres de la source** > **Vos machines sont-elles virtualisées ?** , sélectionnez **Non virtualisé/autre**.
3. Dans **Appliance locale**, sélectionnez le nom de l’appliance Azure Migrate que vous avez configurée.
4. Dans **vCenter Server**, spécifiez le nom du serveur vCenter qui gère les machines virtuelles ou du serveur vSphere sur lequel les machines virtuelles sont hébergées.
5. Dans **Process Server**, sélectionnez le nom de l’appliance de réplication.
6. Dans **Informations d’identification de l’invité**, spécifiez le compte d’administrateur de machine virtuelle qui sera utilisé pour l’installation Push de Mobility Service. Dans ce tutoriel, nous allons installer manuellement Mobility Service pour que vous puissiez ajouter un compte factice. Cliquez ensuite sur **Suivant : Machines virtuelles**.

    ![Répliquer des machines virtuelles](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Dans **Machines virtuelles**, dans **Importer les paramètres de migration à partir d’une évaluation ?** , conservez le paramètre par défaut **Non, je vais spécifier les paramètres de migration manuellement**.
8. Cochez chaque machine virtuelle devant faire l’objet d’une migration. Cliquez ensuite sur **Suivant : Paramètres de la cible**.

    ![Sélectionner les machines virtuelles](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Dans **Paramètres de la cible**, sélectionnez l’abonnement et la région cible vers laquelle vous allez migrer, puis spécifiez le groupe de ressources dans lequel les machines virtuelles Azure résideront après la migration.
10. Dans **Réseau virtuel**, sélectionnez le réseau virtuel/sous-réseau Azure auquel les machines virtuelles Azure seront jointes après la migration.
11. Dans **Azure Hybrid Benefit** :

    - Sélectionnez **Non** si vous ne souhaitez pas appliquer Azure Hybrid Benefit. Cliquez ensuite sur **Suivant**.
    - Sélectionnez **Oui** si vous avez des machines Windows Server couvertes par des abonnements Software Assurance ou Windows Server actifs et que vous souhaitez appliquer l’avantage aux machines que vous migrez. Cliquez ensuite sur **Suivant**.

    ![Paramètres de la cible](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Dans **Capacité de calcul**, vérifiez le nom, la taille, le type de disque du système d’exploitation et le groupe à haute disponibilité de la machine virtuelle. Les machines virtuelles doivent satisfaire aux [exigences d’Azure](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Taille de la machine virtuelle** : par défaut, Azure Migrate Server Migration choisit une taille qui correspond à la taille la plus proche dans l’abonnement Azure. Vous pouvez également choisir une taille manuelle dans **Taille de la machine virtuelle Azure**. 
    - **Disque de système d’exploitation** : spécifiez le disque du système d’exploitation (démarrage) pour la machine virtuelle. Le disque du système d’exploitation est le disque qui contient le chargeur de démarrage et le programme d’installation du système d’exploitation. 
    - **Groupe à haute disponibilité** : si la machine virtuelle doit se trouver dans un groupe à haute disponibilité Azure après la migration, spécifiez-le ici. Ce groupe doit figurer dans le groupe de ressources cible que vous spécifiez pour la migration.

    ![Paramètres de capacité de calcul](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Dans **Disques**, indiquez si les disques de machine virtuelle doivent être répliqués sur Azure, puis sélectionnez le type de disque (SSD/HDD standard ou disques managés Premium) dans Azure. Cliquez ensuite sur **Suivant**.
    - Vous pouvez exclure des disques de la réplication.
    - Si vous excluez des disques, ils ne seront pas présents sur la machine virtuelle Azure après la migration. 

    ![Paramètres des disques](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Dans **Passer en revue et démarrer la réplication**, passez en revue les paramètres, puis cliquez sur **Répliquer** pour démarrer la réplication initiale pour les serveurs.

> [!NOTE]
> Vous pouvez mettre à jour les paramètres de réplication à tout moment avant le démarrage de la réplication (**Gérer** > **Réplication des machines**). Vous ne pouvez pas changer les paramètres après le démarrage de la réplication.



## <a name="track-and-monitor"></a>Suivre et superviser

- Quand vous cliquez sur **Répliquer**, un travail Démarrer la réplication commence. 
- Une fois le travail Démarrer la réplication terminé, les machines commencent leur réplication initiale vers Azure.
- Au terme de la réplication initiale, la réplication différentielle commence. Les modifications incrémentielles apportées aux disques locaux sont répliquées régulièrement sur les disques de réplica dans Azure.


Vous pouvez suivre l’état du travail dans les notifications du portail.

Vous pouvez superviser l’état de la réplication en cliquant sur **Réplication de serveurs** dans **Azure Migrate : Server Migration**.
![Superviser la réplication](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Exécuter un test de migration


Quand la réplication différentielle commence, vous pouvez exécuter une migration de test avec les machines virtuelles avant d’exécuter une migration complète vers Azure. Avant la migration, nous vous recommandons vivement d’effectuer cette opération au moins une fois pour chaque machine.

- L’exécution d’une migration de test permet de vérifier que la migration fonctionne comme prévu, sans impact sur les machines locales qui restent opérationnelles et poursuivent la réplication. 
- Une migration de test simule la migration en créant une machine virtuelle Azure à l’aide de données répliquées (il s’agit en général d’une migration vers un réseau virtuel hors production dans votre abonnement Azure).
- Vous pouvez utiliser la machine virtuelle Azure de test répliquée pour valider la migration, effectuer des tests d’applications et résoudre les éventuels problèmes avant la migration complète.

Effectuez une migration de test de la façon suivante :


1. Dans **Objectifs de migration** > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Tester les serveurs migrés**.

     ![Tester les serveurs migrés](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Cliquez avec le bouton droit sur la machine virtuelle à tester, puis cliquez sur **Migration de test**.

    ![Migration de test](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Dans **Migration de test**, sélectionnez le réseau virtuel Azure dans lequel la machine virtuelle Azure se trouvera après la migration. Nous vous recommandons d’utiliser un réseau virtuel hors production.
4. Le travail **Migration de test** démarre. Supervisez le travail dans les notifications du portail.
5. Une fois la migration terminée, affichez la machine virtuelle Azure migrée dans **Machines virtuelles** dans le portail Azure. Le nom de la machine a le suffixe **-Test**.
6. Une fois le test terminé, cliquez avec le bouton droit sur la machine virtuelle Azure dans **Réplication des machines**, puis cliquez sur **Nettoyer la migration de test**.

    ![Nettoyer la migration](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Migrer des machines virtuelles

Après avoir vérifié que la migration de test fonctionne comme prévu, vous pouvez migrer des machines locales.

1. Dans le projet Azure Migrate > **Serveurs** > **Azure Migrate : Server Migration**, cliquez sur **Réplication de serveurs**.

    ![Réplication de serveurs](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

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
    - Envisagez de déployer [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) pour surveiller l’utilisation et les coûts des ressources.


## <a name="next-steps"></a>Étapes suivantes

Examinez le [parcours de migration cloud](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) dans le framework d’adoption du cloud Azure.
