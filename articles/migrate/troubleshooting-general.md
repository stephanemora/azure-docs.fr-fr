---
title: Résolution de problèmes Azure Migrate | Microsoft Docs
description: Présente les problèmes connus du service Azure Migrate, ainsi que des conseils de dépannage pour les erreurs courantes.
author: musa-57
ms.manager: abhemraj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: hamusa
ms.openlocfilehash: 96c5190988d79885f3a1335b6fd431e028bba8fc
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384064"
---
# <a name="troubleshoot-azure-migrate"></a>Résoudre les problèmes d’Azure Migrate

[Azure Migrate](migrate-services-overview.md) fournit un hub d’outils pour l’évaluation et la migration, ainsi que des offres de fournisseurs de logiciels indépendants tiers. Cet article vous aide à résoudre les problèmes liés à l’utilisation d’Azure Migrate, d’Azure Migrate Server Assessment et d’Azure Migrate Server Migration.


## <a name="find-a-project"></a>Rechercher un projet

Il existe [deux versions](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) d’Azure Migrate.


Si vous avez créé le projet Azure Migrate dans la version actuelle d’Azure Migrate, procédez comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate > **Serveurs**, sélectionnez **Modifier** dans le coin supérieur droit.

    ![Passer à un projet Azure Migrate existant](./media/troubleshooting-general/switch-project.png)

3. Sélectionnez l’abonnement et le projet Azure Migrate de votre choix.


Si vous avez créé le projet dans la version précédente d’Azure Migrate, procédez comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate, si vous avez créé un projet dans la version précédente, une bannière faisant référence aux projets plus anciens s’affiche. Sélectionnez la bannière.

    ![Accéder aux projets existants](./media/troubleshooting-general/access-existing-projects.png)

3. Passez en revue la liste des anciens projets.


## <a name="create-additional-projects"></a>Créer des projets supplémentaires

Créez un nouveau projet Azure Migrate comme suit :

