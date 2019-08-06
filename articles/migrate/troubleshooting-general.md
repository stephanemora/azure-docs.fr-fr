---
title: Résolution de problèmes Azure Migrate | Microsoft Docs
description: Fournit une vue d'ensemble des problèmes connus dans le service Azure Migrate et des conseils de dépannage pour les erreurs courantes.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 15d3809b9a028fd2495c504e9bf19251dd051520
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372584"
---
# <a name="troubleshoot-azure-migrate"></a>Résoudre les problèmes d’Azure Migrate

[Azure Migrate](migrate-services-overview.md) fournit un hub d’outils Microsoft pour l’évaluation et la migration, et des offres de fournisseurs de logiciels indépendants tiers. Ce document fournit de l’aide sur la résolution des erreurs avec Azure Migrate : Server Assessment et Azure Migrate : Server Migration.

## <a name="azure-migrate-project-issues"></a>Problèmes des projets Azure Migrate

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Je ne parviens pas à trouver mon projet Azure Migrate existant.

Il existe [deux versions](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate. Selon la version dans laquelle vous avez créé le projet, suivez les étapes ci-dessous pour rechercher le projet :

1. Si vous recherchez un projet créé avec la version précédente (ancienne expérience) d’Azure Migrate, suivez les étapes ci-dessous pour rechercher le projet.

    1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
    2. Dans le tableau de bord Azure Migrate, vous voyez une bannière donnant des informations sur l’accès aux anciens projets. Vous voyez cette bannière seulement si vous avez créé un projet avec l’ancienne expérience. Cliquez sur la bannière.

    ![Accéder aux projets existants](./media/troubleshooting-general/access-existing-projects.png)

    3. Vous voyez maintenant la liste des projets existants créés avec la version précédente d’Azure Migrate.

2. Si vous recherchez un projet créé avec la version actuelle (nouvelle expérience), suivez les étapes ci-dessous pour rechercher le projet.

    1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
    2. Dans le tableau de bord Azure Migrate, accédez à la page **Serveurs** dans le volet gauche, puis sélectionnez **Modifier** dans le coin supérieur droit :

    ![Passer à un projet Azure Migrate existant](./media/troubleshooting-general/switch-project.png)

    3. Sélectionnez l’**Abonnement** et le **Projet Migrate** appropriés.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Je ne parviens pas à créer un deuxième projet Azure Migrate.

Suivez les étapes ci-dessous pour créer un projet Azure Migrate.

1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate, accédez à la page **Serveurs** dans le volet gauche, puis sélectionnez **Modifier** dans le coin supérieur droit :

   ![Modifier un projet Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Pour créer un projet, sélectionnez **cliquez ici** comme indiqué dans la capture d’écran ci-dessous :

   ![Créer un deuxième projet Azure Migrate](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quelles sont les zones géographiques Azure prises en charge par Azure Migrate ?

Vous pouvez trouver la liste pour [VMware ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) et pour [Hyper-V ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Suppression de projets Azure Migrate et espace de travail Log Analytics associé

Quand vous supprimez un projet Azure Migrate, le projet de migration et les métadonnées sur les machines découvertes sont supprimés. Cependant, si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment, il n’est pas supprimé automatiquement. En effet, le même espace de travail Log Analytics peut être utilisé dans plusieurs cas d’usage. Si vous souhaitez aussi supprimer l’espace de travail Log Analytics, vous devez le faire manuellement.

1. Accédez à l’espace de travail Log Analytics associé au projet.
     1. Si vous n’avez pas encore supprimé le projet de migration, le lien vers l’espace de travail se trouve dans la page Vue d’ensemble de Server Assessment, à la section Essentials (Fondamentaux).

     ![Espace de travail Log Analytics](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Si vous avez déjà supprimé le projet de migration, sélectionnez **Groupes de ressources** dans le volet gauche du portail Azure. Accédez au groupe de ressources où l’espace de travail a été créé, puis accédez à celui-ci.
2. Suivez les instructions [de cet article](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) pour supprimer l’espace de travail.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Échec de la création du projet de migration avec l’erreur *Les demandes doivent comporter des en-têtes d’identité de l’utilisateur*

Ce problème peut se produire pour les utilisateurs qui n’ont pas accès au locataire (tenant) Azure Active Directory (Azure AD) de l’organisation. Lorsqu’un utilisateur est ajouté pour la première fois à un locataire Azure AD, il reçoit une invitation à rejoindre le locataire par e-mail. Il doit ouvrir le message et accepter l’invitation pour être correctement ajouté au locataire. Si vous ne trouvez pas l’e-mail, demandez à un utilisateur qui a déjà accès au locataire de vous renvoyer l’invitation en suivant les étapes décrites [ici](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Dès que vous avez reçu l’e-mail d’invitation, ouvrez-le et cliquez sur le lien à l’intérieur pour accepter l’invitation. Après cela, vous devez vous déconnecter du portail Azure et vous reconnecter ; actualiser le navigateur ne fonctionnera pas. Vous pourrez ensuite essayer de créer le projet de migration.

## <a name="appliance-issues"></a>Problèmes liés aux appliances

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Échec du déploiement de l’appliance Azure Migrate pour VMware avec l’erreur : Le fichier manifeste indiqué n'est pas valide : entrée de manifeste OVF non valide.

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. Reportez-vous à cet [article](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) pour vérifier la valeur de hachage. Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si vous faites à nouveau face à un échec et que vous utilisez un client VMware vSphere pour le déploiement du fichier OVF, essayez plutôt d’utiliser le client Web vSphere. Si le problème persiste, essayez d’utiliser un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi en suivant les étapes ci-dessous :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) en utilisant le client web (https://<*adresse IP de l’hôte*>/ui).
   - Accédez à **Accueil** > **Inventaire**.
   - Cliquez sur **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
4. Si le déploiement échoue toujours, contactez le support Azure Migrate.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>L’appliance ne peut pas se connecter à Internet

Cela peut se produire lorsque l’ordinateur que vous utilisez est derrière un proxy. Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
Si vous utilisez un proxy de pare-feu basé sur une URL pour contrôler la connectivité sortante, veillez à ajouter ces URL nécessaires à la liste verte :

Scénario | Liste d’URL
--- | ---
Server Assessment pour VMware | [Ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Server Assessment pour Hyper-V | [Ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Server Migration pour VMware (sans agent) | [Ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Server Migration pour VMware (basé sur un agent) | [Ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Server Migration pour Hyper-V | [Ici](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Si vous utilisez un proxy d’interception pour vous connecter à Internet, vous devez importer le certificat de proxy sur la machine virtuelle de l’appliance. Pour importer le certificat de proxy, suivez les étapes détaillées [ici](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Erreur 802 : erreur de synchronisation de la date et de l’heure

L'horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l'heure actuelle. Modifiez l'heure de l'horloge sur la machine virtuelle du collecteur afin qu'elle corresponde à l'heure actuelle, en procédant comme suit :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
2. Pour vérifier le fuseau horaire, exécutez w32tm /tz.
3. Pour synchroniser l'heure, exécutez w32tm /resync.


### <a name="error-unabletoconnecttoserver"></a>Erreur UnableToConnectToServer

Impossible de se connecter à vCenter Server « ServerName.com: 9443 » en raison de l’erreur : Il n’existait pas de point de terminaison à l’écoute sur https://Servername.com:9443/sdk pouvant accepter le message.

Vérifiez que vous exécutez la dernière version de l’appliance du collecteur ; si ce n’est pas le cas, [mettez-la à niveau](https://docs.microsoft.com/azure/migrate/concepts-collector).

Si le problème se produit toujours dans la dernière version, cela peut être dû au fait que l’ordinateur collecteur ne parvient pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué est incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tentera de se connecter au port numéro 443.

1. Essayez d’effectuer un test ping sur Servername.com à partir de l’ordinateur collecteur.
2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter sur l’adresse IP.
3. Identifiez le numéro de port correct pour se connecter au serveur vCenter.
4. Enfin, vérifiez si le serveur vCenter est en cours d’exécution.


### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Il n’a pas été possible d’inscrire l’appliance au projet Azure Migrate (ID d’erreur : 60052)

Cette erreur est due à une insuffisance d’autorisations sur le compte Azure utilisé pour inscrire l’appliance. Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’un accès « Contributeur » à l’abonnement. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les rôles et autorisations Azure requis.

### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Il n’a pas été possible d’inscrire l’appliance au projet Azure Migrate (ID d’erreur : 60039)

Le projet de Azure Migrate que vous avez sélectionné pour inscrire l’appliance est introuvable, ce qui entraîne l’échec de l’inscription. Accédez au portail Azure et vérifiez si le projet existe dans votre groupe de ressources. Si le projet n’existe pas, créez un projet Azure Migrate dans votre groupe de ressources et réinscrivez l’appliance. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) plus sur la création d’un projet Azure Migrate.

### <a name="azure-key-vault-management-operation-failed-error-id-60030-60031"></a>L’opération de gestion d’Azure Key Vault a échoué (ID d’erreur : 60030, 60031)

Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’un accès « Contributeur » à l’abonnement. Vérifiez également si le compte a accès au Key Vault spécifié dans le message d’erreur, puis retentez l’opération. Si le problème persiste, contactez le support technique Microsoft. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les rôles et autorisations Azure requis.

### <a name="discovery-could-not-be-initiated-due-to-the-error-the-operation-failed-for-the-given-list-of-hosts-or-clusters-error-id-60028"></a>Il n’a pas été possible de lancer la découverte en raison de l’erreur. L’opération a échoué pour la liste donnée d’hôtes ou de clusters (ID d’erreur : 60028)

Il n’a pas été possible de lancer la découverte sur les hôtes répertoriés dans l’erreur en raison d’un problème d’accès aux informations sur les machines virtuelles ou de récupération de celles-ci. Les autres hôtes ont été correctement ajoutés. Ajoutez de nouveau les hôtes répertoriés dans l’erreur à l’aide de l’option **Ajouter un hôte**. En cas d’erreur de validation, reportez-vous au guide de correction pour résoudre l’erreur, puis réessayez d’**Enregistrer et lancer la découverte**.

### <a name="azure-active-directory-aad-operation-failed-the-error-occurred-while-creating-or-updating-the-aad-application-error-id-60025"></a>L’opération Azure Active Directory (AAD) a échoué. L’erreur s’est produite lors de la création ou de la mise à jour de l’application AAD (ID d’erreur : 60025)

Le compte d’utilisateur Azure utilisé pour inscrire l’appliance n’a pas accès à l’application AAD spécifiée dans le message d’erreur. Vérifiez si vous êtes le propriétaire de l’application AAD. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les permissions d’application AAD.


## <a name="discovery-issues"></a>Problèmes de découverte

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>J’ai démarré la détection, mais je ne vois pas les machines virtuelles découvertes sur le portail Azure. Les vignettes Évaluation de serveur et Migration de serveur affichent l’état « Découverte en cours »
Après le démarrage de la découverte à partir de l’appliance, prévoyez un certain temps pour que les machines détectées apparaissent dans le portail Azure. Il faut environ 15 minutes pour une découverte VMware et environ 2 minutes par hôte ajouté pour une découverte Hyper-V. Si vous continuez à voir « Détection en cours » même au bout de ce délai, cliquez sur **Actualiser** sous l’onglet **Serveurs**. Ceci doit normalement montrer le nombre de serveurs découverts dans les vignettes Server Assessment et Server Migration.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>J’utilise l’appliance qui assure la découverte continue de mon environnement local, mais les machines virtuelles supprimées de mon environnement local apparaissent toujours dans le portail.

Il faut jusqu’à 30 minutes pour que les données de découverte collectées par l’appliance soient reflétées dans le portail. Si vous ne voyez pas les informations à jour même après 30 minutes, lancez une actualisation des données en utilisant les étapes suivantes :

1. Dans Serveurs > Azure Migrate : Server Assessment, cliquez sur **Vue d’ensemble**.
2. Sous **Gérer**, cliquez**Intégrité de l’agent**
3. Cliquez sur l’option pour **Actualiser l’agent**. Vous voyez cette option ci-dessous, sous la liste des agents.
4. Attendez que l’opération d’actualisation se termine. Vérifiez que vous pouvez voir des informations à jour sur vos machines virtuelles.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Je ne vois pas les informations à jour sur les machines virtuelles locales dans le portail

Il faut jusqu’à 30 minutes pour que les données de découverte collectées par l’appliance soient reflétées dans le portail. Si vous ne voyez pas les informations à jour même après 30 minutes, lancez une actualisation des données en utilisant les étapes suivantes :

1. Dans Serveurs > Azure Migrate : Server Assessment, cliquez sur **Vue d’ensemble**.
2. Sous **Gérer**, cliquez**Intégrité de l’agent**
3. Cliquez sur l’option pour **Actualiser l’agent**. Vous voyez cette option sous la liste des agents.
4. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour sur vos machines virtuelles.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Impossible de se connecter aux hôtes ou au cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 (ID d’erreur : 50004)
Cette erreur se produit si le DNS servant l’appliance ne peut pas résoudre le nom du cluster ou de l’hôte que vous avez fourni. Si vous voyez cette erreur sur le cluster, essayez en fournissant le nom de domaine complet du cluster.

Vous pouvez aussi voir cette erreur pour les hôtes d’un cluster. Dans ce cas, l’appliance a pu se connecter au cluster. Le cluster a cependant retourné les noms d’hôte qui ne sont pas des noms de domaine complets.

Pour résoudre cette erreur, mettez à jour le fichier hosts sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes.
1. Ouvrez le Bloc-notes en tant qu’administrateur. Ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts.
2. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
3. Enregistrez et fermez le fichier hosts.
4. Vous pouvez vérifier si l’appliance peut se connecter aux hôtes en utilisant l’application de gestion de l’appliance. Au bout de 30 minutes, vous devez normalement voir les informations à jour sur ces hôtes dans le portail Azure.


## <a name="assessment-issues"></a>Problèmes d’évaluation

### <a name="azure-readiness-issues"></a>Problèmes liés à la préparation pour Azure

Problème | Correction
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Nous vous recommandons de convertir le type de démarrage en BIOS avant d’exécuter une migration. <br/><br/>Vous pouvez utiliser Azure Migrate Server Migration pour effectuer la migration de ces machines virtuelles, car il convertit leur type de démarrage en BIOS pendant la migration.
Systèmes d’exploitation Windows pris en charge sous condition | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement (CSA) pour bénéficier d’une [prise en charge dans Azure](https://aka.ms/WSosstatement). Envisagez de mettre à niveau le système d’exploitation avant la migration vers Azure.
Systèmes d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](https://aka.ms/WSosstatement). Envisagez de mettre à niveau le système d’exploitation de la machine avant la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau le système d’exploitation de la machine avant la migration vers Azure.
Systèmes d’exploitation Linux non approuvés | La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est assurée par Azure. Envisagez de mettre à niveau le système d’exploitation vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation spécifié de la machine virtuelle est « Autre » dans vCenter Server, ce qui explique pourquoi Azure Migrate ne peut pas déterminer si la machine virtuelle est prête pour Azure. Vérifiez que le système d’exploitation s’exécutant dans la machine est [pris en charge](https://aka.ms/azureoslist) par Azure avant de procéder à sa migration.
Nombre de bits du système d’exploitation non pris en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent démarrer dans Azure, mais il est recommandé de mettre à niveau le système d’exploitation de la machine virtuelle de 32 bits à 64 bits avant la migration vers Azure.
Nécessite un abonnement Visual Studio. | La machine exécute un système d’exploitation client Windows qui est pris en charge seulement dans un abonnement Visual Studio.
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

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Je ne parviens pas à spécifier Contrat Entreprise (EA) en tant qu’offre Azure dans les propriétés d’évaluation
Azure Migrate : Server Assessment ne prend actuellement pas en charge les tarifs Contrat Entreprise (EA). La solution de contournement consiste à utiliser « Paiement à l’utilisation » comme offre Azure et la propriété « Remise » pour spécifier les remises personnalisées que vous recevez. [Découvrez plus d’informations sur la façon dont vous pouvez personnaliser une évaluation](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Pourquoi Server Assessment marque-t-il mes machines virtuelles Linux comme étant « Prêtes sous condition » ? Y a-t-il quelque chose que je puisse faire pour résoudre ce problème ?
Il existe un problème connu dans Server Assessment qui l’empêche de détecter la version mineure du système d’exploitation Linux installé sur les machines virtuelles locales (par exemple, pour RHEL 6.10, Server Assessment détecte actuellement seulement RHEL 6 comme version du système d’exploitation). Dans la mesure où Azure approuve uniquement certaines versions spécifiques de Linux, les machines virtuelles Linux sont actuellement marquées comme « Prêtes sous condition » dans Server Assessment. Vous pouvez vérifier manuellement que le système d’exploitation Linux exécuté sur la machine virtuelle locale est approuvé dans Azure en consultant la [documentation sur la prise en charge de Linux par Azure](https://aka.ms/migrate/selfhost/azureendorseddistros). Une fois que vous avez vérifié la distribution approuvée, vous pouvez ignorer cet avertissement.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>La référence SKU de machine virtuelle recommandée par Server Assessment a un nombre de cœurs plus élevé et une taille de mémoire supérieure à ce qui est alloué localement. Pourquoi ?
La recommandation en matière de référence SKU de machine virtuelle dans Server Assessment varie en fonction des propriétés d’évaluation. Vous pouvez créer deux types d’évaluations dans Server Assessment : « En fonction des performances » et « Localement ». Pour les évaluations en fonction des performances, Server Assessment prend en compte les données d’utilisation des machines virtuelles locales (processeur, mémoire, utilisation des disques et du réseau) afin de déterminer la référence SKU de machine virtuelle cible appropriée pour vos machines virtuelles locales. De plus, pour le dimensionnement « En fonction des performances », le facteur de confort est pris en compte pour identifier l’utilisation effective. Pour le dimensionnement « Localement », les données de performances ne sont pas prises en compte et une référence SKU cible est recommandée en fonction de ce qui est alloué localement.

Par exemple, supposez qu’il existe une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, avec une utilisation du processeur de 50 % et une utilisation de la mémoire de 50 %, et qu’un facteur de confort de 1,3 est spécifié dans l’évaluation. Si le critère de dimensionnement de l’évaluation est « Localement », une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire est recommandée. En revanche, si le critère de dimensionnement est « En fonction des performances », d’après l’utilisation du processeur et de la mémoire (50 % de 4 cœurs * 1,3 = 2,6 cœurs et 50 % de 8 Go de mémoire * 1,3 = 5,3 Go mémoire), la référence SKU de machine virtuelle la plus économique de quatre cœurs (nombre de cœurs pris en charge le plus proche) et de 8 Go de mémoire (taille de mémoire prise en charge la plus proche) est recommandée. [Découvrez plus d’informations sur la façon dont Server Assessment effectue le dimensionnement](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>La référence SKU de disque recommandée par Server Assessment a une taille supérieure à ce qui est alloué localement. Pourquoi ?
Le dimensionnement de disque dans Server Assessment dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage. Si le critère de dimensionnement est « En fonction des performances » et que le type de stockage est défini sur « Automatique », les valeurs de débit et d’IOPS du disque sont prises en compte pour identifier le type de disque cible (HDD Standard, SSD Standard ou Premium). Une référence SKU de disque parmi celles du type approprié est ensuite recommandée conformément aux exigences de taille du disque local. Si le critère de dimensionnement est « En fonction des performances » et que le type de stockage est « Premium », une référence SKU de disque Premium dans Azure est recommandée conformément aux exigences en matière de débit, d’IOPS et de taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est « Localement » et que le type de stockage est « HDD Standard », « SSD Standard » ou « Premium ».

Par exemple, si vous avez un disque local avec 32 Go de mémoire, mais que la valeur d’IOPS en lecture et écriture agrégée pour le disque est de 800 IOPS, Server Assessment recommandera un type de disque Premium (en raison des exigences d’IOPS plus élevées), puis recommandera une référence SKU de disque capable de prendre en charge la taille et les IOPS requises. La correspondance la plus proche dans cet exemple serait P15 (256 Go, 1100 IOPS). Par conséquent, même si la taille requise par le disque locale était de 32 Go, Server Assessment a recommandé un disque avec une plus grande taille en raison de l’exigence élevée du disque local en matière d’IOPS.

### <a name="why-does-my-assessment-report-say-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Pourquoi mon rapport d’évaluation indique-t-il « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » pour certaines machines virtuelles ?
Les problèmes ci-dessus sont répertoriés lorsque l’appliance Azure Migrate ne peut pas collecter les données de performances des machines virtuelles locales. Cela peut se produire si les machines virtuelles étaient hors tension durant la période pour laquelle vous créez l’évaluation (dernier jour/dernière semaine/dernière mois), car l’appliance ne peut pas collecter les données de performances d’une machine virtuelle hors tension. Si seuls les compteurs mémoire manquent et que vous essayez d’évaluer des machines virtuelles Hyper-V, vérifiez si vous avez activé la mémoire dynamique sur ces machines virtuelles. Il existe actuellement un problème connu qui a pour effet que l’appliance Azure Migrate ne peut pas collecter les données d’utilisation de la mémoire des machines virtuelles sur lesquelles la mémoire dynamique n’est pas activée. Notez que ce problème affecte uniquement les machines virtuelles Hyper-V, pas les machines virtuelles VMware. Si l’un des compteurs de performances est manquant, Azure Migrate : L’évaluation du serveur revient aux cœurs et à la mémoire alloués, et recommande une taille de machine virtuelle appropriée.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Le coût de la licence du système d’exploitation de la machine virtuelle est-il inclus dans le coût de calcul estimé par Server Assessment ?
Actuellement, Server Assessment ne prend en compte que le coût de la licence du système d’exploitation pour les machines Windows. Le coût de la licence du système d’exploitation pour les machines Linux n’est pas encore pris en compte.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Quel est l’impact de l’utilisation de l’historique des performances et des centiles sur les suggestions de taille ?
Ces propriétés sont applicables seulement pour le dimensionnement « En fonction des performances ». Server Assessment recueille les données de performances des machines locales de manière continue, et les utilise pour recommander la référence SKU de machine virtuelle et la référence SKU de disque dans Azure. Voici comment les données de performances sont recueillies par Server Assessment :
- L’appliance Azure Migrate profile en continu l’environnement local afin de recueillir des données d’utilisation en temps réel toutes les 20 secondes pour les machines virtuelles VMware et toutes les 30 secondes pour les machines virtuelles Hyper-V.
- L’appliance cumule les échantillons de 20/30 secondes afin de créer un point de données unique toutes les 10 minutes. Pour créer le point de données unique, l’appliance sélectionne la valeur maximale de tous les échantillons de 20/30 secondes, puis l’envoie à Azure.
- Quand vous créez une évaluation dans Server Assessment, la valeur d’utilisation représentative est identifiée en fonction de la durée de l’historique des performances et de la valeur de centile d’utilisation. Par exemple, si l’historique des performances est d’une semaine que le centile d’utilisation est 95e, Azure Migrate trie tous les points d’échantillonnage de 10 minutes pour la dernière semaine dans l’ordre croissant, puis sélectionne le 95e centile comme valeur représentative.
Le 95e centile permet de garantir que vous ignorez les aberrations, celles-ci pouvant être incluses si vous choisissez le 99e centile. Si vous souhaitez choisir l’utilisation maximale de la période et que vous ne souhaitez pas manquer les aberrations, vous devez sélectionner le 99e centile comme utilisation en centile.

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
Azure Migrate vous permet de visualiser des dépendances pour une durée maximale d’une heure. Même si Azure Migrate vous permet de revenir en arrière dans l’historique jusqu’à une date spécifique sur une période d’un mois, la durée maximale pour laquelle vous pouvez visualiser des dépendances est 1 heure. Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement pour une fenêtre d’une heure. Vous pouvez aussi utiliser les journaux d’activité Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une plus longue durée.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Je n’arrive pas à visualiser les dépendances de groupes ayant plus de 10 machines virtuelles ?
Vous pouvez [visualiser les dépendances de groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ayant 10 machines virtuelles maximum. Si vous avez ce type de groupe, nous vous recommandons de diviser le groupe en groupes plus petits pour visualiser les dépendances.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>J’ai installé des agents et utilisé la visualisation des dépendances pour créer des groupes. Maintenant, après le basculement, les machines affichent l’action « Installer l’agent » au lieu de « Afficher les dépendances ».
* Après un basculement planifié ou non planifié, les machines sur site sont désactivées et les machines équivalentes sont lancées dans Azure. Ces machines acquièrent une adresse MAC différente. Elles peuvent acquérir une adresse IP différente selon que l'utilisateur a choisi de conserver ou non l'adresse IP sur site. Si les adresses MAC et IP diffèrent, Azure Migrate n'associe pas les machines sur site avec les données de dépendance Service Map et demande à l'utilisateur d'installer des agents au lieu d'afficher les dépendances.
* Après un basculement de test, les machines sur site restent activées comme prévu. Les machines équivalentes lancées dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que l’utilisateur ne bloque le trafic des journaux Azure Monitor provenant de ces machines, Azure Migrate n’associe pas les machines locales aux données de dépendance Service Map et demande à l’utilisateur d’installer des agents au lieu d’afficher les dépendances.

## <a name="collect-azure-portal-logs"></a>Collecter les journaux du portail Azure

**Comment collecter les journaux d’activité du trafic réseau du portail Azure ?**

1. Ouvrez le navigateur et connectez-vous [au portail](https://portal.azure.com).
2. Appuyez sur F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
3. Cliquez sur l'onglet **Réseau**, puis commencez la capture du trafic réseau :
   - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si ce n’est pas le cas, cliquez sur le cercle noir pour commencer.
   - Dans Microsoft Edge/Internet Explorer, l'enregistrement devrait commencer automatiquement. Si ce n'est pas le cas, cliquez sur le bouton de lecture vert.
4. Essayez de reproduire l'erreur.
5. Après avoir rencontré l'erreur pendant l'enregistrement, arrêtez l'enregistrement et enregistrez une copie de l'activité enregistrée :
   - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette opération compresse et exporte les journaux d’activité sous la forme d’un fichier .har.
   - Dans Microsoft Edge/Internet Explorer, cliquez sur l'icône **Exporter le trafic capturé**. Ceci compresse et exporte le journal.
6. Accédez à l'onglet **Console** pour vérifier la présence d'avertissements ou d'erreurs. Pour enregistrer le journal de la console :
   - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
   - Dans Microsoft Edge/Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**.
7. Fermez les outils de développement.
