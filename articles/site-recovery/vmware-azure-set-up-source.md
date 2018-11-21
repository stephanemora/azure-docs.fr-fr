---
title: Configurer l’environnement source et le serveur de configuration pour la reprise d’activité de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs'
description: Cet article explique comment configurer votre environnement local et votre serveur de configuration pour la reprise d’activité de machines virtuelles VMware sur Azure avec Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: e09e93ef85449c51e35b8da7ad93ee7088a0e7b4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566310"
---
# <a name="set-up-the-source-environment-and-configuration-server"></a>Configurer l’environnement source et le serveur de configuration

Cet article explique comment configurer votre environnement local source pour la reprise d’activité de machines virtuelles VMware sur Azure avec [Azure Site Recovery](site-recovery-overview.md). Il comprend des étapes pour la configuration d’un ordinateur local en tant que serveur de configuration Site Recovery et la détection automatique des machines virtuelles locales. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez avoir effectué les opérations suivantes :

- Planification du déploiement de reprise d’activité à l’aide du [Planificateur de déploiement Azure Site Recovery](site-recovery-deployment-planner.md). Le Planificateur vous aide à vérifier que vous disposez de suffisamment de ressources et de bande passante pour la reprise d’activité et l’objectif de point de récupération (RPO) dans votre organisation.
- [Configuration des ressources Azure](tutorial-prepare-azure.md) dans le [portail Azure](http://portal.azure.com).
- [Installation des ressources VMware locales](vmware-azure-tutorial-prepare-on-premises.md), notamment un compte dédié pour la détection automatique des machines virtuelles VMWare.
- Vous pouvez [consulter les questions les plus fréquentes](vmware-azure-common-questions.md#configuration-server) sur la gestion et le déploiement du serveur de configuration.


## <a name="choose-protection-goals"></a>Choisir les objectifs de protection

1. Dans **Archivages de Recovery Services**, sélectionnez le nom de l’archivage. 
2. Dans **Prise en main**, sélectionnez Site Recovery. Sélectionnez ensuite **Préparer l’infrastructure**.
3. Dans **Objectif de protection** > **Où se trouvent vos machines**, sélectionnez **Local**.
4. Dans **Où voulez-vous répliquer vos machines**, sélectionnez **Dans Azure**.
5. Dans **Vos machines sont-elles virtualisées**, sélectionnez **Oui, avec l’hyperviseur VMware vSphere**. Sélectionnez ensuite **OK**.

## <a name="about-the-configuration-server"></a>À propos du serveur de configuration

Vous devez déployer un serveur de configuration local quand vous configurez la reprise d’activité de machines virtuelles VMware sur Azure.

- Le serveur de configuration coordonne la communication entre les machines locales VMware et Azure. Il gère également la réplication des données.
- Vous déployez le serveur de configuration en tant que machine virtuelle VMware.
- Site Recovery fournit un modèle OVA que vous téléchargez à partir du portail Azure et importez dans vCenter Server pour configurer la machine virtuelle du serveur de configuration.
- [Découvrez-en plus](vmware-azure-architecture.md) sur les composants et les processus du serveur de configuration.


>[!NOTE]
N’utilisez pas ces instructions si vous déployez le serveur de configuration pour la reprise d’activité de machines physiques locales sur Azure. Pour ce scénario, suivez [cet article](physical-azure-set-up-source.md).


## <a name="before-you-deploy-the-configuration-server"></a>Avant de déployer le serveur de configuration

Si vous installez le serveur de configuration en tant que machine virtuelle VMware à l’aide du modèle OVA, la machine virtuelle sera installée avec tous les prérequis. Si vous souhaitez en savoir plus sur les prérequis, consultez les articles suivants.

- [Découvrez-en plus](vmware-azure-configuration-server-requirements.md) sur le matériel, les logiciels et les exigences de capacité du serveur de configuration.
- Si vous répliquez plusieurs machines virtuelles VMware, lisez les [considérations sur la planification de la capacité](site-recovery-plan-capacity-vmware.md) et exécutez l’outil [Planificateur de déploiement Azure Site Recovery](site-recovery-deployment-planner.md) pour la réplication VMware.
- La licence Windows pour le modèle OVA est une licence d’évaluation valide pendant 180 jours. Au-delà de ce délai, vous devez activer Windows avec une licence valide. 
- Le modèle OVA fournit un moyen simple de configurer le serveur de configuration en tant que machine virtuelle VMware. Si pour une raison quelconque vous devez configurer la machine virtuelle VMware sans le modèle, passez en revue les prérequis et suivez les instructions manuelles fournies dans [cet article](physical-azure-set-up-source.md).
- Votre compte Azure doit être autorisé à créer des applications Azure AD.


>[!IMPORTANT]
Le serveur de configuration doit être déployé comme décrit dans cet article. La copie ou le clonage d’un serveur de configuration existant n’est pas pris en charge.

### <a name="set-up-azure-account-permissions"></a>Configurer les autorisations de compte Azure

Un administrateur global/locataire peut affecter au compte des autorisations pour créer des applications Azure AD, ou lui attribuer le rôle Développeur d’applications (qui dispose des autorisations).


L’administrateur général/locataire peut accorder des autorisations au compte comme suit :

1. Dans Azure AD, l’administrateur général/locataire doit accéder à **Azure Active Directory** > **Utilisateurs** > **Paramètres utilisateur**.
2. L’administrateur doit affecter la valeur **Oui** à **Inscriptions des applications**.

 > [!NOTE]
 > Il s’agit d’un paramètre par défaut qui n’est pas sensible. [Plus d’informations](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)

L’administrateur général/locataire dispose également des autorisations nécessaires pour affecter le rôle au compte. [Plus d’informations](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 


## <a name="download-the-ova-template"></a>Télécharger le modèle OVA

1. Dans le coffre, allez dans **Préparer l’infrastructure** > **Source**.
2. Dans **Préparer la source**, sélectionnez **+Serveur de configuration**.
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration pour VMware** s’affiche dans **Type de serveur**.
4. Téléchargez le modèle OVA (Open Virtualization Application) pour le serveur de configuration.

  > [!TIP]
>Vous pouvez également télécharger la dernière version du modèle de serveur de configuration à partir du [Centre de téléchargement Microsoft](https://aka.ms/asrconfigurationserver).


## <a name="import-the-ova-template-in-vmware"></a>Importer le modèle OVA dans VMware

1. Connectez-vous au serveur VMware vCenter ou à l’hôte vSphere ESXi, à l’aide du client VMWare vSphere.
2. Dans le menu **Fichier**, sélectionnez **Déployer le modèle OVF** pour démarrer l’Assistant Déploiement du modèle OVF.

     ![Modèle OVF](./media/vmware-azure-deploy-configuration-server/vcenter-wizard.png)

3. Dans **Sélectionner une source**, saisissez l’emplacement du modèle OVF téléchargé.
4. Dans **Examiner les détails**, sélectionnez **Suivant**.
5. Dans **Sélectionner le nom et le dossier** et **Sélectionner la configuration**, acceptez les paramètres par défaut.
6. Dans **Sélectionner un stockage**, et pour des performances optimales, sélectionnez **Remise à zéro rapide d’un approvisionnement important** dans **Sélectionner le format de disque virtuel**.
7. Dans le reste des pages de l’assistant, acceptez les paramètres par défaut.
8. Dans **Prêt à terminer**, pour configurer la machine virtuelle avec les paramètres par défaut, sélectionnez **Mettre sous tension après le déploiement** > **Terminer**.
9. Par défaut, la machine virtuelle est déployée avec une seule carte réseau. Si vous souhaitez ajouter une carte réseau supplémentaire, décochez la case **Mettre sous tension après le déploiement** et cliquez sur **Terminer**. Ensuite, appliquez la procédure suivante.

## <a name="add-an-adapter-to-the-configuration-server"></a>Ajouter une carte réseau au serveur de configuration

Si vous souhaitez ajouter une carte d’interface réseau supplémentaire au serveur de configuration, faites-le avant d’inscrire le serveur dans le coffre. L’ajout de cartes supplémentaires n’est pas pris en charge après l’inscription.

1. Dans l’inventaire du client vSphere, faites un clic droit sur la machine virtuelle, puis sélectionnez **Modifier les paramètres**.
2. Dans **Matériel**, sélectionnez **Ajouter** > **Adaptateur Ethernet**. Sélectionnez ensuite **Suivant**.
3. Sélectionnez un type d’adaptateur et un réseau.
4. Pour connecter la carte réseau virtuelle lorsque la machine virtuelle s’allume, sélectionnez **Connecter à la mise sous tension**. Ensuite, sélectionnez **Suivant** > **Terminer** > **OK**.

## <a name="register-the-configuration-server"></a>Inscrire le serveur de configuration 
Activez la machine virtuelle et inscrivez le serveur de configuration dans le coffre Site Recovery.

1. À partir de la console du client vSphere de VMware, activez la machine virtuelle.
2. La machine virtuelle démarre sur une expérience d’installation de Windows Server 2016. Acceptez le contrat de licence et entrez un mot de passe administrateur.
3. Une fois l’installation terminée, connectez-vous à la machine virtuelle en tant qu’administrateur.



## <a name="set-up-the-configuration-server"></a>Configurer le serveur de configuration

Dans le cadre du déploiement, vous devez installer MySQL sur la machine virtuelle du serveur de configuration. Vous pouvez accomplir cette opération de différentes manières :

- Laisser Site Recovery télécharger et installer MySQL. Si vous souhaitez utiliser cette option, vous n’avez rien à faire avant de commencer l’installation du serveur de configuration.
- Télécharger et installer MySQL manuellement : avant de configurer le serveur de configuration, téléchargez le programme d’installation de MySQL et placez-le dans le dossier **C:\Temp\ASRSetup**. Maintenant, installez MySQL. 
- Télécharger manuellement et laisser Site Recovery procéder à l’installation. Avant de configurer le serveur de configuration, téléchargez le programme d’installation de MySQL et placez-le dans le dossier **C:\Temp\ASRSetup**.


1. L’outil de configuration d’Azure Site Recovery démarre la première fois que vous vous connectez à la machine virtuelle.
2. Spécifiez un nom utilisé pour inscrire le serveur de configuration dans le coffre Site Recovery. Sélectionnez ensuite **Suivant**.
3. L’outil vérifie que la machine virtuelle peut se connecter à Azure. Une fois la connexion établie, sélectionnez **Connecter** pour vous connecter à votre abonnement Azure. Notez que le compte doit avoir accès au coffre dans lequel vous souhaitez inscrire le serveur de configuration.
4. L’outil effectue des tâches de configuration, puis redémarre.
5. Reconnectez-vous à la machine. L’Assistant Gestion de serveur de configuration démarre automatiquement au bout de quelques secondes.
6. Dans l’Assistant, sélectionnez **Configurer la connectivité**.
7. Sélectionnez la carte d’interface réseau utilisée par le serveur de processus (qui s’exécute par défaut sur le serveur de configuration) pour recevoir le trafic de réplication des machines virtuelles.
8. Ensuite, sélectionnez **Enregistrer**. Notez que vous ne pouvez pas modifier les paramètres du coffre une fois que le serveur de configuration a été inscrit. 
9. Dans **Sélectionner le coffre Recovery Services**, connectez-vous à Microsoft Azure et sélectionnez votre abonnement Azure ainsi que le groupe de ressources et le coffre souhaités. 
10. Dans **Installer des logiciels tiers**, installez MySQL :
     - Si Site Recovery gère le téléchargement et l’installation de MySQL, cliquez sur **Télécharger et installer**. Attendez que l’installation se termine, puis continuez avec l’Assistant.
     - Si vous avez téléchargé MySQL et que Site Recovery l’installera, cliquez sur **Télécharger et installer**. Attendez que l’installation se termine, puis continuez avec l’Assistant.
     - Si vous avez téléchargé et installé manuellement MySQL, cliquez sur **Télécharger et installer**. L’application s’affiche comme **Déjà installé**. Continuez avec l’Assistant.
11. Dans **Valider la configuration de l’appliance**, le programme d’installation vérifie les prérequis avant que vous ne poursuiviez. 
12. Dans **Configurer le serveur vCenter/vSphere ESXi**, entrez le nom de domaine complet ou l’adresse IP du serveur vCenter, ou l’hôte vSphere, sur lequel sont situées les machines virtuelles que vous souhaitez répliquer. Entrez le port écouté par le serveur et spécifiez un nom convivial pour le serveur VMware dans le coffre.
13. Entrez les informations d’identification qui seront utilisées par le serveur de configuration pour se connecter au serveur VMware et détecter automatiquement les machines virtuelles VMware disponibles pour la réplication. Sélectionnez **Ajouter** >  **Continuer**. Les informations d’identification sont enregistrées localement.
14. Dans **Configurer les informations d’identification de la machine virtuelle**, spécifiez les informations d’identification qui seront utilisées par Site Recovery pour installer automatiquement le service Mobilité une fois la réplication activée pour une machine virtuelle.
    - Pour les machines Windows, le compte doit disposer des privilèges d’administrateur local sur les machines que vous souhaitez répliquer.
    - Pour Linux, fournissez les détails du compte racine.
15. Sélectionnez **Finaliser la configuration** pour terminer l’inscription.
16. Une fois l’inscription terminée, ouvrez le portail Azure et vérifiez que le serveur de configuration et le serveur VMware sont affichés dans **Coffre Recovery Services** > **Gérer** > **Infrastructure Site Recovery** > **Serveurs de configuration**.


## <a name="exclude-antivirus-on-the-configuration-server"></a>Exclure un logiciel antivirus sur le serveur de configuration

Si un logiciel antivirus est en cours d’exécution sur la machine virtuelle de serveur de configuration, assurez-vous que les dossiers suivants sont exclus des opérations antivirus. Cela garantira le bon fonctionnent de la réplication et de la connectivité : 

- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\Program Files\Microsoft Azure Site Recovery Provider
- C:\Program Files\Microsoft Azure Site Recovery Configuration Manager
- C:\Program Files\Microsoft Azure Site Recovery Error Collection Tool
- C:\thirdparty
- C:\Temp
- C:\strawberry
- C:\ProgramData\MySQL
- C:\Program Files (x86)\MySQL
- C:\ProgramData\ASR
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\inetpub
- Répertoire d’installation de site Recovery. Par exemple : E:\Program Files (x86)\Microsoft Azure Site Recovery


## <a name="next-steps"></a>Étapes suivantes
- Si vous rencontrez des difficultés, consultez les [questions les plus fréquentes](vmware-azure-common-questions.md#configuration server) et l’article sur le [dépannage](vmware-azure-troubleshoot-configuration-server.md) du serveur de configuration.
- Maintenant, [configurez votre environnement cible](./vmware-azure-set-up-target.md) dans Azure.
