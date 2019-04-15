---
title: Résolution de problèmes Azure Migrate | Microsoft Docs
description: Fournit une vue d'ensemble des problèmes connus dans le service Azure Migrate et des conseils de dépannage pour les erreurs courantes.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: dff3c96cf3ac8eea7c1160ee1834cc70390c0333
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652635"
---
# <a name="troubleshoot-azure-migrate"></a>Résoudre les problèmes d’Azure Migrate

## <a name="troubleshoot-common-errors"></a>Résolution des erreurs courantes

[Azure Migrate](migrate-overview.md) évalue les charges de travail sur site pour la migration vers Azure. Utilisez cet article pour résoudre les problèmes lors du déploiement et de l'utilisation d'Azure Migrate.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>J’utilise l’OVA qui assure la découverte continue de mon environnement local, mais les machines virtuelles supprimées de mon environnement local apparaissent toujours dans le portail.

L’appliance de découverte continue collecte uniquement les données de performances en continu. Elle ne détecte pas les changements de configuration dans l’environnement local (par exemple, ajout ou suppression de machine virtuelle, ajout de disque, etc.). En cas de modification de configuration de l’environnement local, vous pouvez procéder aux opérations suivantes pour refléter les modifications dans le portail :

- Ajout d’éléments (machines virtuelles, disques, cœurs, etc.) : pour refléter ces modifications dans le portail Azure, vous pouvez arrêter la détection de l’appliance, puis la redémarrer. Cela garantit que les modifications sont mises à jour dans le projet Azure Migrate.

   ![Arrêter la découverte](./media/troubleshooting-general/stop-discovery.png)

- Suppression de machines virtuelles : en raison de la façon dont l’appliance est conçue, la suppression de machines virtuelles n’apparaît pas, même si vous arrêtez et redémarrez la détection. Cela est dû au fait que les données de détections ultérieures sont ajoutées, et non pas remplacées, aux détections plus anciennes. Dans ce cas, vous pouvez simplement ignorer la machine virtuelle dans le portail en la supprimant de votre groupe et en recalculant l’évaluation.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Suppression de projets Azure Migrate et espace de travail Log Analytics associé

Lorsque vous supprimez un projet Azure Migrate, c’est le projet de migration avec tous les groupes et toutes les évaluations qui sont supprimés. Toutefois, si vous avez attaché un espace de travail Log Analytics au projet, celui-ci n’est pas automatiquement supprimé. En effet, le même espace de travail Log Analytics peut être utilisé dans plusieurs cas d’usage. Si vous souhaitez aussi supprimer l’espace de travail Log Analytics, vous devez le faire manuellement.

1. Accédez à l’espace de travail Log Analytics associé au projet.
   a. Si vous n’avez pas encore supprimé le projet de migration, le lien menant à l’espace de travail se trouve dans la page Vue d’ensemble du projet, à la section Essentials (Fondamentaux).

   ![Espace de travail Log Analytics](./media/troubleshooting-general/LA-workspace.png)

   b. Si vous avez déjà supprimé le projet de migration, cliquez sur **Groupes de ressources** dans le volet gauche du portail Azure et accédez au groupe de ressources dans lequel l’espace de travail a été créé, puis accédez à l’espace.
2. Suivez les instructions [de cet article](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) pour supprimer l’espace de travail.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Échec de la création du projet de migration avec l’erreur *Les demandes doivent comporter des en-têtes d’identité de l’utilisateur*

