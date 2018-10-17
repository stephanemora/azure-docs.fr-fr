---
title: Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales avec Azure Site Recovery | Microsoft Docs
description: Découvrez comment configurer la récupération d’urgence de machines virtuelles VMware locales vers Azure avec Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/10/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: b1b903b945a60cebe2001a1ae41201b9b665c556
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078815"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>Configurer la récupération d’urgence vers Azure pour des machines virtuelles VMware locales

[Azure Site Recovery](site-recovery-overview.md) contribue à votre stratégie de récupération d’urgence et de continuité d’activité en garantissant le bon fonctionnement et la disponibilité de vos applications métier pendant les interruptions planifiées et non planifiées. Site Recovery gère et orchestre la récupération d’urgence des machines locales et des machines virtuelles Azure, notamment la réplication, le basculement et la récupération.


Dans ce didacticiel, nous allons vous montrer comment configurer et activer la réplication d’une machine virtuelle VMware vers Azure, à l’aide d’Azure Site Recovery. Les didacticiels sont conçus pour vous montrer comment déployer Site Recovery avec des paramètres de base. Ils utilisent le chemin d’accès le plus simple, et n’affichent pas toutes les options. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Entrer la source et la cible de la réplication.
> * Configurer l’environnement de réplication source, y compris les composants Azure Site Recovery locaux, et l’environnement de réplication cible.
> * Créer une stratégie de réplication.
> * Activer la réplication pour une machine virtuelle.

## <a name="before-you-start"></a>Avant de commencer

Avant de commencer :

- [Examinez l’architecture](vmware-azure-architecture.md) de ce scénario de récupération d’urgence.
- Si vous voulez en apprendre plus sur la configuration de la récupération d’urgence pour les machines virtuelles VMware, examinez et utilisez les ressources suivantes :
    - [Lisez les questions fréquentes](vmware-azure-common-questions.md) sur la récupération d’urgence pour VMware.
    - [Découvrez](vmware-physical-azure-support-matrix.md) ce qui est pris en charge et requis pour VMware.
-  Lisez nos **guides d’utilisation** pour des instructions détaillées qui décrivent toutes les options de déploiement pour VMware :
    - Configurez la [source de réplication](vmware-azure-set-up-source.md) et le [serveur de configuration](vmware-azure-deploy-configuration-server.md).
    - Configurez la [cible de réplication](vmware-azure-set-up-target.md).
    - Configurez une [stratégie de réplication](vmware-azure-set-up-replication.md) et [activez la réplication](vmware-azure-enable-replication.md).


## <a name="select-a-protection-goal"></a>Sélectionner un objectif de protection

1. Dans **Archivages de Recovery Services**, sélectionnez le nom de l’archivage. Nous utilisons **ContosoVMVault** pour ce scénario.
2. Dans **Prise en main**, sélectionnez Site Recovery. Sélectionnez ensuite **Préparer l’infrastructure**.
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Sélectionnez ensuite **OK**.


## <a name="plan-your-deployment"></a>Planifier votre déploiement

