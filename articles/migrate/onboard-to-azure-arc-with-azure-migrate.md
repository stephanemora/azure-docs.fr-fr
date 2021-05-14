---
title: Intégration de serveurs locaux dans l’environnement virtuel VMware à Azure Arc
description: Intégration de serveurs locaux dans l’environnement virtuel VMware à Azure Arc
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/27/2021
ms.openlocfilehash: 27b1253b2d2808e01e5dae542e82211d96add216
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108281078"
---
# <a name="onboard-on-premises-servers-in-vmware-virtual-environment-to-azure-arc"></a>Intégration de serveurs locaux dans l’environnement virtuel VMware à Azure Arc   

Cet article explique comment intégrer des machines virtuelles VMware locales à Azure Arc pour la gestion Azure à l’aide de l’outil Azure Migrate : découverte et évaluation. 

Azure Arc vous permet de gérer votre parc informatique hybride depuis un volet unique en étendant l’expérience de gestion Azure à vos serveurs locaux qui ne sont pas des candidats idéaux pour la migration. [En savoir plus sur Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/overview). 

## <a name="before-you-get-started"></a>Avant de commencer

- [Passez en revue les exigences](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#prerequisites) pour découvrir les serveurs fonctionnant dans un environnement VMware avec l’outil Azure Migrate : découverte et évaluation.  
- Préparez [VMware vCenter](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#prepare-vmware) pour l’utilisation et passez en revue les [exigences liées à VMware](migrate-support-matrix-vmware.md#vmware-requirements) pour effectuer l’inventaire logiciel. L’inventaire logiciel doit être terminé pour commencer l’intégration des serveurs découverts à Azure Arc.   
- Examinez la [configuration requise pour la découverte des applications](migrate-support-matrix-vmware.md#application-discovery-requirements) avant de lancer un inventaire logiciel des serveurs. PowerShell version 3.0 ou ultérieure doit être installé sur les serveurs Windows.
- Veillez à vérifier les [prérequis pour Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#prerequisites) et à passer en revue les éléments suivants :
    - L’intégration à Azure Arc ne peut être lancée qu’une fois que la découverte et l’inventaire logiciel de vCenter Server sont terminés. L’inventaire logiciel peut durer jusqu’à 6 heures après son activation
    -  L’agent [Azure Arc Hybrid Connected Machine Agent](https://docs.microsoft.com/azure/azure-arc/servers/learn/quick-enable-hybrid-vm) sera installé sur les serveurs découverts lors du processus d’intégration à Azure Arc. Veillez à fournir des informations d’identification avec des autorisations d’administrateur sur les serveurs pour installer et configurer l’agent. Sur Linux, fournissez le compte racine. Sur Windows, fournissez un compte membre du groupe Administrateurs locaux. 
    - Vérifiez que les serveurs exécutent [un système d’exploitation pris en charge](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#supported-operating-systems).
    - Assurez-vous que votre compte Azure dispose des [rôles Azure requis](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#required-permissions).
    - Vérifiez que [les URL requises](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#networking-configuration) ne sont pas bloquées si les serveurs détectés se connectent via un pare-feu ou un serveur proxy pour communiquer par Internet.
    - Passez en revue les [régions prises en charge](https://docs.microsoft.com/azure/azure-arc/servers/overview#supported-regions) par Azure Arc. 
    - Les serveurs compatibles avec Azure Arc prennent en charge jusqu’à 5 000 instances de machine dans un groupe de ressources.


## <a name="set-up-the-azure-migrate-project"></a>Configurer un projet Azure Migrate  

1. Avant de commencer, préparez le [compte d’utilisateur Azure](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#prepare-an-azure-user-account) et vérifiez que vous disposez des [rôles nécessaires](./create-manage-projects.md#verify-permissions) dans l’abonnement pour créer les ressources requises par Azure Migrate. 
2. [Utilisez cet article](https://docs.microsoft.com/azure/migrate/create-manage-projects) pour configurer un nouveau projet Azure Migrate en y ajoutant l’outil Azure Migrate : découverte et évaluation.  

    > [!Note]
    > Vous pouvez également utiliser un projet Migrate existant et intégrer l’inventaire des serveurs découverts à Azure Arc. Pour cela, lancez le gestionnaire de configuration de l’appliance à partir du serveur de votre appliance et assurez-vous que les services sont mis à jour vers les dernières versions. [En savoir plus](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-upgrades) <br/> <br/> Ensuite, [suivez ces instructions](#onboard-to-azure-arc) pour intégrer vos serveurs.  

## <a name="deploy-and-register-the-azure-migrate-appliance"></a>Déployer et inscrire l’appliance Azure Migrate 

L’outil Azure Migrate : découverte et évaluation utilise une appliance Azure Migrate légère. L’appliance effectue la détection du serveur, puis envoie la configuration et les métadonnées de performances du serveur à Azure Migrate. 

Avant de configurer l’appliance 

1. [Examinez](migrate-appliance.md#appliance---vmware) la configuration requise pour le déploiement de l’appliance Azure Migrate.
2. Passez en revue les URL Azure auxquelles l’appliance devra accéder dans les clouds [publics](migrate-appliance.md#public-cloud-urls) et les [clouds Government](migrate-appliance.md#government-cloud-urls). 
3. Notez [les conditions d’accès aux ports](migrate-support-matrix-vmware.md#port-access-requirements) pour l’appliance. 


Ensuite,

- Suivez cet article pour [configurer l’appliance Azure Migrate](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#set-up-the-appliance) pour démarrer la découverte vCenter Server. Pour déployer l’appliance, vous pouvez télécharger et importer un modèle OVA dans VMware afin de créer un serveur s’exécutant dans votre serveur vCenter Server.  
- Après avoir déployé l’appliance, vous devez l’inscrire avec le projet avant de lancer la découverte. Suivez [ces instructions](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate) pour inscrire l’appliance. 

## <a name="configure-the-appliance-and-start-discovery"></a>Configurer l’appliance et démarrer la découverte  

Utilisez [cet article](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#start-continuous-discovery) pour configurer l’appliance Azure Migrate et lancer la découverte de vCenter Server. 

Quand vous configurez l’appliance pour la découverte, vous devez spécifier les détails suivants dans le gestionnaire de configuration de l’appliance :

- détails du vCenter Server auquel vous souhaitez vous connecter ;  
- informations d’identification de vCenter Server étendues pour découvrir les serveurs dans votre environnement VMware ; 
- Informations d’identification du serveur avec autorisations d’administrateur. Pour l’intégration à Azure Arc, il est nécessaire que les informations d’identification disposent d’autorisations d’administrateur sur le serveur pour installer et configurer l’agent Azure Arc Hybrid Connect Machine. [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.

Une fois la découverte réussie, il faut environ 15 minutes pour que les serveurs détectés s’affichent dans le portail.


## <a name="onboard-to-azure-arc"></a>Intégrer à Azure Arc

>[!Important]
>L’inventaire logiciel doit être terminé avant l’intégration des serveurs découverts à Azure Arc. 

Une fois la découverte de vCenter Server terminée, l’inventaire logiciel (découverte des applications installées) se lance automatiquement. Au cours de l’inventaire logiciel, les informations d’identification de serveur fournies seront itérées et validées par rapport aux serveurs découverts. Vous pouvez démarrer l’intégration une fois que l’inventaire logiciel est terminé et que les informations d’identification ont été mappées. L’inventaire logiciel peut durer jusqu’à 6 heures après son activation.  
1. Accédez au panneau **Intégrer à Azure Arc**. 

    ![Intégration à Arc](./media/onboard-to-azure-arc-with-azure-migrate/azure-arc-onboarding-panel-after-being-enabled.png)

2. Fournissez **l’abonnement** et le **groupe de ressources** dans lesquels vous voulez que le serveur soit managé dans Azure.

3. Dans la liste déroulante **Région**, sélectionnez la région Azure dans laquelle stocker les métadonnées des serveurs.

4. Fournissez les détails du **principal de service Azure Active Directory** pour l’intégration à grande échelle. Consultez cet article pour [créer un principal de service à l’aide du portail Azure ou d’Azure PowerShell.](https://docs.microsoft.com/azure/azure-arc/servers/onboard-service-principal#create-a-service-principal-for-onboarding-at-scale) <br/>

    Les entrées suivantes sont nécessaires :
    - **ID du répertoire (locataire)**  : [identificateur unique (GUID)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in) qui représente votre instance dédiée Azure AD. 
    - **ID d’application (client)**  : [identificateur unique (GUID)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in) qui représente l’ID d’application du principal de service.
    - **Secret du principal du service (secret de l’application)**  : [secret du client](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#option-2-create-a-new-application-secret) pour l’authentification basée sur un mot de passe. 
    
5. _Facultatif_ : fournissez **l’adresse IP du serveur proxy**, ou bien le nom et le **numéro de port** si vos serveurs découverts requièrent un serveur proxy pour se connecter à Internet. Saisissez la valeur au format `http://<proxyURL>:<proxyport>`. Ce serveur proxy utilisé par les serveurs découverts peut être différent du serveur proxy requis par le serveur de l’appliance pour se connecter à Internet (comme indiqué dans la section des prérequis du gestionnaire de configuration de l’appliance).   

    > [!Note]
    > La configuration de l’authentification proxy n’est pas prise en charge. 

6. Sélectionnez **Démarrer l’intégration** pour initier le processus d’intégration à Azure Arc. Le processus d’intégration prend un certain temps. Une fois l’intégration terminée, vous voyez s’afficher l’état d’avancement et un rapport détaillé sur l’état de l’intégration.    

    ![Rapport d’intégration à Arc](./media/onboard-to-azure-arc-with-azure-migrate/onboarding-completion-report.png)

    > [!Note]
    > Si votre connexion expire, sélectionnez **Se reconnecter**. Cela a pour effet d’ouvrir une fenêtre modale avec le code de l’appareil. Sélectionnez **Copier le code et se connecter** pour copier le code de l’appareil et ouvrir une invite de connexion Azure dans un nouvel onglet de navigateur. Si la fenêtre ne s’affiche pas, vérifiez que vous avez désactivé le bloqueur de fenêtres publicitaires dans le navigateur.

7. Une fois l’intégration terminée, accédez à la [page d’accueil d’Azure Arc](https://portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/servers) pour afficher et gérer vos serveurs intégrés.   

8. Consultez le rapport d’intégration détaillé pour comprendre l’état d’intégration de vos serveurs et prendre les mesures appropriées. 

> [!Note]
> L’appliance Azure Migrate lance des sessions WinRM pour exécuter le script d’intégration Azure Arc. Assurez-vous qu’il n’existe aucun paramètre limitant l’accès au service WinRM sur les serveurs cibles.    


## <a name="next-steps"></a>Étapes suivantes 

- Pour plus d’informations et de détails sur les erreurs, consultez le rapport d’intégration détaillé. Résolvez les erreurs le cas échéant afin d’intégrer correctement les serveurs. 
- Vous pouvez sélectionner **Démarrer l’intégration** pour exécuter de nouveau le processus d’intégration à Azure Arc. La tentative d’intégration s’effectuera pour tous les serveurs récemment découverts et pour ceux qui n’ont pas pu être intégrés correctement lors de l’exécution précédente.   

## <a name="troubleshooting-azure-arc-onboarding-errors"></a>Résolution des problèmes d’intégration à Azure Arc

Si vous recevez une erreur lors de l’intégration à Azure Arc à l’aide de l’appliance Azure Migrate, la section suivante peut vous aider à identifier la cause probable et les étapes suggérées pour résoudre votre problème. 

Si vous ne voyez pas le code d’erreur indiqué ci-dessous ou si le code d’erreur commence par **_AZCM_**, reportez-vous à [ce guide de résolution des problèmes liés à Azure Arc ](https://docs.microsoft.com/azure/azure-arc/servers/troubleshoot-agent-onboard)

### <a name="error-60001---unabletoconnecttophysicalserver"></a>Erreur 60001 : UnableToConnectToPhysicalServer  

**Causes possibles**  
Soit les [prérequis](https://go.microsoft.com/fwlink/?linkid=2134728) pour se connecter au serveur n’ont pas été remplis, soit il y a des problèmes de réseau lors de la connexion au serveur, par exemple certains paramètres de proxy.

**Actions recommandées**   
- Assurez-vous que le serveur remplit les prérequis pour la découverte et l’évaluation, comme indiqué [ici](https://go.microsoft.com/fwlink/?linkid=2134728). 
- Vérifiez que vous avez choisi la méthode d’authentification correcte sur l’appliance pour la connexion au serveur. 
   > [!Note] 
   > Azure Migrate prend en charge à la fois l’authentification basée sur un mot de passe et basée sur une clé SSH pour les serveurs Linux.
- Si le problème persiste, soumettez une demande de support Microsoft en fournissant l’ID de la machine de l’appliance (disponible dans le pied de page du gestionnaire de configuration de l’appliance).    

### <a name="error-60002---invalidservercredentials"></a>Erreur 60002 : InvalidServerCredentials  

**Causes possibles**  
Impossible de se connecter au serveur. Soit vous avez fourni des informations d’identification incorrectes sur l’appliance, soit les informations d’identification fournies précédemment ont expiré.

**Actions recommandées**  
- Vérifiez que vous avez fourni les informations d’identification correctes pour le serveur sur l’appliance. Vous pouvez vérifier cela en essayant de vous connecter au serveur à l’aide de ces informations d’identification.
- Si les informations d’identification ajoutées sont incorrectes ou ont expiré, modifiez les informations d’identification sur l’appliance et revalidez les serveurs ajoutés. Si la validation réussit, le problème est résolu.
- Si le problème persiste, soumettez une demande de support Microsoft en fournissant l’ID de la machine de l’appliance (disponible dans le pied de page du gestionnaire de configuration de l’appliance).

### <a name="error-60005---sshoperationtimeout"></a>Erreur 60005 : SSHOperationTimeout  

**Causes possibles**  
- L’opération a pris plus de temps que prévu en raison de problèmes de temps de réponse du réseau ou de l’absence des dernières mises à jour sur le serveur. 

**Actions recommandées**  
- Assurez-vous que les dernières mises à jour du noyau et du système d’exploitation sont installées sur le serveur concerné.
- Assurez-vous qu’il n’y a pas de latence réseau entre l’appliance et le serveur. Il est recommandé que l’appliance et le serveur source se trouvent sur le même domaine pour éviter les problèmes de latence.
- Connectez-vous au serveur concerné à partir de l’appliance et exécutez les commandes [décrites ici](https://go.microsoft.com/fwlink/?linkid=2152600) pour vérifier si elles retournent des données null ou vides.
- Si le problème persiste, soumettez une demande de support Microsoft en fournissant l’ID de la machine de l’appliance (disponible dans le pied de page du gestionnaire de configuration de l’appliance).  

### <a name="error-60108---softwareinventorycredentialnotassociated"></a>Erreur 60108 : SoftwareInventoryCredentialNotAssociated  

**Causes possibles**  
- Aucune information d’identification associée au serveur n’a été trouvée.

**Actions recommandées**  
- L’inventaire logiciel doit être terminé pour commencer l’intégration des serveurs découverts à Azure Arc. [En savoir plus](https://docs.microsoft.com/azure/migrate/how-to-discover-applications#add-credentials-and-initiate-discovery)
- Assurez-vous que les informations d’identification fournies sur le gestionnaire de configuration de l’appliance sont valides et que le serveur est accessible à l’aide des informations d’identification.
- Revenez au gestionnaire de configuration de l’appliance pour fournir un autre ensemble d’informations d’identification ou en modifier un existant.  

### <a name="error-60109---arcosnotsupported"></a>Erreur 60109 : ArcOsNotSupported  

**Causes possibles**  
- Le serveur héberge un système d’exploitation non pris en charge pour l’intégration à Azure Arc.

**Actions recommandées**  
- [Passez en revue les systèmes d’exploitation pris en charge](https://docs.microsoft.com/azure/azure-arc/servers/agent-overview#supported-operating-systems) par Azure Arc. 
 
### <a name="error-10002---scriptexecutiontimedoutonvm"></a>Erreur 10002 : ScriptExecutionTimedOutOnVm  

**Causes possibles**  
- La tâche d’intégration ne s’est pas terminée à temps. L’exécution a pris plus de temps que prévu. 

**Actions recommandées**  
- Le problème doit se résoudre automatiquement dans quelques instants. Si le problème persiste, contactez le support technique Microsoft.  
 
### <a name="error-50000---accessdenied"></a>Erreur 50000 : AccessDenied 

**Causes possibles**  
- L’opération n’a pas pu s’effectuer en raison d’un accès interdit sur le serveur. Le compte d’utilisateur fourni sur l’appliance pour accéder au serveur ne dispose pas des autorisations requises, ou les informations d’identification sont incorrectes. Code d’erreur WinRM : 0x80070005

**Actions recommandées**  
- Validez les causes possibles et recommencer l’opération. Si le problème persiste, contactez le support.

### <a name="error-9609516000960078---nullresultunhandledexceptionserverunknownerrorunhandlederror"></a>Erreur 960/951/60009/60078 : NullResult/UnhandledException/ServerUnknownError/UnhandledError

**Causes possibles**  
- Échec de l’opération en raison d’une erreur interne. 

**Actions recommandées**  
- Réessayez l’opération après un certain temps. Si le problème persiste, contactez le support en fournissant l’ID de la machine de l’appliance (disponible dans le **pied de page** du gestionnaire de configuration de l’appliance).