Ce problème peut se produire pour les utilisateurs qui n’ont pas accès au locataire (tenant) Azure Active Directory (Azure AD) de l’organisation. Lorsqu’un utilisateur est ajouté pour la première fois à un locataire Azure AD, il reçoit une invitation à rejoindre le locataire par e-mail. Il doit ouvrir le message et accepter l’invitation pour être correctement ajouté au locataire. Si vous ne trouvez pas l’e-mail, demandez à un utilisateur qui a déjà accès au locataire de vous renvoyer l’invitation en suivant les étapes décrites [ici](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Dès que vous avez reçu l’e-mail d’invitation, ouvrez-le et cliquez sur le lien à l’intérieur pour accepter l’invitation. Après cela, vous devrez vous déconnecter du Portail Azure et vous reconnecter ; actualiser le navigateur ne fonctionnera pas. Vous pourrez ensuite essayer de créer le projet de migration.

### <a name="i-am-unable-to-export-the-assessment-report"></a>Je ne parviens pas à exporter le rapport d’évaluation.

Si vous ne pouvez pas exporter le rapport d’évaluation à partir du portail, essayez d’utiliser l’API REST ci-dessous pour obtenir une URL de téléchargement pour le rapport d’évaluation.

1. Installez *armclient* sur votre ordinateur (s’il n’y est pas encore) :

   a. Dans la fenêtre d'invite de commandes d’administrateur, exécutez la commande suivante : ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

   b. Dans la fenêtre Windows PowerShell d’administrateur, exécutez la commande suivante : ```choco install armclient```

2. Obtenir l’URL de téléchargement pour le rapport d’évaluation à l’aide de l’API REST Azure Migrate

   a.    Dans la fenêtre Windows PowerShell d’administrateur, exécutez la commande suivante : ```armclient login```

        This opens the Azure login pop-up where you need to sign in to Azure.

   b.    Dans la même fenêtre PowerShell, exécutez la commande suivante pour obtenir l’URL de téléchargement pour le rapport d’évaluation (remplacez les paramètres d’URI par les valeurs appropriées, exemple d’API de requête ci-dessous).

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

      Exemple de requête et de sortie :

      ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
   esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
   018_12_16_21/downloadUrl?api-version=2018-02-02
   {
   "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
   "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. Copiez l’URL de la réponse et ouvrez-la dans un navigateur pour télécharger le rapport d’évaluation.

4. Une fois le rapport téléchargé, utilisez Excel pour parcourir le dossier téléchargé et ouvrez le fichier dans Excel pour le consulter.

### <a name="performance-data-for-cpu-memory-and-disks-is-showing-up-as-zeroes"></a>Données de performances pour le processeur, mémoire et disques ne s’affichent sous forme de zéros

Azure Migrate en permanence les profils de l’environnement local pour collecter des données de performances des machines virtuelles en local. Si vous venez de démarrer la découverte de votre environnement, vous devez attendre au moins un jour pour la collecte de données de performances à faire. Si une évaluation est créée sans attendre un jour, les mesures de performances seront afficheront sous forme de zéros. Après avoir attendu une journée, vous pouvez créer une nouvelle évaluation ou mettre à jour de l’évaluation existante à l’aide de l’option « Recalculer » dans le rapport d’évaluation.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>J’ai spécifié une zone géographique Azure ; au moment de la création d’un projet de migration, comment faire pour trouver la région Azure exacte où sont stockées les métadonnées découvertes ?

Vous pouvez accéder à la section **Bases** de la page **Vue d’ensemble** du projet pour identifier l’emplacement exact auquel les métadonnées sont stockées. L’emplacement est sélectionné de façon aléatoire dans la zone géographique par Azure Migrate et vous ne pouvez pas le modifier. Si vous souhaitez uniquement créer un projet dans une région spécifique, vous pouvez utiliser les API REST pour créer le projet de migration, puis passer la région souhaitée.

   ![Emplacement du projet](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Problèmes liés au collecteur

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Échec du déploiement d’Azure Migrate Collector avec l’erreur : Le fichier manifeste indiqué n'est pas valide : entrée de manifeste OVF non valide.

1. Confirmez que le fichier OVA Azure Migrate Collector est correctement téléchargé en vérifiant sa valeur de hachage. Reportez-vous à cet [article](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) pour vérifier la valeur de hachage. Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si vous faites à nouveau face à un échec et que vous utilisez un client VMware vSphere pour le déploiement du fichier OVF, essayez plutôt d’utiliser le client Web vSphere. Si le problème persiste, essayez d’utiliser un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi en suivant les étapes ci-dessous :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) à l’aide du client web (https:// <*adresse IP de l’hôte*> /ui).
   - Accédez à l’accueil, puis à « Inventaire ».
   - Cliquez sur Fichier > Déployer un modèle OVF > Parcourir. Recherchez le fichier OVA et terminez le déploiement.
4. Si le déploiement échoue toujours, contactez le support Azure Migrate.

### <a name="unable-to-select-the-azure-cloud-in-the-appliance-fails-with-error-azure-cloud-selection-failed"></a>Impossible de sélectionner le Azure cloud dans l’appliance, échoue avec l’erreur « Échec de la sélection en nuage Azure »

Il s’agit d’un problème connu et un correctif est disponible pour le problème. Téléchargez le [dernière mise à niveau des bits](https://docs.microsoft.com/azure/migrate/concepts-collector-upgrade#continuous-discovery-upgrade-versions) pour l’appliance et la mise à jour de l’appliance à appliquer le correctif.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Le collecteur ne parvient pas à se connecter à Internet

Cela peut se produire lorsque l’ordinateur que vous utilisez est derrière un proxy. Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
Si vous utilisez un proxy de pare-feu basé sur une URL pour contrôler la connectivité sortante, veillez à inclure dans la liste verte les URL nécessaires suivantes :

**URL** | **Objectif**  
--- | ---
*. portal.azure.com | Indispensable pour vérifier la connectivité avec le service Azure et valider les problèmes de synchronisation de l’heure.
*. oneget.org | Indispensable pour télécharger le module vCenter PowerCLI reposant sur powershell.

**Le collecteur ne parvient pas à se connecter à Internet en raison d’un échec de validation du certificat**

Cette erreur est susceptible de se produire si vous utilisez un proxy d’interception pour vous connecter à Internet et que vous n’avez pas importé le certificat de proxy sur la machine virtuelle collecteur. Pour importer le certificat de proxy, suivez les étapes détaillées [ici](https://docs.microsoft.com/azure/migrate/concepts-collector).

**Le collecteur ne peut pas se connecter au projet à l'aide de l'ID du projet et de la clé copiée sur le portail.**

Assurez-vous d'avoir copié et collé les bonnes informations. Pour résoudre les problèmes, installez Microsoft Monitoring Agent (MMA) et vérifiez si la MMA peut se connecter au projet de la manière suivante :

1. Sur la machine virtuelle du collecteur, téléchargez [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Pour démarrer l’installation, double-cliquez sur le fichier téléchargé.
3. Dans la page de **bienvenue** du programme d'installation, cliquez sur **Suivant**. Sur la page **Termes du contrat de licence**, cliquez sur **J’accepte** pour accepter la licence.
4. Dans **Dossier de destination**, conservez ou modifiez le dossier d’installation par défaut > **Suivant**.
5. Dans **Options d’installation de l’agent**, sélectionnez **Azure Log Analytics** > **Suivant**.
6. Cliquez sur **Ajouter** pour ajouter un nouvel espace de travail Log Analytics. Collez l'ID et la clé du projet que vous avez copiés. Cliquez ensuite sur **Suivant**.
7. Vérifiez que l'agent peut se connecter au projet. Dans le cas contraire, vérifiez les paramètres. Si l'agent peut se connecter mais que le collecteur ne le peut pas, contactez le support.


### <a name="error-802-date-and-time-synchronization-error"></a>Erreur 802 : erreur de synchronisation de la date et de l’heure

L'horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l'heure actuelle. Modifiez l'heure de l'horloge sur la machine virtuelle du collecteur afin qu'elle corresponde à l'heure actuelle, en procédant comme suit :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
2. Pour vérifier le fuseau horaire, exécutez w32tm /tz.
3. Pour synchroniser l'heure, exécutez w32tm /resync.

### <a name="vmware-powercli-installation-failed"></a>Échec de l’installation de VMware PowerCLI

Le collecteur Azure Migrate télécharge PowerCLI et l’installe sur l’appliance. L’échec de l’installation de PowerCLI peut être dû à des points de terminaison inaccessibles pour le référentiel PowerCLI. Pour résoudre le problème, essayez d’installer manuellement PowerCLI dans la machine virtuelle du collecteur en suivant les étapes ci-dessous :

1. Ouvrez Windows PowerShell en mode administrateur.
2. Accédez au répertoire C:\ProgramFiles\ProfilerService\VMWare\Scripts\.
3. Exécutez le script InstallPowerCLI.ps1.

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>Une erreur interne UnhandledException s’est produite : System.IO.FileNotFoundException

Ce problème peut être lié à l’installation de VMware PowerCLI. Suivez les étapes ci-dessous pour le résoudre :

1. Si vous n’utilisez pas la dernière version de l’appliance collecteur, [mettez à niveau Collector vers la dernière version](https://aka.ms/migrate/col/checkforupdates) et vérifiez si le problème a été résolu.
2. Si vous avez déjà la dernière version du collecteur, suivez les étapes ci-dessous pour effectuer une nouvelle installation de PowerCLI :

   a. Fermez le navigateur web dans l’appliance.

   b. Arrêtez le service « Azure Migrate Collector » en accédant au Gestionnaire de services Windows (ouvrez « Exécuter » et tapez « services.msc » pour ouvrir le Gestionnaire de services Windows). Cliquez avec le bouton droit sur le service Azure Migrate Collector et cliquez sur Arrêter.

   c. Supprimez tous les dossiers commençant par « VMware » à partir des emplacements suivants : C:\Program Files\WindowsPowerShell\Modules  
        C:\Program Files (x86)\WindowsPowerShell\Modules

   d. Redémarrez le service « Azure Migrate Collector » dans le Gestionnaire de services Windows (ouvrez « Exécuter » et tapez « services.msc » pour ouvrir le Gestionnaire de services Windows). Cliquez avec le bouton droit sur le service Azure Migrate Collector et cliquez sur Démarrer.

   e. Double-cliquez sur le raccourci sur le Bureau « Run collector (Exécuter le collecteur) » pour démarrer l’application de collecteur. L’application de collecteur doit automatiquement télécharger et installer la version requise de PowerCLI.

3. Si la méthode ci-dessus ne résout pas le problème, suivez les étapes une à c ci-dessus, puis installez manuellement PowerCLI dans l’appliance en procédant comme suit :

   a. Nettoyer PowerCLI incomplète de tous les fichiers d’installation en suivant les étapes #a à #c à l’étape #2 ci-dessus.

   b. Cliquez sur Démarrer > Exécuter > Open PowerShell(x86) Windows en mode administrateur

   c. Exécutez la commande suivante :  Install-Module « VMWare.VimAutomation.Core » - RequiredVersion « 6.5.2.6234650 » (type « A » lorsqu’il demande confirmation)

   d. Redémarrez le service « Azure Migrate Collector » dans le Gestionnaire de services Windows (ouvrez « Exécuter » et tapez « services.msc » pour ouvrir le Gestionnaire de services Windows). Cliquez avec le bouton droit sur le service Azure Migrate Collector et cliquez sur Démarrer.

   e. Double-cliquez sur le raccourci sur le Bureau « Run collector (Exécuter le collecteur) » pour démarrer l’application de collecteur. L’application de collecteur doit automatiquement télécharger et installer la version requise de PowerCLI.

4. Si vous ne parvenez pas à télécharger le module dans l’appliance en raison de problèmes de pare-feu, téléchargez et installez le module dans un ordinateur qui a accès à internet en procédant comme suit :

    a. Nettoyer PowerCLI incomplète de tous les fichiers d’installation en suivant les étapes #a à #c à l’étape #2 ci-dessus.

    b. Cliquez sur Démarrer > Exécuter > Open PowerShell(x86) Windows en mode administrateur

    c. Exécutez la commande suivante :  Install-Module « VMWare.VimAutomation.Core » - RequiredVersion « 6.5.2.6234650 » (type « A » lorsqu’il demande confirmation)

    d. Copiez tous les modules commençant par « VMware » à partir de « C:\Program Files (x86) \WindowsPowerShell\Modules » au même emplacement sur la machine virtuelle collector.

    e. Redémarrez le service « Azure Migrate Collector » dans le Gestionnaire de services Windows (ouvrez « Exécuter » et tapez « services.msc » pour ouvrir le Gestionnaire de services Windows). Cliquez avec le bouton droit sur le service Azure Migrate Collector et cliquez sur Démarrer.

    f. Double-cliquez sur le raccourci sur le Bureau « Run collector (Exécuter le collecteur) » pour démarrer l’application de collecteur. L’application de collecteur doit automatiquement télécharger et installer la version requise de PowerCLI.

### <a name="error-unabletoconnecttoserver"></a>Erreur UnableToConnectToServer

Impossible de se connecter à vCenter Server « ServerName.com: 9443 » en raison de l’erreur : Il n’existait pas de point de terminaison à l’écoute sur https://Servername.com:9443/sdk pouvant accepter le message.

Vérifiez que vous exécutez la dernière version de l’appliance du collecteur ; si ce n’est pas le cas, [mettez-la à niveau](https://docs.microsoft.com/azure/migrate/concepts-collector).

Si le problème se produit toujours dans la dernière version, cela peut être dû au fait que l’ordinateur collecteur ne parvient pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué est incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tentera de se connecter au port numéro 443.

1. Essayez d’effectuer un test ping sur Servername.com à partir de l’ordinateur collecteur.
2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter sur l’adresse IP.
3. Identifiez le numéro de port correct pour se connecter au serveur vCenter.
4. Enfin, vérifiez si le serveur vCenter est en cours d’exécution.

### <a name="antivirus-exclusions"></a>Exclusions d’antivirus

Pour renforcer l’appliance Azure Migrate, vous devez exclure de l’analyse antivirus les dossiers suivants qui appartiennent à l’appliance :

- Dossier contenant les fichiers binaires du service Azure Migrate. Exclure tous les sous-dossiers.
  %ProgramFiles%\ProfilerService  
- Application web Azure Migrate. Exclure tous les sous-dossiers.
  %SystemDrive%\inetpub\wwwroot
- Cache local pour la base de données et les fichiers journaux. Le service Azure Migrate nécessite un accès en lecture/écriture pour ce dossier.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>Problèmes de visualisation des dépendances

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Je n’arrive pas à trouver la fonctionnalité de visualisation des dépendances pour les projets Azure Government.

Azure Migrate dépend de Service Map pour la fonctionnalité de visualisation des dépendances et, étant donné que Service Map est actuellement indisponible dans Azure Government, cette fonctionnalité n’est pas disponible dans Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>J’ai installé MMA (Microsoft Monitoring Agent) et l’agent de dépendances sur mes machines virtuelles locales, mais les dépendances apparaissent maintenant dans le portail Azure Migrate.

Une fois que vous avez installé les agents, Azure Migrate prend généralement 15 à 30 minutes pour afficher les dépendances dans le portail. Si vous avez attendu plus de 30 minutes, vérifiez que l’agent MMA peut communiquer avec l’espace de travail OMS en suivant les étapes ci-dessous :

Pour les machines virtuelles Windows :
1. Accédez au **Panneau de configuration** et lancez **Microsoft Monitoring Agent**.
2. Accédez à l’onglet **Azure Log Analytics (OMS)** dans la fenêtre contextuelle des propriétés MMA.
3. Vérifiez que **l’état** de l’espace de travail est vert.
4. Si l’état n’est pas vert, essayez de supprimer l’espace de travail et de le rajouter à MMA.
        ![État MMA](./media/troubleshooting-general/mma-status.png)

Pour les machines virtuelles, vérifiez que les commandes d’installation pour les agents MMA et de dépendances se sont déroulées correctement.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quels sont les systèmes d’exploitation pris en charge par MMA ?

La liste des systèmes d’exploitation Windows pris en charge par MMA est disponible [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
La liste des systèmes d’exploitation Linux pris en charge par MMA est disponible [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quels sont les systèmes d’exploitation pris en charge par l’agent de dépendances ?

La liste des systèmes d’exploitation Windows pris en charge par l’agent de dépendances est disponible [ici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
La liste des systèmes d’exploitation Linux pris en charge par l’agent de dépendances est disponible [ici](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Je n’arrive pas à visualiser des dépendances dans Azure Migrate pour une durée supérieure à une heure ?
Azure Migrate vous permet de visualiser des dépendances pour une durée maximale d’une heure. Même si Azure Migrate vous permet de revenir en arrière dans l’historique jusqu’à une date spécifique sur une période d’un mois, la durée maximale pour laquelle vous pouvez visualiser des dépendances est 1 heure. Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement pour une fenêtre d’une heure. Toutefois, vous pouvez utiliser les journaux Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) pendant une durée plus longue.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Je n’arrive pas à visualiser les dépendances de groupes ayant plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances de groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ayant 10 machines virtuelles maximum. Si vous avez ce type de groupe, nous vous recommandons de diviser le groupe en groupes plus petits pour visualiser les dépendances.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>J’ai installé des agents et utilisé la visualisation des dépendances pour créer des groupes. Maintenant, après le basculement, les machines affichent l’action « Installer l’agent » au lieu de « Afficher les dépendances ».
* Après un basculement planifié ou non planifié, les machines sur site sont désactivées et les machines équivalentes sont lancées dans Azure. Ces machines acquièrent une adresse MAC différente. Elles peuvent acquérir une adresse IP différente selon que l'utilisateur a choisi de conserver ou non l'adresse IP sur site. Si les adresses MAC et IP diffèrent, Azure Migrate n'associe pas les machines sur site avec les données de dépendance Service Map et demande à l'utilisateur d'installer des agents au lieu d'afficher les dépendances.
* Après un basculement de test, les machines sur site restent activées comme prévu. Les machines équivalentes lancées dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que les blocs utilisateur sortants Azure Monitor enregistre le trafic provenant de ces machines, Azure Migrate n’associe pas les machines sur site avec les données de dépendance Service Map et demande à l’utilisateur pour installer des agents au lieu d’afficher les dépendances.

## <a name="troubleshoot-azure-readiness-issues"></a>Résoudre les problèmes de disponibilité Azure

**Problème** | **Correctif**
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Il est recommandé de convertir le type de démarrage en BIOS avant d’exécuter une migration. <br/><br/>Vous pouvez utiliser [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) pour effectuer la migration de ces machines virtuelles, car il convertira leur type de démarrage en BIOS pendant la migration.
Systèmes d’exploitation Windows pris en charge sous condition | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement (CSA) pour bénéficier d’une [prise en charge dans Azure](https://aka.ms/WSosstatement). Envisagez de mettre à niveau le système d’exploitation avant la migration vers Azure.
Systèmes d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](https://aka.ms/WSosstatement). Envisagez de mettre à niveau le système d’exploitation de la machine avant la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau le système d’exploitation de la machine avant la migration vers Azure.
Systèmes d’exploitation Linux non approuvés | La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est assurée par Azure. Envisagez de mettre à niveau le système d’exploitation vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation spécifié de la machine virtuelle est « Autre » dans vCenter Server, ce qui explique pourquoi Azure Migrate ne peut pas déterminer si la machine virtuelle est prête pour Azure. Vérifiez que le système d’exploitation s’exécutant dans la machine est [pris en charge](https://aka.ms/azureoslist) par Azure avant de procéder à sa migration.
Nombre de bits du système d’exploitation non pris en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent démarrer dans Azure, mais il est recommandé de mettre à niveau le système d’exploitation de la machine virtuelle de 32 bits à 64 bits avant la migration vers Azure.
Nécessite un abonnement Visual Studio. | Le système d’exploitation de client Windows qui s’exécute dans la machine n’est pris en charge que dans l’abonnement Visual Studio.
Nous n'avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage requis | Les performances de stockage (IOPS/débit) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les besoins de stockage de la machine avant la migration.
Nous n'avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau requis | Les performances réseau (entrée/sortie) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les exigences de mise en réseau de la machine.
Nous n'avons pas trouvé de machine virtuelle dans l'emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Un ou plusieurs disques ne sont pas adaptés | Un ou plusieurs disques attachés à la machine virtuelle ne répondent pas à la configuration requise pour Azure. Pour chaque disque attaché à la machine virtuelle, assurez-vous que la taille du disque est < 4 To, sinon, réduisez la taille du disque avant de migrer vers Azure. Assurez-vous que les performances (IOPS/débit) requises par chaque disque sont prises en charge par [les disques de la machine virtuelle gérée](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) Azure.   
Un ou plusieurs adaptateurs réseau ne sont pas adaptés | Supprimez les cartes réseau non utilisées de la machine avant la migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés de la machine avant la migration.
La taille du disque dépasse la limite autorisée | Azure prend en charge les disques dont la taille ne dépasse pas 4 To. Réduisez la taille du disque à moins de 4 To avant la migration.
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l'adéquation du disque en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n'a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs de la machine sur site avant de migrer.
Impossible de déterminer l'adéquation de la machine virtuelle en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l'adéquation pour un ou plusieurs disques en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l'adéquation pour un ou plusieurs adaptateurs réseau en raison d'une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.


## <a name="collect-logs"></a>Collecter les journaux d’activité

**Comment collecter des journaux sur la machine virtuelle du collecteur ?**

La journalisation est activée par défaut. Les journaux d’activité se trouvent dans les emplacements suivants :

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Pour collecter le suivi d'événements pour Windows, procédez comme suit :

1. Sur la machine virtuelle du collecteur, ouvrez une fenêtre de commande PowerShell.
2. Exécutez **Get-EventLog -LogName Application | export-csv eventlog.csv**.

**Comment collecter les journaux du trafic réseau du portail ?**

1. Ouvrez le navigateur et connectez-vous [au portail](https://portal.azure.com).
2. Appuyez sur F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
3. Cliquez sur l'onglet **Réseau**, puis commencez la capture du trafic réseau :
   - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si ce n'est pas le cas, cliquez sur le cercle noir pour commencer
   - Dans Microsoft Edge/Internet Explorer, l'enregistrement devrait commencer automatiquement. Si ce n'est pas le cas, cliquez sur le bouton de lecture vert.
4. Essayez de reproduire l'erreur.
5. Après avoir rencontré l'erreur pendant l'enregistrement, arrêtez l'enregistrement et enregistrez une copie de l'activité enregistrée :
   - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette opération compresse et exporte les journaux d’activité en tant que fichier .har.
   - Dans Microsoft Edge/Internet Explorer, cliquez sur l'icône **Exporter le trafic capturé**. Cette opération compresse et exporte les journaux.
6. Accédez à l'onglet **Console** pour vérifier la présence d'avertissements ou d'erreurs. Pour enregistrer le journal de la console :
   - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
   - Dans Microsoft Edge/Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**.
7. Fermez les outils de développement.

## <a name="collector-error-codes-and-recommended-actions"></a>Codes d’erreur du collecteur et actions recommandées

| Code d'erreur | Nom de l’erreur   | Message   | Causes possibles | Action recommandée  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | Le collecteur a expiré.                                                        | Le collecteur a expiré.                                                                                    | Veuillez télécharger une nouvelle version du collecteur, puis réessayez.                                                                                      |
| 751       | UnableToConnectToServer        | Impossible de se connecter à vCenter Server « %Name; » en raison de l’erreur suivante : %ErrorMessage;     | Pour plus d’informations, consultez le message d’erreur.                                                             | Résolvez le problème et réessayez.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | Le serveur « %Name; » n’est pas un vCenter Server.                                  | Fournissez les détails de vCenter Server.                                                                       | Retentez l’opération avec les détails corrects de vCenter Server.                                                                                   |
| 753       | InvalidLoginCredentials        | Connexion impossible au vCenter Server « %Name; » en raison de l’erreur %ErrorMessage; | Échec de la connexion à vCenter Server en raison d’informations d’identification de connexion non valides.                             | Vérifiez que les informations d’identification de connexion fournies sont correctes.                                                                                    |
| 754       | NoPerfDataAvailable           | Les données de performances ne sont pas disponibles.                                               | Vérifiez le niveau des statistiques dans vCenter Server. Il doit être défini sur 3 pour que les données de performances soient disponibles. | Modifiez le niveau de statistiques pour le définir sur 3 (pour une durée de 5 minutes, 30 minutes et 2 heures) et recommencez après avoir attendu au moins un jour.                   |
| 756       | NullInstanceUUID               | Encountered a machine with null InstanceUUID (A trouvé un ordinateur avec InstanceUUID null)                                  | vCenter Server peut avoir un objet inapproprié.                                                      | Résolvez le problème et réessayez.                                                                                                           |
| 757       | VMNotFound                     | Virtual machine is not found (La machine virtuelle est introuvable)                                                  | La machine virtuelle a peut-être été supprimée : %VMID;                                                                | Vérifiez que les machines virtuelles sélectionnées lors de l’inventaire vCenter existent pendant la détection                                      |
| 758       | GetPerfDataTimeout             | VCenter request timed out. (Le délai d’attente de la requête vCenter a expiré.) Message %Message;                                  | Les informations d’identification de vCenter Server sont incorrectes.                                                              | Vérifiez les informations d’identification de vCenter Server et assurez-vous que vCenter Server est accessible. Retentez l’opération. Si le problème persiste, contactez le support. |
| 759       | VmwareDllNotFound              | DLL VMWare.Vim introuvable.                                                     | PowerCLI n’est pas installé correctement.                                                                   | Vérifiez si PowerCLI est correctement installé. Retentez l’opération. Si le problème persiste, contactez le support.                               |
| 800       | ServiceError                   | Le service Azure Migrate Collector n’est pas en cours d’exécution.                               | Le service Azure Migrate Collector n’est pas en cours d’exécution.                                                       | Utilisez services.msc pour démarrer le service et recommencez l’opération.                                                                             |
| 801       | PowerCLIError                  | L’installation de VMware PowerCLI a échoué.                                          | L’installation de VMware PowerCLI a échoué.                                                                  | Retentez l’opération. Si le problème persiste, installez-le manuellement et recommencez l’opération.                                                   |
| 802       | TimeSyncError                  | L’heure n’est pas synchronisée avec le serveur de temps Internet.                            | L’heure n’est pas synchronisée avec le serveur de temps Internet.                                                    | Assurez-vous que l’heure sur l’ordinateur est définie correctement pour le fuseau horaire de l’ordinateur et recommencez l’opération.                                 |
| 702       | OMSInvalidProjectKey           | Clé spécifiée du projet non valide.                                                | Clé spécifiée du projet non valide.                                                                        | Recommencez l’opération avec la clé de projet correcte.                                                                                              |
| 703       | OMSHttpRequestException        | Erreur lors de l’envoi de la demande. Message %Message;                                | Vérifiez l’ID et la clé du projet et veillez à ce que le point de terminaison soit accessible.                                       | Retentez l’opération. Si le problème persiste, contactez le support technique Microsoft.                                                                     |
| 704       | OMSHttpRequestTimeoutException | HTTP request timed out. (La requête HTTP a expiré.) Message %Message;                                     | Vérifiez l’ID et la clé du projet et veillez à ce que le point de terminaison soit accessible.                                       | Retentez l’opération. Si le problème persiste, contactez le support technique Microsoft.                                                                     |