1. Accédez au [Portail Azure](https://portal.azure.com) et recherchez **Azure Migrate**.
2. Dans le tableau de bord Azure Migrate > **Serveurs**, sélectionnez **Modifier** dans le coin supérieur droit.

   ![Modifier un projet Azure Migrate](./media/troubleshooting-general/switch-project.png)

3. Pour créer un nouveau projet, sélectionnez **cliquez ici**.

   ![Créer un deuxième projet Azure Migrate](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Passer en revue les zones géographiques prises en charge

Passez en revue les zones géographiques prises en charge pour [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) et [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Supprimer des projets/espaces de travail

Lorsque vous supprimez des projets Azure Migrate et des espaces de travail Log Analytics , notez ce qui suit :

- Quand vous supprimez un projet Azure Migrate, le projet *et* les métadonnées concernant les machines découvertes sont supprimés.
- Cependant, si vous avez attaché un espace de travail Log Analytics à l’outil Server Assessment, l’espace de travail n’est pas supprimé automatiquement.
- Le même espace de travail Log Analytics peut être utilisé dans plusieurs scénarios.
- Si vous souhaitez supprimer l’espace de travail Log Analytics, vous devez le faire manuellement.


### <a name="delete-a-project"></a>Supprimer un projet

Pour supprimer un projet dans la version actuelle d’Azure Migrate :

1. Ouvrez le groupe de ressources Azure dans lequel le projet a été créé.
2. Dans la page du groupe de ressources, sélectionnez **Afficher les types masqués**.
3. Sélectionnez le projet de migration que vous souhaitez supprimer. Le type de ressource est Microsoft.Migrate/migrateprojects. Supprimez-le.

Pour supprimer un projet dans l’ancienne version d’Azure Migrate :

1. Ouvrez le groupe de ressources Azure dans lequel le projet a été créé.
2. Sélectionnez le projet de migration que vous souhaitez supprimer. Le type de ressource est Projet de migration. Supprimez-le.


### <a name="delete-a-workspace"></a>Supprimer un espace de travail

Accédez à l’espace de travail Log Analytics associé au projet.
* Si vous n’avez pas supprimé le projet Azure Migrate, vous trouverez le lien vers l’espace de travail dans **Essentials** > **Server Assessment**.
       ![Espace de travail Log Analytics](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Erreur « Les demandes doivent contenir des en-têtes d’identité de l’utilisateur »

Lorsque vous créez un projet, cette erreur peut indiquer que vous n’avez pas accès au locataire Azure Active Directory (Azure AD) de l’organisation.

- Quand vous êtes ajouté à un locataire Azure AD pour la première fois, vous recevez une invitation par e-mail pour rejoindre le locataire.
- Acceptez l’invitation pour être correctement ajouté au locataire.
    - Si vous ne voyez pas l’e-mail, contactez un utilisateur disposant d’un accès au locataire et demandez-lui de vous [renvoyer l’invitation](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).
    - Après avoir reçu l’e-mail d’invitation, ouvrez-le et sélectionnez le lien pour accepter l’invitation. Ensuite, déconnectez-vous du Portail Azure et reconnectez-vous. (l’actualisation du navigateur ne fonctionnera pas). Vous pourrez ensuite créer le projet de migration.


## <a name="error-invalid-ovf-manifest-entry"></a>Erreur « Entrée de manifeste OVF non valide »

Si vous recevez l’erreur « Le fichier manifeste indiqué n’est pas valide : entrée de manifeste OVF non valide », procédez comme suit :

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. [Plus d’informations](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si le déploiement échoue une nouvelle fois et vous utilisez le client VMware vSphere pour déployer le fichier OVF, essayez plutôt de le déployer à l’aide du client web vSphere. Si le déploiement échoue de nouveau, essayez un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) avec le client web (https://<*adresse IP de l’hôte*>/ui).
   - Dans **Accueil** > **Inventaire**, sélectionnez **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
4. Si le déploiement échoue toujours, contactez le support Azure Migrate.

## <a name="appliance-cant-connect-to-the-internet"></a>L’appliance ne peut pas se connecter à Internet

Cela peut se produire si l’ordinateur de l’appliance se trouve derrière un proxy.

- Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
- Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, ajoutez ces URL à une liste verte :

    - [URL pour l’évaluation VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [URL pour l’évaluation Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [URL pour la migration sans agent VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URL pour la migration basée sur l’agent VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [URL pour la migration Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Si vous utilisez un proxy d’interception pour vous connecter à Internet, importez le certificat de proxy sur la machine virtuelle de l’appliance en suivant [ces étapes](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Erreur : synchronisation de date/heure

Une erreur de synchronisation de la date et de l’heure (802) indique que l’horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l’heure actuelle. Modifiez l’heure de l’horloge sur la machine virtuelle du collecteur afin qu’elle corresponde à l’heure actuelle :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
2. Pour vérifier le fuseau horaire, exécutez **w32tm /tz**.
3. Pour synchroniser l’heure, exécutez **w32tm /resync**.


## <a name="error-unabletoconnecttoserver"></a>Error: UnableToConnectToServer

Si cette erreur de connexion s’affiche, il est possible que vous ne puissiez pas vous connecter à vCenter Server *Nom_Serveur*.com:9443. Les détails de l’erreur indiquent qu’aucun des points de terminaison qui écoutent https://*nom_serveur*.com:9443/sdk ne peut accepter le message.

- Vérifiez si vous exécutez la dernière version de l’appliance. Si ce n’est pas le cas, mettez-la à niveau vers la [dernière version](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Si le problème se produit encore avec la dernière version, il est possible que l’appliance ne parvienne pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué soit incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tente de se connecter au port numéro 443.

    1. Effectuez un test ping sur *Nom_Serveur*.com à partir de l’appliance.
    2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter à l’aide de l’adresse IP.
    3. Identifiez le numéro de port correct pour se connecter à vCenter Server.
    4. Vérifiez que vCenter Server est opérationnel.


## <a name="error-appliance-might-not-be-registered"></a>Error: L’appliance n’est peut-être pas inscrite

- L’erreur 60052 « L’appliance n’est peut-être pas inscrite correctement dans le projet Azure Migrate » se produit si le compte Azure utilisé pour inscrire l’appliance ne dispose pas des autorisations suffisantes.
    - Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
    - [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les rôles et autorisations Azure requis.
- L’erreur 60039 « L’appliance n’est peut-être pas inscrite correctement dans le projet Azure Migrate » peut se produire si l’inscription échoue car le projet Azure Migrate utilisé pour inscrire l’appliance est introuvable.
    - Dans le Portail Azure, vérifiez si le projet se trouve dans le groupe de ressources.
    - Si le projet n’existe pas, créez un projet Azure Migrate dans votre groupe de ressources et réinscrivez l’appliance. [Découvrez comment](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) créer un nouveau projet.

## <a name="error-key-vault-management-operation-failed"></a>Error: Échec de l’opération de gestion de Key Vault

Si l’erreur 60030 ou 60031 s’affiche, « Une opération de gestion d’Azure Key Vault a échoué », procédez comme suit :
- Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
- Vérifiez que le compte a accès au coffre de clés spécifié dans le message d’erreur, puis retentez l’opération.
- Si le problème persiste, contactez le support technique Microsoft.
- [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les rôles et autorisations Azure requis.

## <a name="fix-discovery-couldnt-be-initiated"></a>Correctif : Impossible de démarrer la découverte

Erreur 60028 : « Impossible de démarrer la découverte en raison d’une erreur. L’opération a échoué pour la liste spécifiée d’hôtes ou de clusters » indique que la découverte n’a pas pu être démarrée sur les hôtes répertoriés dans l’erreur en raison d’un problème d’accès ou de récupération des informations de la machine virtuelle. Les autres hôtes ont bien été ajoutés.

- Ajoutez de nouveau les hôtes listés dans l’erreur, à l’aide de l’option **Ajouter un hôte**.
- En cas d’erreur de validation, reportez-vous au guide de correction pour résoudre l’erreur, puis réessayez l’option **Enregistrer et lancer la découverte**.

## <a name="fix-azure-ad-operation-failed-60025"></a>Correctif : Une opération Azure AD a échoué (60025)

Erreur 60025 : « Une opération Azure AD a échoué. L’erreur s’est produite lors de la création ou de la mise à jour de l’application Azure AD » se produit lorsque le compte d’utilisateur Azure utilisé pour lancer la découverte est différent de celui utilisé pour inscrire l’appliance. Effectuez l’une des actions suivantes :

- Vérifiez que le compte d’utilisateur qui lance la découverte est identique à celui utilisé pour inscrire l’appliance.
- Accordez des autorisations d’accès à l’application Azure Active Directory au compte d’utilisateur pour lequel l’opération de découverte échoue.
- Supprimez le groupe de ressources créé précédemment pour le projet Azure Migrate. Créez un autre groupe de ressources pour recommencer.
- [En savoir plus](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sur les autorisations d’application Azure Active Directory.


## <a name="discovered-vms-not-in-portal"></a>Machines virtuelles découvertes absentes du portail

Si vous démarrez la découverte afin que **Server Assessment** et **Server Migration** affichent la **détection en cours**, mais ne voyez pas encore les machines virtuelles dans le portail, notez les points suivants :

- Après le démarrage de la découverte à partir de l’appliance, il faut environ 15 minutes pour une découverte de machines virtuelles VMware et environ deux minutes pour chaque hôte ajouté pour la découverte de machines virtuelles Hyper-V.
- Si vous continuez à voir **Découverte en cours** même après ce délai, sélectionnez **Actualiser** sous l’onglet **Serveurs**. Ceci doit normalement montrer le nombre de serveurs découverts dans les vignettes **Server Assessment** et **Server Migration**.


## <a name="deleted-vms-in-the-portal"></a>Machines virtuelles supprimées dans le portail

Si vous avez déployé une appliance qui découvre continuellement votre environnement local, mais que les machines virtuelles supprimées restent affichées dans le portail, notez les points suivants :  

- Il faut jusqu’à 30 minutes pour que les données de découverte collectées par l’appliance soient affichées dans le portail.
- Au bout de 30 minutes, si les informations n’ont pas été mises à jour , lancez une actualisation des données en effectuant les étapes suivantes :

    1. Dans **Serveurs** > **Évaluation de serveur Azure Migrate**, sélectionnez **Vue d’ensemble**.
    2. Sous **Gérer**, sélectionnez **Agent Health**.
    3. Sélectionnez **Actualiser l’agent**.
    1. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="vm-information-isnt-in-the-portal"></a>Les informations sur les machines virtuelles ne sont pas dans le portail

- Il faut jusqu’à 30 minutes pour que les données de découverte collectées par l’appliance soient affichées dans le portail.
- Au bout de 30 minutes, si les informations n’ont pas été mises à jour , lancez une actualisation des données en effectuant les étapes suivantes :

    1. Dans **Serveurs** > **Évaluation de serveur Azure Migrate**, sélectionnez **Vue d’ensemble**.
    2. Sous **Gérer**, sélectionnez **Agent Health**.
    3. Sélectionnez **Actualiser l’agent**.
    1. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Correctif : Impossible de se connecter à l’hôte ou au cluster

Erreur 50004 : « Impossible de se connecter à un hôte ou à un cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 » peut se produire si le service Azure DNS pour l’appliance ne peut pas résoudre le nom de cluster ou d’hôte que vous avez fourni.

- Si vous voyez cette erreur sur le cluster, le nom de domaine complet du cluster.
- Vous pouvez également voir cette erreur pour les hôtes d’un cluster. Cela indique que l’appliance peut se connecter au cluster, mais que le cluster renvoie les noms d’hôte qui ne sont pas des noms de domaine complets. Pour résoudre cette erreur, mettez à jour le fichier hôtes sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes :
    1. Ouvrez le Bloc-notes en tant qu’admin.
    2. Ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts.
    3. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
    4. Enregistrez et fermez le fichier hôtes.
    5. Vérifiez si l’appliance peut se connecter aux hôtes en utilisant l’application de gestion de l’appliance. Au bout de 30 minutes, vous devriez voir les informations à jour sur ces hôtes dans le Portail Azure.

## <a name="application-discovery-issues"></a>Problèmes liés à la découverte des applications

Actuellement, la découverte des applications est uniquement prise en charge pour les machines virtuelles VMware. La prise en charge de serveurs physiques et machines virtuelles Hyper-V sera possible dans le futur.

La découverte des applications vous oblige à fournir des informations d’identification de machine virtuelle dans l’appliance. Si vous n’avez pas fourni d’informations d’identification de machine virtuelle dans l’appliance, la détection des applications ne fonctionnera pas. [En savoir plus](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-vcenter-server-permissions) sur les privilèges d’accès nécessaires pour vCenter Server et les machines virtuelles VMware. Si vous avez fourni des informations d’identification de machine virtuelle dans l’appliance et que la détection des applications échoue, consultez le tableau suivant pour identifier la cause de l’échec et de l’action de correction :

**Code d’erreur** | **Message** | **Cause possible** | **Action recommandée**
--- | --- | --- | ---
10000 | Impossible de découvrir les applications installées sur le serveur. | Cela peut se produire si le système d’exploitation exécuté sur le serveur n’est ni Windows, ni Linux. | La découverte des applications installées est uniquement prise en charge pour les serveurs Windows et Linux.
10001 | Impossible de récupérer les applications installées sur le serveur. | Ceci est dû à une erreur interne, car des fichiers sont manquants dans l’appliance. | Contactez le support Microsoft.
10002 | Impossible de récupérer les applications installées sur le serveur. | Cela peut se produire si l’agent de découverte dans l’appliance Azure Migrate ne fonctionne pas correctement. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft.
10003 | Impossible de récupérer les applications installées sur le serveur. | Cela peut se produire si l’agent de découverte ne fonctionne pas correctement. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft.
10004 | Impossible de découvrir les applications installées pour les machines <Windows/Linux>. |  Les informations d’identification pour accéder aux machines <Windows/Linux> n’ont pas été fournies dans l’appliance Azure Migrate | Ajoutez des informations d’identification dans l’appliance Azure Migrate qui accède aux machines <Windows/Linux>.
10005 | Impossible d’accéder au serveur local. | Cela peut se produire si les informations d’identification permettant à la machine d’accéder au serveur sont incorrectes. | Mettez à jour les informations d’identification fournies dans l’appliance et vérifiez que le serveur est accessible avec ces informations d’identification.
10006 | Impossible d’accéder au serveur local. | Cela peut se produire si le système d’exploitation exécuté sur le serveur n’est ni Windows, ni Linux. | La découverte des applications installées est uniquement prise en charge pour les serveurs Windows et Linux.
9000 | Impossible de découvrir les applications installées sur la machine virtuelle. | Les outils VMware ne sont peut-être pas installés ou sont endommagés. | Installez/réinstallez les outils VMware sur la machine virtuelle et vérifiez si elle est en cours d’exécution.
9001 | Impossible de découvrir les applications installées sur la machine virtuelle. | Les outils VMware ne sont peut-être pas installés ou sont endommagés. | Installez/réinstallez les outils VMware sur la machine virtuelle et vérifiez si elle est en cours d’exécution.
9002 | Impossible de découvrir les applications installées sur la machine virtuelle. | Les outils VMware ne sont peut-être pas en cours d’exécution. | Installez/réinstallez les outils VMware sur la machine virtuelle et vérifiez si elle est en cours d’exécution.
9003 | Impossible de découvrir les applications installées sur le serveur. | Cela peut se produire si le système d’exploitation exécuté sur le serveur n’est ni Windows, ni Linux. | La découverte des applications installées est uniquement prise en charge pour les serveurs Windows et Linux.
9004 | Impossible de découvrir les applications installées sur le serveur. | Cela peut se produire si la machine virtuelle est hors tension. | Pour découvrir les applications installées sur le serveur, vérifiez que la machine virtuelle est sous tension.
9005 | Impossible de découvrir les applications installées sur la machine virtuelle. | Cela peut se produire si le système d’exploitation exécuté sur la machine virtuelle n’est ni Windows, ni Linux. | La découverte des applications installées est uniquement prise en charge pour les serveurs Windows et Linux.
9006 | Impossible de récupérer les applications installées sur le serveur. | Cela peut se produire si l’agent de découverte ne fonctionne pas correctement. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft.
9007 | Impossible de récupérer les applications installées sur le serveur. | Cela peut se produire si l’agent de découverte ne fonctionne pas correctement. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft.
9008 | Impossible de récupérer les applications installées sur le serveur. | Ce problème peut se produire en raison d’une erreur interne.  | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft.
9009 | Impossible de récupérer les applications installées sur le serveur. | Le problème peut se produire si les paramètres de contrôle de compte d’utilisateur (UAC) Windows sur le serveur sont restrictifs et empêchent la découverte des applications installées. | Recherchez les paramètres « Contrôle de compte d’utilisateur » sur le serveur et configurez le paramètre UAC du serveur sur l’un des deux niveaux inférieurs.
9010 | Impossible de récupérer les applications installées sur le serveur. | Ce problème peut se produire en raison d’une erreur interne.  | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft.
8084 | Impossible de découvrir les applications en raison d'une erreur VMware :  <Exception from VMware> | L’appliance Azure Migrate utilise des API VMware pour découvrir des applications. Ce problème peut se produire en raison d’une exception levée par vCenter Server pendant la tentative de découverte des applications. Le message de défaillance de VMware apparaît dans le message d’erreur affiché dans le portail. | Consultez la [documentation de VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html), recherchez le message de défaillance et suivez les étapes de dépannage dans l’article VMware pour résoudre le problème. Si vous ne parvenez toujours pas à résoudre le problème, contactez le support Microsoft.


## <a name="fix-assessment-readiness"></a>Corriger la préparation de l’évaluation

Corrigez les problèmes de préparation à l’évaluation comme suit :

**Problème** | **Correctif**
--- | ---
Type de démarrage non pris en charge | Azure ne prend pas en charge les machines virtuelles associées au type de démarrage EFI. Nous vous recommandons de convertir le type de démarrage en BIOS avant d’effectuer une migration. <br/><br/>Vous pouvez utiliser Azure Migrate Server Migration pour gérer la migration de ces machines virtuelles. Celui-ci permet de convertir le type de démarrage de la machine virtuelle en BIOS au cours de la migration.
Système d’exploitation Windows pris en charge de manière conditionnelle | Le système d’exploitation n’est plus pris en charge et a besoin d’un programme Custom Support Agreement (CSA) pour bénéficier d’une [prise en charge dans Azure](https://aka.ms/WSosstatement). Envisagez de mettre à niveau avant d’effectuer la migration vers Azure.
Système d’exploitation Windows non pris en charge | Azure ne prend en charge que [certaines versions du système d’exploitation Windows](https://aka.ms/WSosstatement). Envisagez de mettre à niveau la machine avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Linux approuvés sous condition | Azure n’approuve que [certaines versions du système d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). Envisagez de mettre à niveau la machine avant d’effectuer la migration vers Azure.
Systèmes d’exploitation Linux non approuvés | La machine peut démarrer dans Azure, mais Azure ne fournit aucune prise en charge du système d’exploitation. Envisagez de mettre à niveau vers une [version approuvée de Linux](../virtual-machines/linux/endorsed-distros.md) avant d’effectuer la migration vers Azure.
Système d’exploitation inconnu | Le système d’exploitation de la machine virtuelle a été spécifié comme étant du type « Autre » dans vCenter Server. Ce comportement empêche Azure Migrate de vérifier la préparation de la machine virtuelle à Azure. Vérifiez que le système d’exploitation est [pris en charge](https://aka.ms/azureoslist) par Azure avant de procéder à la migration.
Version de bit non prise en charge | Les machines virtuelles dotées d’un système d’exploitation 32 bits peuvent être démarrées dans Azure. Toutefois, il est recommandé d’effectuer une mise à niveau vers la version 64 bits avant de procéder à la migration vers Azure.
Nécessite un abonnement Microsoft Visual Studio. | La machine exécute un système d’exploitation client Windows qui est pris en charge uniquement dans un abonnement Visual Studio.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance de stockage nécessaire | Les performances de stockage (IOPS et débit) nécessaires à la machine dépassent le niveau de prise en charge Azure. Réduisez les besoins de stockage de la machine avant la migration.
Nous n’avons pas trouvé de machine virtuelle correspondant au niveau de performance réseau nécessaire | Les performances réseau (entrée/sortie) requises pour la machine dépassent la prise en charge des machines virtuelles Azure. Réduisez les exigences de mise en réseau de la machine.
Nous n’avons pas trouvé de machine virtuelle à l’emplacement spécifié | Utilisez un emplacement cible différent avant la migration.
Un ou plusieurs disques ne sont pas adaptés | Un ou plusieurs disques attachés à la machine virtuelle ne répondent pas aux exigences Azure.A<br/><br/> Azure Migrate : Server Assessment ne prend pas en charge les disques SSD Ultra et évalue les disques en fonction des limites appliquées aux disques managés Premium (32 To).<br/><br/> Pour chaque disque attaché à la machine virtuelle, assurez-vous que la taille du disque est < 64 To (pris en charge par les disques SSD Ultra).<br/><br/> Si ce n’est pas le cas, réduisez la taille du disque avant d’effectuer la migration vers Azure, ou utilisez plusieurs disques dans Azure et [associez-les](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) pour bénéficier de limites de stockage supérieures. Vérifiez que les performances (IOPS et débit) nécessaires à chaque disque sont prises en charge par les [disques de la machine virtuelle managée](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) Azure.
Un ou plusieurs adaptateurs réseau ne sont pas adaptés | Supprimez les cartes réseau non utilisées de la machine avant la migration.
Le nombre de disques dépasse la limite autorisée | Supprimez les disques non utilisés de la machine avant la migration.
La taille du disque dépasse la limite autorisée | Azure Migrate : Server Assessment ne prend pas en charge les disques SSD Ultra, et évalue les disques en fonction des limites appliquées aux disques Premium (32 To).<br/><br/> Toutefois, Azure prend en charge les disques d’une taille maximale de 64 To (prise en charge par les disques SSD Ultra). Réduisez la taille des disques pour passer en dessous des 64 To avant la migration, ou utilisez plusieurs disques Azure et [associez-les](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) pour bénéficier de limites de stockage plus élevées.
Disque indisponible dans l'emplacement spécifié | Assurez-vous que le disque se trouve dans votre emplacement cible avant la migration.
Disque indisponible pour la redondance spécifiée | Le disque doit utiliser le type de stockage de redondance défini dans les paramètres d'évaluation (LRS par défaut).
Impossible de déterminer l’adéquation du disque en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Nous n'avons pas trouvé de machine virtuelle avec les cœurs et la mémoire requis | Azure n’a pas trouvé de type de machine virtuelle adapté. Réduisez la mémoire et le nombre de cœurs de la machine sur site avant de migrer.
Impossible de déterminer l’adéquation de la machine virtuelle en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’un ou plusieurs disques en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.
Impossible de déterminer l’adéquation d’une ou plusieurs cartes réseau en raison d’une erreur interne | Essayez de créer une nouvelle évaluation pour le groupe.

## <a name="linux-vms-are-conditionally-ready"></a>Les machines virtuelles Linux sont « préparées sous condition »

Server Assessment marque les machines virtuelles Linux comme « préparées sous condition » en raison d’un écart connu dans Server Assessment.

- L’écart l’empêche de détecter la version mineure du système d’exploitation Linux installé sur les machines virtuelles locales.
- Par exemple, pour RHEL 6.10, Server Assessment ne détecte actuellement que RHEL 6 comme version du système d’exploitation.
-  Dans la mesure où Azure approuve uniquement certaines versions de Linux, les machines virtuelles Linux sont actuellement marquées comme « Prêtes sous condition » dans Server Assessment.
- Vous pouvez déterminer si le système d’exploitation Linux exécuté sur la machine virtuelle locale est approuvé dans Azure en consultant la [prise en charge de Linux par Azure](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Une fois que vous avez vérifié la distribution approuvée, vous pouvez ignorer cet avertissement.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Les références SKU Azure dépassent le dimensionnement local

Azure Migrate Server Assessment peut recommander des références SKU de machine virtuelle Azure avec davantage de cœurs et de mémoire que la répartition locale actuelle en fonction du type d’évaluation :


- La recommandation en matière de référence SKU de machine virtuelle varie en fonction des propriétés d’évaluation.
- Cela est affecté par le type d’évaluation que vous effectuez dans Server Assessment : une évaluation effectuée *En fonction des performances* et une évaluation effectuée *Localement*.
- Pour les évaluations en fonction des performances, Server Assessment prend en compte les données d’utilisation des machines virtuelles locales (processeur, mémoire, utilisation des disques et du réseau) afin de déterminer la référence SKU de machine virtuelle cible appropriée pour vos machines virtuelles locales. Il ajoute également un facteur de confort pour déterminer l’utilisation effective.
- Pour le dimensionnement local, les données de performances ne sont pas prises en compte et la référence SKU cible est recommandée en fonction de la répartition locale.

Pour illustrer la façon dont cela peut affecter les recommandations, prenons un exemple :

Nous disposons d’une machine virtuelle locale avec quatre cœurs et huit Go de mémoire, avec une utilisation de l’UC de 50 % et une utilisation de la mémoire de 50 %, ainsi qu’un facteur de confort spécifié de 1,3.

-  Si l’évaluation est définie sur **Localement**, il est recommandé d’utiliser une référence SKU de machine virtuelle Azure avec 4 cœurs et 8 Go de mémoire.
- Si l’évaluation est basée sur les performancess, d’après l’utilisation effective de l’UC et de la mémoire (50 % de 4 cœurs * 1,3 = 2,6 cœurs et 50 % de 8 Go de mémoire * 1,3 = 5,3 Go de mémoire), la référence SKU de machine virtuelle la moins coûteuse avec quatre cœurs (nombre de cœurs pris en charge le plus proche) et huit Go de mémoire (taille de mémoire prise en charge la plus proche) est celle qui est recommandée.
- [En savoir](concepts-assessment-calculation.md#sizing) plus sur le dimensionnement de l’évaluation.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Références SKU de disque Azure supérieures aux locales

Azure Migrate Server Assessment peut recommander un disque plus volumineux en fonction du type d’évaluation.
- Dans Server Assessment, le dimensionnement du disque dépend de deux propriétés d’évaluation : le critère de dimensionnement et le type de stockage.
- Si le critère de dimensionnement est **En fonction des performances** et que le type de stockage est défini sur **Automatique**, les valeurs de débit et d’IOPS du disque sont prises en compte lors de l’identification du type de disque cible (HDD Standard, SSD Standard ou Premium). Une référence SKU de disque parmi celles du type approprié est ensuite recommandée conformément aux exigences de taille du disque local.
- Si le critère de dimensionnement est **En fonction des performances** et si le type de stockage est **Premium**, une référence SKU de disque Premium dans Azure est recommandée conformément aux exigences de débit, d’IOPS et de taille du disque local. La même logique est utilisée pour effectuer le dimensionnement du disque quand le critère de dimensionnement est défini sur **Localement** et que le type de stockage est **HDD Standard**, **SSD Standard** ou **Premium**.

Par exemple, si vous avez un disque local avec 32 Go de mémoire, mais que la valeur d’IOPS en lecture et écriture agrégée pour le disque est de 800 IOPS, Server Assessment recommande un disque Premium (en raison des exigences d’IOPS plus élevées), puis recommande une référence SKU de disque capable de prendre en charge la taille et les IOPS nécessaires. La correspondance la plus proche dans cet exemple serait P15 (256 Go, 1100 IOPS). Par conséquent, même si la taille nécessaire au disque local était de 32 Go, Server Assessment a recommandé un disque avec une plus grande taille en raison de l’exigence élevée du disque local concernant les IOPS.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Correctif : Pourcentage de noyau ou de mémoire utilisé manquant

Server Assessment signale « PercentageOfCoresUtilizedMissing » ou « PercentageOfMemoryUtilizedMissing » lorsque l’appliance Azure Migrate ne peut pas collecter les données de performances pour les machines virtuelles locales appropriées.

- Cela peut se produire si les machines virtuelles sont désactivées pendant la durée de l’évaluation. L’appliance ne peut pas collecter les données de performances d’une machine virtuelle lorsque celle-ci est désactivée.
- S’il manque uniquement les compteurs de mémoire et si vous tentez d’évaluer des machines virtuelles Hyper-V, vérifiez si la mémoire dynamique est activée sur ces machines. Il existe un problème connu pour les machines virtuelles Hyper-V qui empêche l’appliance Azure Migrate de collecter les données d’utilisation de la mémoire pour les machines virtuelles sur lesquelles la mémoire dynamique n’est pas activée.
- Si l’un des compteurs de performances est manquant, Azure Migrate Server Assessment a recours aux cœurs et à la mémoire alloués, et recommande une taille de machine virtuelle adaptée.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>Les coûts de licence du système d’exploitation de machine virtuelle sont-ils inclus dans l’évaluation des coûts ?

Azure Migrate Server Assessment estime le coût de la licence du système d’exploitation pour les ordinateurs Windows, mais pas pour les ordinateurs Linux.

## <a name="performance-history-and-percentile-use"></a>Historique des performances et utilisation des centiles

Ces propriétés s’appliquent uniquement au dimensionnement basé sur les performances dans Azure Migrate Server Assessment.

Server Assessment recueille les données de performances des machines locales de manière continue, et les utilise pour recommander la référence SKU de machine virtuelle et la référence SKU de disque dans Azure. Ces données de performances sont collectées par Server Assessment de la façon suivante :
- L’appliance Azure Migrate profile en continu l’environnement local afin de recueillir des données d’utilisation en temps réel toutes les 20 secondes pour les machines virtuelles VMware, et toutes les 30 secondes pour les machines virtuelles Hyper-V.
- L’appliance cumule les échantillons de 20 ou 30 secondes afin de créer un point de données unique toutes les 10 minutes. Pour créer le point de données, l’appliance sélectionne la valeur maximale de tous les échantillons de 20 secondes et de 30 secondes, puis l’envoie à Azure.
- Quand vous créez une évaluation dans Server Assessment, la valeur d’utilisation représentative est identifiée en fonction de la durée de l’historique des performances et de la valeur de centile d’utilisation. Par exemple, si l’historique des performances est d’une semaine et si le centile d’utilisation est le 95e, Azure Migrate trie tous les points d’échantillonnage de 10 minutes pour la dernière semaine dans l’ordre croissant, puis sélectionne le 95e centile comme valeur représentative.
- Le 95e centile vous permet d’ignorer les valeurs hors norme qui peuvent être incluses quand vous choisissez le 99e centile.
- Si vous souhaitez choisir l’utilisation maximale de la période et si vous ne souhaitez pas ignorer les valeurs hors norme, vous devez sélectionner le 99e centile comme utilisation en centile.


## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Impossible de trouver la visualisation des dépendances pour Azure Government

Azure Migrate dépend de Service Map pour la fonctionnalité de visualisation des dépendances. Étant donné que Service Map n’est pas disponible pour Azure Government, cette fonctionnalité de visualisation n’est pas disponible dans Azure Government.

## <a name="dependencies-dont-show-after-installing-agents"></a>Les dépendances ne sont pas affichées après l’installation des agents

Une fois que vous avez installé les agents de visualisation des dépendances sur les machines virtuelles locales, Azure Migrate prend généralement 15 à 30 minutes pour afficher les dépendances dans le portail. Si vous avez attendu plus de 30 minutes, assurez-vous que le Microsoft Monitoring Agent (MMA) peut se connecter à l’espace de travail Log Analytics.

Pour les machines virtuelles Windows :
1. Dans le panneau de configuration, démarrez MMA.
2. Dans les **propriétés Microsoft Monitoring Agent** > **Azure Log Analytics (OMS)** , assurez-vous que l’**état** de l’espace de travail est vert.
3. Si l’état n’est pas vert, essayez de supprimer l’espace de travail et de le rajouter à MMA.

      ![Boîte de dialogue Microsoft Monitoring Agent](./media/troubleshooting-general/mma-status.png)

Pour les machines virtuelles Linux, vérifiez que tout s’est bien passé pour les commandes d’installation MMA et Dependency Agent.

## <a name="supported-mma-os"></a>Système d’exploitation MMA pris en charge

 Passez en revue les systèmes d’exploitation [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) et [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) pris en charge.

## <a name="supported-dependency-agent-os"></a>Système d’exploitation de Dependency Agent pris en charge

Passez en revue les systèmes d’exploitation [Windows et Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) pris en charge.

## <a name="dependencies-for-more-than-an-hour"></a>Dépendances pendant plus d’une heure

Même si Azure Migrate vous permet de revenir à une date du mois passé, la durée maximale pendant laquelle vous pouvez visualiser les dépendances est fixée à 1 heure.

Par exemple, vous pouvez utiliser la fonctionnalité de durée dans la carte des dépendances pour voir les dépendances d’hier, mais uniquement sur une période d’une heure.

Vous pouvez aussi utiliser les journaux d’activité Azure Monitor pour [interroger les données de dépendance](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) sur une plus longue durée.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Je ne peux pas visualiser les dépendances pour les groupes avec plus de 10 machines virtuelles

Dans Azure Migrate Server Assessment, vous pouvez [visualiser les dépendances pour des groupes](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) contenant jusqu’à 10 machines virtuelles. Pour les groupes plus grands, nous vous recommandons de fractionner les machines virtuelles en groupes plus petits pour visualiser les dépendances.

## <a name="machines-show-install-agent-not-view-dependencies"></a>Les machines affichent « Installer l’agent » et non « Afficher les dépendances »

Après la migration des machines avec la visualisation des dépendances activée vers Azure, les ordinateurs peuvent afficher l’action « Installer l’agent » au lieu de « Afficher les dépendances » en raison du comportement suivant :


- Après la migration vers Azure, les machines locales sont désactivées et les machines virtuelles équivalentes sont lancées dans Azure. Ces machines acquièrent une adresse MAC différente.
- Les ordinateurs peuvent également avoir une adresse IP différente, selon que vous avez ou non conservé l’adresse IP locale.
- Si les adresses MAC et IP sont différentes des adresses locales, Azure Migrate n’associe pas les machines locales aux données de dépendance Service Map. Dans ce cas, l’option d’installation de l’agent s’affiche au lieu des dépendances.
- Après une migration de test vers Azure, les machines locales restent allumées comme prévu. Les machines équivalentes lancées dans Azure acquièrent une adresse MAC différente et peuvent acquérir une adresse IP différente. À moins que vous ne bloquiez le trafic sortant des journaux Azure Monitor provenant de ces machines, Azure Migrate n’associera pas les machines locales aux données de dépendances Service Map, et affichera donc l’option permettant d’installer des agents au lieu de celle permettant d’afficher les dépendances.


## <a name="collect-network-traffic-logs-in-portal"></a>Collecter les journaux du trafic réseau dans le portail

Collectez les journaux comme suit :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
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
