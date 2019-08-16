---
title: Résolution de problèmes Azure Migrate | Microsoft Docs
description: Présente les problèmes connus du service Azure Migrate, ainsi que des conseils de dépannage pour les erreurs courantes.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: fa1e7fcf89ccc06e429831191ba5dfce3cf33797
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828312"
---
# <a name="troubleshoot-azure-migrate"></a>Résoudre les problèmes d’Azure Migrate

[Azure Migrate](migrate-services-overview.md) fournit un hub d’outils pour l’évaluation et la migration, ainsi que des offres de fournisseurs de logiciels indépendants tiers. Cet article vous aide à résoudre les problèmes liés à l’utilisation d’Azure Migrate, d’Azure Migrate Server Assessment et d’Azure Migrate Server Migration.

## <a name="azure-migrate-project-issues"></a>Problèmes des projets Azure Migrate

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Je ne parviens pas à trouver mon projet Azure Migrate existant.

Il existe [deux versions](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate. Utilisez l’une des méthodes suivantes, selon la version que vous avez choisie pour créer le projet :

* Si vous recherchez un projet créé avec la version précédente (ancienne expérience) d’Azure Migrate, suivez les étapes ci-dessous pour rechercher le projet :

    1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
    2. Dans le tableau de bord Azure Migrate, vous verrez une bannière donnant des informations sur l’accès aux anciens projets. Vous ne verrez cette bannière que si vous avez créé un projet avec l’ancienne expérience. Sélectionnez la bannière.

       ![Accéder aux projets existants](./media/troubleshooting-general/access-existing-projects.png)

    3. Vous voyez maintenant la liste des projets existants créés avec la version précédente d’Azure Migrate.

* Si vous recherchez un projet créé avec la version actuelle (nouvelle expérience), suivez les étapes ci-dessous pour rechercher le projet :

    1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
    2. Dans le tableau de bord Azure Migrate, accédez à la page **Serveurs** dans le volet gauche, puis sélectionnez **Modifier** dans le coin supérieur droit.

       ![Passer à un projet Azure Migrate existant](./media/troubleshooting-general/switch-project.png)

    3. Sélectionnez l’abonnement et le projet Azure Migrate de votre choix.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Comment créer un deuxième projet Azure Migrate ?

Suivez les étapes ci-dessous pour créer un projet Azure Migrate :

1. Accédez au [portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate, accédez à la page **Serveurs** dans le volet gauche, puis sélectionnez **Modifier** dans le coin supérieur droit.

   ![Modifier un projet Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Pour créer un nouveau projet, sélectionnez **cliquez ici**.

   ![Créer un deuxième projet Azure Migrate](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Quelles sont les zones géographiques Azure qui sont prises en charge par Azure Migrate ?

Consultez les listes pour [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) et [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Comment supprimer des projets Azure Migrate et les espaces de travail Log Analytics qui leur sont associés ?

Quand vous supprimez un projet Azure Migrate, le projet de migration *et* les métadonnées concernant les machines découvertes sont supprimés. Cependant, si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment, l’espace n’est pas supprimé automatiquement (le même espace de travail Log Analytics peut être utilisé dans plusieurs cas d’usage). Si vous souhaitez également supprimer l’espace de travail Log Analytics, vous devez le faire manuellement :

1. Accédez à l’espace de travail Log Analytics associé au projet.
     * Si vous n’avez pas encore supprimé le projet de migration, vous trouverez le lien vers l’espace de travail dans la vue d’ensemble de Server Assessment, sous la section Essentials (Fondamentaux).

       ![Espace de travail Log Analytics](./media/troubleshooting-general/loganalytics-workspace.png)

     * Si vous avez déjà supprimé le projet de migration, sélectionnez **Groupes de ressources** dans le volet gauche du portail Azure. Accédez au groupe de ressources dans lequel l’espace de travail a été créé, puis accédez à celui-ci.
2. Suivez les instructions fournies dans [Supprimer un espace de travail Azure Log Analytics avec le portail Azure](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Échec de la création du projet de migration avec l’erreur « Les demandes doivent contenir des en-têtes d’identité de l’utilisateur »

Ce problème peut se produire si les utilisateurs n’ont pas accès au locataire Azure Active Directory (Azure AD) de l’organisation. Lorsqu’un utilisateur est ajouté pour la première fois à un locataire Azure AD, il reçoit une invitation à rejoindre le locataire par e-mail. Il doit accepter l’invitation pour être correctement ajouté au locataire. Si vous ne trouvez pas cet e-mail, demandez à un utilisateur qui a déjà accès au locataire de vous renvoyer l’invitation en suivant les étapes décrites dans [Renvoyer des invitations aux utilisateurs invités](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Dès que vous avez reçu l’e-mail d’invitation, ouvrez-le et sélectionnez le lien pour accepter l’invitation. Ensuite, vous devez vous déconnecter du portail Azure et vous reconnecter (l’actualisation du navigateur ne fonctionnera pas). Vous pourrez ensuite créer le projet de migration.

## <a name="appliance-issues"></a>Problèmes liés aux appliances

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>Échec du déploiement d’une appliance Azure Migrate pour VMware avec le message d’erreur : « Le fichier manifeste fourni n’est pas valide : entrée de manifeste OVF non valide ».

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. Pour obtenir des instructions, consultez [Préparer l’évaluation de machines virtuelles VMware et leur migration vers Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si le déploiement échoue une nouvelle fois et si vous utilisez un client VMware vSphere pour déployer le fichier OVF, essayez plutôt de le déployer à l’aide du client web vSphere. Si le déploiement échoue de nouveau, essayez un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi en effectuant les étapes suivantes :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) en utilisant le client web (https://<*adresse IP de l’hôte*>/ui).
   - Accédez à **Accueil** > **Inventaire**.
   - Sélectionnez **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
4. Si le déploiement échoue encore, contactez le support Azure Migrate.

### <a name="the-appliance-cant-connect-to-the-internet"></a>L’appliance ne peut pas se connecter à Internet.

Cela peut se produire lorsque l’ordinateur que vous utilisez est situé derrière un proxy. Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
Si vous utilisez un proxy de pare-feu basé sur une URL pour contrôler la connectivité sortante, veillez à ajouter les URL suivantes à la liste verte :

- [Server Assessment pour VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Server Assessment pour Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Server Migration pour VMware (sans agent)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Server Migration pour VMware (basé sur un agent)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Server Migration pour Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Si vous utilisez un proxy d’interception pour vous connecter à Internet, vous devez importer le certificat de proxy sur la machine virtuelle de l’appliance. Vous pouvez importer le certificat de proxy en suivant les étapes décrites dans [Appliance Azure Migrate](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Erreur 802 : erreur de synchronisation de la date et de l’heure.

L’horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l’heure actuelle. Modifiez l'heure de l'horloge sur la machine virtuelle du collecteur afin qu'elle corresponde à l'heure actuelle, en procédant comme suit :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
2. Pour vérifier le fuseau horaire, exécutez **w32tm /tz**.
3. Pour synchroniser l’heure, exécutez **w32tm /resync**.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>Échec de la connexion. Error: UnableToConnectToServer.

Il est possible que vous ne puissiez pas vous connecter à vCenter Server *Nom_Serveur*.com:9443. Les détails de l’erreur indiquent qu’aucun des points de terminaison qui écoutent https://*nom_serveur*.com:9443/sdk n’a pu accepter le message.

Dans ce cas, vérifiez si vous exécutez la dernière version de l’appliance collecteur. Si ce n’est pas le cas, mettez-la à niveau vers la [dernière version](https://docs.microsoft.com/azure/migrate/concepts-collector).

Si le problème se produit encore avec la dernière version, il est possible que l’ordinateur collecteur ne parvienne pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué est incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tente de se connecter au port numéro 443.

1. Effectuez un test ping sur *Nom_Serveur*.com à partir de l’ordinateur collecteur.
2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter à l’aide de l’adresse IP.
3. Identifiez le numéro de port correct pour se connecter au serveur vCenter.
4. Vérifiez que le serveur vCenter est opérationnel.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>Il est possible que l’appliance du projet Azure Migrate ne soit pas correctement inscrite (ID d’erreur : 60052).

Cette erreur se produit lorsque le compte Azure utilisé pour inscrire l’appliance ne dispose pas d’autorisations suffisantes. Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les rôles et autorisations Azure requis.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>Il est possible que l’appliance du projet Azure Migrate ne soit pas correctement inscrite (ID d’erreur : 60039).

Le projet Azure Migrate sélectionné pour inscrire l’appliance est peut-être introuvable. Dans ce cas, l’inscription échoue. Accédez au portail Azure et vérifiez si le projet se trouve dans votre groupe de ressources. Si le projet n’existe pas, créez un projet Azure Migrate dans votre groupe de ressources et réinscrivez l’appliance. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) plus sur la création d’un projet Azure Migrate.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>L’opération de gestion d’Azure Key Vault a échoué (ID d’erreur : 60030, 60031).

Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement. Vérifiez également que le compte a accès au coffre de clés spécifié dans le message d’erreur, puis retentez l’opération. Si le problème persiste, contactez le support technique Microsoft. [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les rôles et autorisations Azure requis.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Impossible de démarrer la découverte en raison d’une erreur. L’opération a échoué pour la liste d’hôtes ou de clusters spécifiée (ID d’erreur : 60028).

Impossible de démarrer la découverte sur les hôtes listés dans l’erreur en raison d’un problème d’accès ou de récupération des informations de machine virtuelle. Les autres hôtes ont bien été ajoutés. Ajoutez de nouveau les hôtes listés dans l’erreur à l’aide de l’option **Ajouter un hôte**. En cas d’erreur de validation, reportez-vous au guide de correction pour résoudre l’erreur, puis réessayez l’option **Enregistrer et lancer la découverte**.

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Une opération Azure AD a échoué. L’erreur s’est produite lors de la création ou de la mise à jour de l’application Azure AD (ID d’erreur : 60025).

Cette erreur se produit quand le compte d’utilisateur Azure utilisé pour lancer la découverte est différent du compte utilisé pour inscrire l’appliance. Vous pouvez effectuer l’une des actions suivantes :
1. Vérifiez que le compte d’utilisateur qui lance la découverte est identique à celui utilisé pour inscrire l’appliance.
1. Accordez des autorisations d’accès à l’application AAD à l’autre compte d’utilisateur pour lequel l’opération de découverte échoue.
1. Supprimez le groupe de ressources créé précédemment pour le projet Azure Migrate et créez un autre groupe de ressources afin de recommencer.

[Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les permissions d’application AAD.

## <a name="discovery-issues"></a>Problèmes de découverte

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>J’ai démarré la découverte, mais je ne vois pas les machines virtuelles découvertes sur le portail Azure. Les vignettes **Server Assessment** et **Server Migration** affichent l’état « Découverte en cours ».
Après le démarrage de la découverte à partir de l’appliance, prévoyez un certain temps pour que les machines découvertes apparaissent dans le portail Azure. Il faut environ 15 minutes pour une découverte VMware et environ 2 minutes par hôte ajouté pour une découverte Hyper-V. Si vous continuez à voir « Découverte en cours » après ce délai, sélectionnez **Actualiser** sous l’onglet **Serveurs**. Ceci doit normalement montrer le nombre de serveurs découverts dans les vignettes Server Assessment et Server Migration.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>J’utilise l’appliance qui assure la découverte continue de mon environnement local, mais les machines virtuelles supprimées de mon environnement local apparaissent toujours dans le portail.

Il faut jusqu’à 30 minutes pour que les données de découverte collectées par l’appliance soient affichées dans le portail. Au bout de 30 minutes, si les informations n’ont pas été mises à jour , lancez une actualisation des données en effectuant les étapes suivantes :

1. Accédez à **Serveurs** > **Azure Migrate Server Assessment**, puis sélectionnez **Vue d’ensemble**.
2. Sous **Gérer**, sélectionnez **Agent Health**.
3. Sélectionnez **Actualiser l’agent**. Vous verrez cette option sous la liste des agents.
4. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour sur vos machines virtuelles.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Je ne vois pas les informations les plus récentes sur les machines virtuelles locales dans le portail.

Il faut jusqu’à 30 minutes pour que les données de découverte collectées par l’appliance soient affichées dans le portail. Au bout de 30 minutes, si les informations n’ont pas été mises à jour , lancez une actualisation des données en effectuant les étapes suivantes :

1. Accédez à **Serveurs** > **Azure Migrate Server Assessment**, puis sélectionnez **Vue d’ensemble**.
2. Sous **Gérer**, sélectionnez **Agent Health**.
3. Sélectionnez **Actualiser l’agent**. Vous verrez cette option sous la liste des agents.
4. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour sur vos machines virtuelles.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Impossible de se connecter à un hôte ou à un cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 (ID d’erreur : 50004).
Cette erreur se produit lorsque le service Azure DNS de l’appliance ne peut pas résoudre le nom de cluster ou d’hôte que vous avez fourni. Si vous voyez cette erreur sur le cluster, essayez de fournir le nom de domaine complet (FQDN) du cluster.

Vous pouvez également voir cette erreur pour les hôtes d’un cluster. Dans ce cas, l’appliance peut se connecter au cluster. Cependant, le cluster retourne des noms d’hôtes qui ne sont pas des noms de domaines complets.

Pour résoudre cette erreur, mettez à jour le fichier hôtes sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes :
1. Ouvrez le Bloc-notes en tant qu’administrateur. Ensuite, ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts file.
2. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
3. Enregistrez et fermez le fichier hôtes.
4. Vous pouvez vérifier si l’appliance peut se connecter aux hôtes en utilisant l’application de gestion de l’appliance. Au bout de 30 minutes, vous devez normalement voir les informations à jour sur ces hôtes dans le portail Azure.


## <a name="assessment-issues"></a>Problèmes d’évaluation

### <a name="azure-readiness-issues"></a>Problèmes liés à la préparation pour Azure

Problème | Correction
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Nous vous recommandons de convertir le type de démarrage en BIOS avant d’effectuer une migration. <br/><br/>Vous pouvez utiliser Azure Migrate Server Migration pour gérer la migration de ces machines virtuelles. Celui-ci permet de convertir le type de démarrage de la machine virtuelle en BIOS au cours de la migration.
Systèmes d’exploitation Windows pris en charge sous condition | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement (CSA) pour bénéficier d’une [prise en charge dans Azure](https://aka.ms/WSosstatement). Envisagez de mettre à niveau le système d’exploitation avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](https://aka.ms/WSosstatement). Envisagez de mettre à niveau le système d’exploitation de la machine avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau le système d’exploitation de la machine avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Linux non approuvés | La machine peut démarrer dans Azure, mais Azure ne fournit aucune prise en charge du système d’exploitation. Envisagez de mettre à niveau le système d’exploitation vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant d’effectuer la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation de la machine virtuelle a été spécifié comme étant du type « Autre » dans vCenter Server. Ce comportement empêche Azure Migrate de vérifier la préparation de la machine virtuelle à Azure. Vérifiez que le système d’exploitation de la machine est [pris en charge](https://aka.ms/azureoslist) par Azure avant de procéder à la migration.
Nombre de bits du système d’exploitation non pris en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent être démarrées dans Azure. Toutefois, il est recommandé d’effectuer une mise à niveau vers la version 64 bits avant de procéder à la migration vers Azure.
Nécessite un abonnement Microsoft Visual Studio. | La machine exécute un système d’exploitation client Windows qui est pris en charge uniquement dans un abonnement Visual Studio.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage nécessaire | Les performances de stockage (IOPS et débit) nécessaires à la machine dépassent le niveau de prise en charge Azure. Réduisez les besoins de stockage de la machine avant la migration.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau nécessaire | Les performances réseau (entrée/sortie) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les exigences de mise en réseau de la machine.
Nous n’avons pas trouvé de machine virtuelle à l’emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Un ou plusieurs disques ne sont pas adaptés | Un ou plusieurs disques attachés à la machine virtuelle ne répondent pas aux exigences Azure. Azure Migrate : Server Assessment ne prend pas en charge les disques SSD Ultra et évalue les disques en fonction des limites appliquées aux disques managés Premium (32 To).  Pour chaque disque attaché à la machine virtuelle, vérifiez que la taille du disque est inférieure à 64 To (prise en charge par les disques SSD Ultra). Si ce n’est pas le cas, réduisez la taille du disque avant d’effectuer la migration vers Azure, ou utilisez plusieurs disques dans Azure et [associez-les](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) pour obtenir des limites de stockage plus élevées. Vérifiez que les performances (IOPS et débit) nécessaires à chaque disque sont prises en charge par les [disques de la machine virtuelle managée](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) Azure.
Un ou plusieurs adaptateurs réseau ne sont pas adaptés | Supprimez les cartes réseau non utilisées de la machine avant la migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés de la machine avant la migration.
La taille du disque dépasse la limite autorisée | Azure Migrate : Server Assessment ne prend pas en charge les disques SSD Ultra et évalue les disques en fonction des limites appliquées aux disques Premium (32 To). Toutefois, Azure prend en charge les disques d’une taille maximale de 64 To (prise en charge par les disques SSD Ultra). Réduisez la taille des disques pour passer en dessous des 64 To avant la migration, ou utilisez plusieurs disques Azure et [associez-les](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) pour bénéficier de limites de stockage plus élevées.
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l’adéquation du disque en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n’a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs de la machine sur site avant de migrer.
Impossible de déterminer l’adéquation de la machine virtuelle en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’un ou plusieurs disques en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’une ou plusieurs cartes réseau en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Je ne parviens pas à spécifier l’offre Azure Contrat Entreprise (EA) dans les propriétés d’évaluation.
Azure Migrate Server Assessment ne prend pas en charge les tarifs Contrat Entreprise (EA). La solution de contournement consiste à choisir le **Paiement à l’utilisation** comme offre Azure et à utiliser la propriété **Remise** pour spécifier les remises personnalisées que vous recevez. [Découvrez plus d’informations sur la façon dont vous pouvez personnaliser une évaluation](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Pourquoi Server Assessment marque-t-il mes machines virtuelles Linux comme étant « Prêtes sous condition » ?
Il existe un problème connu dans Server Assessment qui l’empêche de détecter la version mineure du système d’exploitation Linux installé sur les machines virtuelles locales (par exemple, pour RHEL 6.10, Server Assessment détecte uniquement RHEL 6 comme version du système d’exploitation). Dans la mesure où Azure approuve uniquement certaines versions de Linux, les machines virtuelles Linux sont actuellement marquées comme « Prêtes sous condition » dans Server Assessment. Vous pouvez déterminer si le système d’exploitation Linux exécuté sur la machine virtuelle locale est approuvé dans Azure en consultant la [documentation sur la prise en charge de Linux par Azure](https://aka.ms/migrate/selfhost/azureendorseddistros). Une fois que vous avez vérifié la distribution approuvée, vous pouvez ignorer cet avertissement.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Pourquoi la référence SKU de machine virtuelle recommandée par Server Assessment a-t-elle plus de cœurs et de mémoire que ce qui est alloué localement ?
La recommandation en matière de référence SKU de machine virtuelle dans Server Assessment varie en fonction des propriétés d’évaluation. Vous pouvez créer deux types d’évaluations dans Server Assessment : une évaluation effectuée *En fonction des performances* et une évaluation effectuée *Localement*. Pour les évaluations en fonction des performances, Server Assessment prend en compte les données d’utilisation des machines virtuelles locales (processeur, mémoire, utilisation des disques et du réseau) afin de déterminer la référence SKU de machine virtuelle cible appropriée pour vos machines virtuelles locales. De plus, pour le dimensionnement « En fonction des performances », le facteur de confort est pris en compte pour déterminer l’utilisation effective. Pour le dimensionnement « Localement », les données de performances ne sont pas prises en compte et une référence SKU cible est recommandée en fonction de ce qui est alloué localement.

Par exemple, supposez qu’il existe une machine virtuelle locale avec 4 cœurs et 8 Go de mémoire, avec une utilisation du processeur de 50 % et une utilisation de la mémoire de 50 %, et qu’un facteur de confort de 1,3 soit spécifié dans l’évaluation. Si le critère de dimensionnement de l’évaluation est défini sur **Localement**, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire.

Toutefois, supposons que les critères de dimensionnement soient définis en fonction des performances. D’après l’utilisation effective de l’UC et de la mémoire (50 % de 4 cœurs * 1,3 = 2,6 cœurs et 50 % de 8 Go de mémoire * 1,3 = 5,3 Go de mémoire), la référence SKU de machine virtuelle la moins coûteuse avec 4 cœurs (nombre de cœurs pris en charge le plus proche) et 8 Go de mémoire (taille de mémoire prise en charge la plus proche) est celle qui est recommandée. [Découvrez plus d’informations sur la façon dont Server Assessment effectue le dimensionnement](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Pourquoi la référence SKU de disque recommandée par Server Assessment a-t-elle une taille supérieure à ce qui est alloué localement ?
Dans Server Assessment, le dimensionnement du disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage. Si le critère de dimensionnement est **En fonction des performances** et que le type de stockage est défini sur **Automatique**, les valeurs de débit et d’IOPS du disque sont prises en compte pour identifier le type de disque cible (HDD Standard, SSD Standard ou Premium). Une référence SKU de disque parmi celles du type approprié est ensuite recommandée conformément aux exigences de taille du disque local. Si le critère de dimensionnement est **En fonction des performances** et si le type de stockage est **Premium**, une référence SKU de disque Premium dans Azure est recommandée conformément aux exigences de débit, d’IOPS et de taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est défini sur **Localement** et que le type de stockage est **HDD Standard**, **SSD Standard** ou **Premium**.

Par exemple, si vous avez un disque local avec 32 Go de mémoire, mais que la valeur d’IOPS en lecture et écriture agrégée pour le disque est de 800 IOPS, Server Assessment recommandera un type de disque Premium (en raison des exigences d’IOPS plus élevées), puis recommandera une référence SKU de disque capable de prendre en charge la taille et les IOPS nécessaires. La correspondance la plus proche dans cet exemple serait P15 (256 Go, 1100 IOPS). Par conséquent, même si la taille nécessaire au disque local était de 32 Go, Server Assessment a recommandé un disque avec une plus grande taille en raison de l’exigence élevée du disque local concernant les IOPS.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Pourquoi mon rapport d’évaluation indique-t-il « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » pour certaines machines virtuelles ?
Ces problèmes sont signalés quand l’appliance Azure Migrate ne peut pas collecter de données de performances pour les machines virtuelles locales. Ce comportement peut être observé si les machines virtuelles sont désactivées pendant la durée couverte par l’évaluation (dernier jour/dernière semaine/dernier mois). L’appliance ne peut pas collecter les données de performances d’une machine virtuelle lorsque celle-ci est désactivée. S’il manque uniquement les compteurs de mémoire et si vous tentez d’évaluer des machines virtuelles Hyper-V, vérifiez si la mémoire dynamique est activée sur ces machines. Il existe un problème connu qui empêche l’appliance Azure Migrate de collecter les données d’utilisation de la mémoire pour les machines virtuelles sur lesquelles la mémoire dynamique n’est pas activée. Ce problème concerne uniquement les machines virtuelles Hyper-V, et non les machines virtuelles VMware. Si l’un des compteurs de performances est manquant, Azure Migrate Server Assessment a recours aux cœurs et à la mémoire alloués, et recommande une taille de machine virtuelle adaptée.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>Le coût de la licence du système d’exploitation de la machine virtuelle est-il inclus dans le coût de calcul estimé par Server Assessment ?
Server Assessment estime le coût de la licence du système d’exploitation pour les ordinateurs Windows, mais pas pour les ordinateurs Linux.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Quel est l’impact de l’utilisation de l’historique des performances et des centiles sur les recommandations de taille ?
Ces propriétés s’appliquent uniquement au dimensionnement basé sur les performances. Server Assessment recueille les données de performances des machines locales de manière continue, et les utilise pour recommander la référence SKU de machine virtuelle et la référence SKU de disque dans Azure. Ces données de performances sont collectées par Server Assessment de la façon suivante :
- L’appliance Azure Migrate profile en continu l’environnement local afin de recueillir des données d’utilisation en temps réel toutes les 20 secondes pour les machines virtuelles VMware et toutes les 30 secondes pour les machines virtuelles Hyper-V.
- L’appliance cumule les échantillons de 20 secondes et de 30 secondes afin de créer un point de données unique toutes les 10 minutes. Pour créer le point de données, l’appliance sélectionne la valeur maximale de tous les échantillons de 20 secondes et de 30 secondes, puis l’envoie à Azure.
- Quand vous créez une évaluation dans Server Assessment, la valeur d’utilisation représentative est identifiée en fonction de la durée de l’historique des performances et de la valeur de centile d’utilisation. Par exemple, si l’historique des performances est d’une semaine et si le centile d’utilisation est le 95e, Azure Migrate trie tous les points d’échantillonnage de 10 minutes pour la dernière semaine dans l’ordre croissant, puis sélectionne le 95e centile comme valeur représentative.
Le 95e centile vous permet d’ignorer les valeurs hors norme qui peuvent être incluses quand vous choisissez le 99e centile. Si vous souhaitez choisir l’utilisation maximale de la période et si vous ne souhaitez pas ignorer les valeurs hors norme, vous devez sélectionner le 99e centile comme utilisation en centile.

## <a name="dependency-visualization-issues"></a>Problèmes de visualisation des dépendances

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Je n’arrive pas à trouver la fonctionnalité de visualisation des dépendances pour les projets Azure Government.

Azure Migrate dépend de Service Map pour la fonctionnalité de visualisation des dépendances. Étant donné que Service Map n’est pas disponible pour Azure Government, cette fonctionnalité de visualisation n’est pas disponible dans Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>J’ai installé MMA (Microsoft Monitoring Agent) et l’agent de dépendances sur mes machines virtuelles locales, mais les dépendances apparaissent maintenant dans le portail Azure Migrate.

Une fois que vous avez installé les agents, Azure Migrate prend généralement 15 à 30 minutes pour afficher les dépendances dans le portail. Si vous attendez depuis plus de 30 minutes, vérifiez que MMA peut communiquer avec l’espace de travail OMS en effectuant les étapes suivantes.

Pour les machines virtuelles Windows :
1. Accédez au panneau de configuration, puis démarrez Microsoft Monitoring Agent.
2. Sous l’onglet **Azure Log Analytics (OMS)** de la boîte de dialogue **Propriétés Microsoft Monitoring Agent**, vérifiez que l’**état** de l’espace de travail s’affiche en vert.
3. Si l’état n’est pas vert, essayez de supprimer l’espace de travail et de le rajouter à MMA.

      ![Boîte de dialogue Microsoft Monitoring Agent](./media/troubleshooting-general/mma-status.png)

Pour les machines virtuelles Linux, vérifiez que tout s’est bien passé pour les commandes d’installation MMA et Dependency Agent.

### <a name="what-operating-systems-does-mma-support"></a>Quels sont les systèmes d’exploitation pris en charge par MMA ?

 [Cliquez ici pour voir la liste des systèmes d’exploitation Windows pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)
et [cliquez ici pour voir la liste des systèmes d’exploitation Linux pris en charge par MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>Quels sont les systèmes d’exploitation pris en charge par Dependency Agent ?

[Cliquez ici pour voir la liste des systèmes d’exploitation Windows pris en charge par Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) et [cliquez ici pour voir la liste des systèmes d’exploitation Linux pris en charge par Dependency Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Je ne peux pas visualiser les dépendances sur une période supérieure à une heure dans Azure Migrate.
Dans Azure Migrate, vous pouvez visualiser les dépendances sur une période d’une heure au maximum. Même si Azure Migrate vous permet de revenir à une date du mois passé, la durée maximale pendant laquelle vous pouvez visualiser les dépendances est fixée à 1 heure.

Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement sur une période d’une heure. Vous pouvez aussi utiliser les journaux d’activité Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une plus longue durée.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Je n’arrive pas à visualiser les dépendances des groupes qui comprennent plus de 10 machines virtuelles.
Vous pouvez [visualiser les dépendances des groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) ayant 10 machines virtuelles maximum. Si vous avez ce type de groupe, nous vous recommandons de diviser le groupe en groupes plus petits pour visualiser les dépendances.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>J’ai installé des agents et utilisé la visualisation des dépendances pour créer des groupes. Après le basculement, les machines affichent l’action « Installer l’agent » au lieu de « Afficher les dépendances ».
* Après un basculement planifié ou non planifié, les machines locales sont désactivées et les machines équivalentes sont lancées dans Azure. Ces machines acquièrent une adresse MAC différente. Elles peuvent acquérir une adresse IP différente selon que l’utilisateur a choisi de conserver ou non l’adresse IP locale.

  Si les adresses MAC et IP diffèrent, Azure Migrate n’associe pas les machines locales aux données de dépendances Service Map, et il demande à l’utilisateur d’installer des agents au lieu d’afficher les dépendances.
* Après un basculement de test, les machines locales restent activées comme prévu. Les machines équivalentes lancées dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que l’utilisateur ne bloque le trafic sortant des journaux Azure Monitor provenant de ces machines, Azure Migrate n’associe pas les machines locales aux données de dépendances Service Map et demande à l’utilisateur d’installer des agents au lieu d’afficher les dépendances.

## <a name="collect-azure-portal-logs"></a>Collecter les journaux du portail Azure

**Comment collecter les journaux d’activité du trafic réseau du portail Azure ?**

1. Ouvrez le navigateur, accédez au [portail](https://portal.azure.com), puis connectez-vous.
2. Appuyez sur F12 pour démarrer les outils de développement. Si nécessaire, désactivez le paramètre **Effacer les entrées lors de la navigation**.
3. Sélectionnez l’onglet **Réseau**, puis commencez la capture du trafic réseau :
   - Dans Chrome, sélectionnez **Conserver le journal**. L'enregistrement devrait commencer automatiquement. Un cercle rouge indique que le trafic est en cours de capture. Si le cercle rouge ne s’affiche pas, sélectionnez le cercle noir pour démarrer la capture.
   - Dans Microsoft Edge et Internet Explorer, l’enregistrement doit commencer automatiquement. Si ce n’est pas le cas, sélectionnez le bouton de lecture vert.
4. Essayez de reproduire l'erreur.
5. Après avoir rencontré l'erreur pendant l'enregistrement, arrêtez l'enregistrement et enregistrez une copie de l'activité enregistrée :
   - Dans Chrome, cliquez avec le bouton droit et sélectionnez **Enregistrer comme HAR avec du contenu**. Cette action compresse et exporte les journaux sous la forme d’un fichier .har.
   - Dans Microsoft Edge ou Internet Explorer, sélectionnez l’option **Exporter le trafic capturé**. Cette action compresse et exporte le journal.
6. Sélectionnez l’onglet **Console** pour vérifier la présence d’avertissements ou d’erreurs. Pour enregistrer le journal de la console :
   - Dans Chrome, cliquez avec le bouton droit n'importe où dans le journal de la console. Sélectionnez **Enregistrer sous** pour exporter et compresser le journal.
   - Dans Microsoft Edge ou Internet Explorer, cliquez avec le bouton droit sur les erreurs, puis sélectionnez **Tout copier**.
7. Fermez les outils de développement.