Dans ce didacticiel, nous allons vous montrer comment répliquer une seule machine virtuelle, et pour **Planification de déploiement**, nous choisirons **Oui, je l’ai fait**. Si vous déployez plusieurs machines virtuelles, nous vous recommandons d’ignorer cette étape. Nous fournissons le [Planificateur de déploiement](https://aka.ms/asr-deployment-planner) pour vous aider. [En savoir plus](site-recovery-deployment-planner.md) sur cet outil.

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

En tant que première étape de déploiement, configurez votre environnement source. Vous avez besoin d’une seule machine locale à haut niveau de disponibilité pour héberger les composants Site Recovery locaux. Ces composants englobent le serveur de configuration, le serveur de processus et le serveur cible maître :

- Le serveur de configuration coordonne la communication entre les ordinateurs locaux et Azure, et gère la réplication des données.
- Le serveur de processus fait office de passerelle de réplication. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement et les envoie vers le stockage Azure. De plus, le serveur de processus installe le service Mobilité sur les machines virtuelles que vous voulez répliquer et effectue la détection automatique sur les machines virtuelles VMware locales.
- Le serveur cible maître gère les données de réplication pendant la restauration automatique à partir d’Azure.

Pour configurer le serveur de configuration comme machine virtuelle VMware hautement disponible, téléchargez un modèle OVA (Open Virtualization Application) préparé et importez-le dans VMware pour créer la machine virtuelle. Après avoir configuré le serveur de configuration, inscrivez-le dans le coffre. Après l’inscription, Site Recovery détecte les machines virtuelles VMware locales.

> [!TIP]
> Ce tutoriel utilise un modèle OVA pour créer la machine virtuelle VMware du serveur de configuration. Si vous n’y parvenez pas, vous pouvez [configurer manuellement le serveur de configuration](physical-manage-configuration-server.md).

> [!TIP]
> Dans ce didacticiel, Site Recovery télécharge et installe MySQL dans le serveur de configuration. Si vous ne voulez pas que Site Recovery le fasse, vous pouvez le faire manuellement. [Plus d’informations](vmware-azure-deploy-configuration-server.md#configure-settings)


### <a name="download-the-vm-template"></a>Télécharger le modèle de machine virtuelle

1. Dans le coffre, allez dans **Préparer l’infrastructure** > **Source**.
2. Dans **Préparer la source**, sélectionnez **+Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration pour VMware** s’affiche dans **Type de serveur**.
4. Téléchargez le modèle OVF pour le serveur de configuration.

 > [!TIP]
 >Vous pouvez télécharger la dernière version du modèle de serveur de configuration directement à partir du [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver).

>[!NOTE]
La licence fournie avec le modèle OVF est une licence d’évaluation valide pendant 180 jours. Les clients doivent activer les fenêtres avec une licence provisionnée.

## <a name="import-the-template-in-vmware"></a>Importer le modèle dans VMware

1. Connectez-vous au serveur VMware vCenter ou à l’hôte vSphere ESXi, à l’aide du client VMWare vSphere.
2. Dans le menu **Fichier**, sélectionnez **Déployer le modèle OVF** pour démarrer l’**Assistant Déploiement du modèle OVF**. 

     ![Modèle OVF](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. Dans **Sélectionner une source**, entrez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, sélectionnez **Suivant**.
5. Dans **Sélectionner le nom et le dossier** et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage**, pour des performances optimales, sélectionnez **Remise à zéro rapide d’un approvisionnement important** dans **Sélectionner le format de disque virtuel**.
7. Dans le reste des pages de l’Assistant, acceptez les paramètres par défaut.
8. Sur la fenêtre **Prêt à terminer**, pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.

    > [!TIP]
  Si vous souhaitez ajouter une carte réseau supplémentaire, décochez la case **Mettre sous tension après le déploiement** > **Terminer**. Par défaut, le modèle contient une seule carte réseau. mais vous pouvez en ajouter d’autres après le déploiement.

## <a name="add-an-additional-adapter"></a>Ajouter une carte supplémentaire

Le cas échéant, ajoutez une carte d’interface réseau supplémentaire au serveur de configuration avant d’inscrire le serveur dans le coffre. L’ajout de cartes supplémentaires n’est pas pris en charge après l’inscription.

1. Dans l’inventaire du client vSphere, faites un clic droit sur la machine virtuelle, puis sélectionnez **Modifier les paramètres**.
2. Dans **Matériel**, sélectionnez **Ajouter** > **Adaptateur Ethernet**. Sélectionnez ensuite **Suivant**.
3. Sélectionnez un type d’adaptateur et un réseau. 
4. Pour connecter la carte réseau virtuelle lorsque la machine virtuelle est allumée, sélectionnez **Connecter à la mise sous tension**. Sélectionnez **Suivant** > **Terminer**. Sélectionnez ensuite **OK**.


## <a name="register-the-configuration-server"></a>Inscrire le serveur de configuration 

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et entrez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur.
4. L’outil de configuration d’Azure Site Recovery démarre en quelques secondes la première fois que vous vous connectez.
5. Saisissez un nom utilisé pour inscrire le serveur de configuration sur Site Recovery. Sélectionnez ensuite **Suivant**.
6. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, sélectionnez **Connecter** pour vous connecter à votre abonnement Azure. Les informations d’identification doivent avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.
7. L’outil effectue des tâches de configuration, puis redémarre.
8. Reconnectez-vous à la machine. En quelques secondes, l’Assistant Gestion de serveur de configuration démarre automatiquement.

### <a name="configure-settings-and-add-the-vmware-server"></a>Configurer les paramètres et ajouter le serveur VMware

1. Dans l’Assistant Gestion de serveur de configuration, sélectionnez **Configurer la connectivité**, puis la carte d’interface réseau utilisé par le serveur de processus pour recevoir le trafic de réplication des machines virtuelles. Ensuite, sélectionnez **Enregistrer**. Vous ne pouvez pas modifier ce paramètre une fois qu’il a été configuré.
2. Dans **Sélectionner le coffre Recovery Services**, sélectionnez votre abonnement Azure ainsi que le groupe de ressources et le coffre appropriés.
3. Dans **Installer le logiciel tiers**, acceptez le contrat de licence. Sélectionnez **Télécharger et installer** pour installer MySQL Server. Si vous avez placé MySQL dans le chemin d’accès, cette étape est ignorée.
4. Sélectionnez **Installer VMware PowerCLI**. Assurez-vous que toutes les fenêtres du navigateur sont fermées avant de continuer. Sélectionnez **Continuer**.
5. Dans **Valider la configuration de l’appliance**, les conditions préalables sont vérifiées avant que vous ne poursuiviez.
6. Dans **Configurer le serveur vCenter Server/vSphere ESXi**, entrez le nom de domaine complet ou l’adresse IP du serveur vCenter ou de l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Entrez le numéro de port sur lequel le serveur écoute. Entrez un nom convivial à utiliser pour le serveur VMware dans le coffre.
7. Entrez les informations d’identification utilisateur à utiliser par le serveur de configuration pour se connecter au serveur VMware. Vérifiez que le nom d’utilisateur et le mot de passe sont corrects et qu’il fait partie du groupe Administrateurs de la machine virtuelle à protéger. Site Recovery utilise ces informations d’identification pour détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. Sélectionnez **Ajouter**, puis **Continuer**.
8. Dans **Configurer les informations d’identification de la machine virtuelle**, entrez le nom d’utilisateur et le mot de passe qui seront utilisés pour installer automatiquement le service Mobilité sur les machines virtuelles, une fois la réplication activée.
    - Pour les machines Windows, le compte doit disposer des privilèges d’administrateur local sur les machines que vous souhaitez répliquer.
    - Pour Linux, fournissez les détails du compte racine.
9. Sélectionnez **Finaliser la configuration** pour terminer l’inscription.
10. Une fois l’inscription terminée, dans le portail Azure, vérifiez que le serveur de configuration et le serveur VMware sont répertoriés sur la page **Source** dans le coffre. Sélectionnez ensuite **OK** pour configurer les paramètres cibles.


Site Recovery se connecte aux serveurs VMware en utilisant les paramètres spécifiés et découvre les machines virtuelles.

> [!NOTE]
> L’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, sélectionnez **Serveurs de configuration** > ***nom du serveur*** > **Actualiser le serveur**.

## <a name="set-up-the-target-environment"></a>Configurer l’environnement cible

Sélectionnez et vérifiez les ressources cibles.

1. Sélectionnez **Préparer l’infrastructure** > **Cible**. Sélectionnez l’abonnement Azure à utiliser. Nous utilisons un modèle Resource Manager.
2. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles. Vous devez en disposer lors de la configuration des composants Azure dans le [premier didacticiel](tutorial-prepare-azure.md) de cette série.

   ![Onglet cible](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>Créer une stratégie de réplication

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Toutes les ressources**.
2. Sélectionnez le coffre Recovery Services (**ContosoVMVault** dans ce didacticiel).
3. Pour créer une stratégie de réplication, sélectionnez **Infrastructure Azure Site Recovery** > **Stratégies de réplication** > **+Stratégie de réplication**.
4. Dans **Créer une stratégie de réplication**, entrez le nom de stratégie. Nous utilisons **VMwareRepPolicy**.
5. Dans **Seuil RPO**, utilisez la valeur par défaut de 60 minutes. Cette valeur définit la fréquence à laquelle les points de récupération sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
6. Dans **Rétention des points de récupération**, précisez le temps de rétention de chaque point de récupération. Pour ce didacticiel, nous utilisons 72 heures. Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre de rétention.
7. Dans **Fréquence des captures instantanées cohérentes au niveau de l’application**, spécifiez la fréquence de création des captures instantanées cohérentes au niveau de l’application. Nous utilisons la valeur par défaut, 60 minutes. Sélectionnez **OK** pour créer la stratégie.

   ![Créer une stratégie de réplication](./media/vmware-azure-tutorial/replication-policy.png)

- La stratégie est automatiquement associée au serveur de configuration.
- Par défaut, une stratégie de correspondance est automatiquement créée pour la restauration automatique. Par exemple, si la stratégie de réplication est **rep-policy**, la stratégie de restauration automatique correspond alors à **rep-policy-failback**. Cette stratégie n’est utilisée qu’à partir du moment où vous initiez une restauration automatique à partir d’Azure.

## <a name="enable-replication"></a>Activer la réplication

Pour activer la réplication, vous pouvez effectuer les étapes suivantes :

1. Sélectionnez **Répliquer l’application** > **Source**.
2. Dans **Source**, sélectionnez **Local**, puis le serveur de configuration dans **Emplacement source**.
3. Dans **Type de machine**, sélectionnez **Machines virtuelles**.
4. Dans **Hyperviseur vCenter/vSphere**, sélectionnez l’hôte vSphere, ou le serveur vCenter qui gère l’hôte.
5. Sélectionnez le serveur de processus (installé par défaut sur la machine virtuelle du serveur de configuration). Sélectionnez ensuite **OK**.
6. Dans **Cible**, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Nous utilisons le modèle de déploiement Resource Manager. 
7. Sélectionnez le compte de stockage Azure que vous voulez utiliser pour répliquer les données, ainsi que le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement.
8. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer les paramètres réseau à l’ensemble des machines virtuelles pour lesquelles vous activez la réplication. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine.
9. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, sélectionnez chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Sélectionnez ensuite **OK**.
10. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte que le serveur de processus doit utiliser pour installer automatiquement le service Mobilité sur la machine.
11. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée.
12. Sélectionnez **Activer la réplication**. Site Recovery installe automatiquement le service Mobilité quand la réplication est activée pour une machine virtuelle.
13. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.
- Il peut être nécessaire d’attendre 15 minutes ou plus avant que les modifications prennent effet et qu’elles apparaissent dans le portail.
- Pour surveiller les machines virtuelles que vous ajoutez, consultez l’heure de la dernière découverte des machines virtuelles dans **Serveurs de configuration** > **Dernier contact à**. Pour ajouter des machines virtuelles sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (sans le sélectionner) et sélectionnez **Actualiser**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Effectuer un test de récupération d’urgence](site-recovery-test-failover-to-azure.md)
