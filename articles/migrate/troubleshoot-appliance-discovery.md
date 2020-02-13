---
title: Résoudre les problèmes de déploiement et de découverte d’appliance Azure Migrate
description: Obtenir de l’aide pour le déploiement de l’appliance Azure Migrate et la découverte des machines.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 37da62a4eb0f934133d6486872ba319138299614
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048701"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Résoudre les problèmes d’appliance et de découverte Azure Migrate

Cet article vous aide à résoudre les problèmes liés au déploiement de l'appliance [Azure Migrate](migrate-services-overview.md) et à l’utilisation de l’appliance pour découvrir des machines locales.


## <a name="whats-supported"></a>Prise en charge

[Passez en revue](migrate-appliance.md) la configuration requise pour la prise en charge de l’appliance.


## <a name="invalid-ovf-manifest-entry"></a>« entrée de manifeste OVF non valide »

Si vous recevez l’erreur « Le fichier manifeste indiqué n’est pas valide : entrée de manifeste OVF non valide », procédez comme suit :

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. [Plus d’informations](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si le déploiement échoue une nouvelle fois et vous utilisez le client VMware vSphere pour déployer le fichier OVF, essayez plutôt de le déployer à l’aide du client web vSphere. Si le déploiement échoue de nouveau, essayez un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) avec le client web (https://<*adresse IP de l’hôte*>/ui).
   - Dans **Accueil** > **Inventaire**, sélectionnez **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
4. Si le déploiement échoue toujours, contactez le support Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Impossible de se connecter à Internet

Cela peut se produire si l’ordinateur de l’appliance se trouve derrière un proxy.

- Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
- Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, ajoutez [ces URL](migrate-appliance.md#url-access) à une liste verte.
- Si vous utilisez un proxy d’interception pour vous connecter à Internet, importez le certificat de proxy sur la machine virtuelle de l’appliance en suivant [ces étapes](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Erreur de synchronisation de date/heure

Une erreur de synchronisation de la date et de l’heure (802) indique que l’horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l’heure actuelle. Modifiez l’heure de l’horloge sur la machine virtuelle du collecteur afin qu’elle corresponde à l’heure actuelle :

1. Ouvrez une invite de commandes d’administration sur la machine virtuelle.
2. Pour vérifier le fuseau horaire, exécutez **w32tm /tz**.
3. Pour synchroniser l’heure, exécutez **w32tm /resync**.


## <a name="unabletoconnecttoserver"></a>« UnableToConnectToServer »

Si cette erreur de connexion s’affiche, il est possible que vous ne puissiez pas vous connecter à vCenter Server *Nom_Serveur*.com:9443. Les détails de l’erreur indiquent qu’aucun des points de terminaison qui écoutent https://*nom_serveur*.com:9443/sdk ne peut accepter le message.

- Vérifiez si vous exécutez la dernière version de l’appliance. Si ce n’est pas le cas, mettez-la à niveau vers la [dernière version](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Si le problème se produit encore avec la dernière version, il est possible que l’appliance ne parvienne pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué soit incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tente de se connecter au port numéro 443.

    1. Effectuez un test ping sur *Nom_Serveur*.com à partir de l’appliance.
    2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter à l’aide de l’adresse IP.
    3. Identifiez le numéro de port correct pour se connecter à vCenter Server.
    4. Vérifiez que vCenter Server est opérationnel.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erreur 60052/60039 : L’appliance n’est peut-être pas inscrite

- L’erreur 60052 « L’appliance n’est peut-être pas inscrite correctement dans le projet Azure Migrate » se produit si le compte Azure utilisé pour inscrire l’appliance ne dispose pas des autorisations suffisantes.
    - Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
    - [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sur les rôles et autorisations Azure requis.
- L’erreur 60039 « L’appliance n’est peut-être pas inscrite correctement dans le projet Azure Migrate » peut se produire si l’inscription échoue car le projet Azure Migrate utilisé pour inscrire l’appliance est introuvable.
    - Dans le Portail Azure, vérifiez si le projet se trouve dans le groupe de ressources.
    - Si le projet n’existe pas, créez un projet Azure Migrate dans votre groupe de ressources et réinscrivez l’appliance. [Découvrez comment](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) créer un nouveau projet.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Error 60030/60031 : Échec de l’opération de gestion de Key Vault

Si l’erreur 60030 ou 60031 s’affiche, « Une opération de gestion d’Azure Key Vault a échoué », procédez comme suit :
- Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
- Vérifiez que le compte a accès au coffre de clés spécifié dans le message d’erreur, puis retentez l’opération.
- Si le problème persiste, contactez le support technique Microsoft.
- [Apprenez-en davantage](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sur les rôles et autorisations Azure requis.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erreur 60028 : Impossible de démarrer la découverte

Erreur 60028 : « Impossible de démarrer la découverte en raison d’une erreur. L’opération a échoué pour la liste spécifiée d’hôtes ou de clusters » indique que la découverte n’a pas pu être démarrée sur les hôtes répertoriés dans l’erreur en raison d’un problème d’accès ou de récupération des informations de la machine virtuelle. Les autres hôtes ont bien été ajoutés.

- Ajoutez de nouveau les hôtes listés dans l’erreur, à l’aide de l’option **Ajouter un hôte**.
- En cas d’erreur de validation, reportez-vous au guide de correction pour résoudre l’erreur, puis réessayez l’option **Enregistrer et lancer la découverte**.

## <a name="error-60025-azure-ad-operation-failed"></a>Erreur 60025 : Une opération Azure AD a échoué 
Erreur 60025 : « Une opération Azure AD a échoué. L’erreur s’est produite lors de la création ou de la mise à jour de l’application Azure AD » se produit lorsque le compte d’utilisateur Azure utilisé pour lancer la découverte est différent de celui utilisé pour inscrire l’appliance. Effectuez l’une des actions suivantes :

- Vérifiez que le compte d’utilisateur qui lance la découverte est identique à celui utilisé pour inscrire l’appliance.
- Accordez des autorisations d’accès à l’application Azure Active Directory au compte d’utilisateur pour lequel l’opération de découverte échoue.
- Supprimez le groupe de ressources créé précédemment pour le projet Azure Migrate. Créez un autre groupe de ressources pour recommencer.
- [En savoir plus](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sur les autorisations d’application Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erreur 50004 : Impossible de se connecter à l’hôte ou au cluster

Erreur 50004 : « Impossible de se connecter à un hôte ou à un cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 » peut se produire si le service Azure DNS pour l’appliance ne peut pas résoudre le nom de cluster ou d’hôte que vous avez fourni.

- Si vous voyez cette erreur sur le cluster, le nom de domaine complet du cluster.
- Vous pouvez également voir cette erreur pour les hôtes d’un cluster. Cela indique que l’appliance peut se connecter au cluster, mais que le cluster renvoie les noms d’hôte qui ne sont pas des noms de domaine complets. Pour résoudre cette erreur, mettez à jour le fichier hôtes sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes :
    1. Ouvrez le Bloc-notes en tant qu’admin.
    2. Ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts.
    3. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
    4. Enregistrez et fermez le fichier hôtes.
    5. Vérifiez si l’appliance peut se connecter aux hôtes en utilisant l’application de gestion de l’appliance. Au bout de 30 minutes, vous devriez voir les informations à jour sur ces hôtes dans le Portail Azure.

## <a name="discovered-vms-not-in-portal"></a>Machines virtuelles découvertes absentes du portail

Si l’état de découverte est « Découverte en cours », mais que vous ne voyez pas encore les machines virtuelles dans le portail, patientez quelques minutes :
- Il faut environ 15 minutes pour une machine virtuelle VMware.
- Il faut environ deux minutes pour chaque hôte ajouté pour la découverte de machines virtuelles Hyper-V.

Si vous attendez et que l’état ne change pas, sélectionnez **Actualiser** sous l’onglet **Serveurs**. Ceci doit normalement montrer le nombre de serveurs découverts dans Azure Migrate : Server Assessment et Azure Migrate : Server Migration.

Si cela ne fonctionne pas et que vous découvrez des serveurs VMware :

- Vérifiez que les autorisations du compte vCenter que vous avez spécifié ont été définies correctement, avec un accès à au moins une machine virtuelle.
- Azure Migrate ne peut pas découvrir les machines virtuelles VMware si le compte vCenter s’est vu octroyer un accès au niveau du dossier de machine virtuelle vCenter. [En savoir plus](tutorial-assess-vmware.md#set-the-scope-of-discovery) sur la définition de l’étendue de la découverte.

## <a name="vm-data-not-in-portal"></a>Données de machine virtuelle absentes du portail

Si des machines virtuelles découvertes n’apparaissent pas dans le portail ou si des données de machine virtuelle sont obsolètes, patientez quelques minutes. L’affichage sur le portail des modifications de données de configuration de machine virtuelle découverte prend jusqu’à 30 minutes. L’affichage de modifications de données d’application peut prendre plusieurs heures. Si aucune donnée n’apparaît après ce délai, essayez d’actualiser comme suit.

1. Dans **Serveurs** > **Évaluation de serveur Azure Migrate**, sélectionnez **Vue d’ensemble**.
2. Sous **Gérer**, sélectionnez **Agent Health**.
3. Sélectionnez **Actualiser l’agent**.
4. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="deleted-vms-appear-in-portal"></a>Les machines virtuelles supprimées apparaissent dans le portail

Si vous supprimez des machines virtuelles et qu’elles apparaissent toujours dans le portail, attendez 30 minutes. Si elles apparaissent toujours, actualisez comme décrit ci-dessus.

## <a name="common-app-discovery-errors"></a>Erreurs courantes de découverte d’applications

Azure Migrate prend en charge la découverte des applications, des rôles et des caractéristiques à l’aide d’Azure Migrate : Server Assessment. La découverte des applications est actuellement prise en charge pour VMware uniquement. [En savoir plus](how-to-discover-applications.md) sur les exigences et les étapes de configuration de la découverte d’application.

Les erreurs classiques de découverte d’application sont résumées dans le tableau. 

**Error** | **Cause** | **Action**
--- | --- | --- | ---
10000 : « Impossible de découvrir les applications installées sur le serveur. » | Cela peut se produire si le système d’exploitation de l’ordinateur n’est pas Windows ou Linux. | Utilisez uniquement la découverte d’application pour Windows/Linux.
10001 : « Impossible de récupérer les applications installées sur le serveur. » | Erreur interne : certains fichiers sont manquants dans l’appliance. | Contactez le Support Microsoft.
10002 : « Impossible de récupérer les applications installées sur le serveur. » | L’agent de découverte sur l’appliance peut ne pas fonctionner correctement. | Si le problème ne se résout pas de lui-même dans les 24 heures, contactez le support technique.
10003 « Impossible de récupérer les applications installées sur le serveur. » | L’agent de découverte sur l’appliance peut ne pas fonctionner correctement. | Si le problème ne se résout pas de lui-même dans les 24 heures, contactez le support technique.
10004 : « Impossible de découvrir les applications installées pour les machines <Windows/Linux>. » |  Les informations d’identification permettant d’accéder aux machines <Windows/Linux> n’ont pas été fournies dans l’appliance.| Ajoutez des informations d’identification à l’appliance qui accède aux machines <Windows/Linux>.
10005 : « Impossible d’accéder au serveur local. » | Les informations d’identification d’accès sont peut-être incorrectes. | Mettez à jour les informations d’identification de l’appliance pour vous assurer d’avoir accès à la machine correspondante. 
10006 : « Impossible d’accéder au serveur local. » | Cela peut se produire si le système d’exploitation de l’ordinateur n’est pas Windows ou Linux.|  Utilisez uniquement la découverte d’application pour Windows/Linux.
9000/9001/9002 : « Impossible de découvrir les applications installées sur le serveur. » | Les outils VMware ne sont peut-être pas installés ou sont endommagés. | Installez/réinstallez les outils VMware sur l’ordinateur concerné, puis vérifiez qu’il s’exécute correctement.
9003 : Impossible de découvrir les applications installées sur le serveur. » | Cela peut se produire si le système d’exploitation de l’ordinateur n’est pas Windows ou Linux. | Utilisez uniquement la découverte d’application pour Windows/Linux.
9004 : « Impossible de découvrir les applications installées sur le serveur. » | Cela peut se produire si la machine virtuelle est hors tension. | Pour la découverte, assurez-vous que la machine virtuelle est activée.
9005 : « Impossible de découvrir les applications installées sur la machine virtuelle. | Cela peut se produire si le système d’exploitation de l’ordinateur n’est pas Windows ou Linux. | Utilisez uniquement la découverte d’application pour Windows/Linux.
9006/9007 : « Impossible de récupérer les applications installées sur le serveur. » | L’agent de découverte sur l’appliance peut ne pas fonctionner correctement. | Si le problème ne se résout pas de lui-même dans les 24 heures, contactez le support technique.
9008 : « Impossible de récupérer les applications installées sur le serveur. » | Il s'agit peut-être d'une erreur interne.  | Si le problème ne se résout pas de lui-même dans les 24 heures, contactez le support technique.
9009 : « Impossible de récupérer les applications installées sur le serveur. » | Peut se produire si les paramètres de contrôle de compte d’utilisateur (UAC) Windows sur le serveur sont restrictifs et empêchent la découverte des applications installées. | Recherchez les paramètres « Contrôle de compte d’utilisateur » sur le serveur, et configurez le paramètre UAC du serveur sur l’un des deux niveaux inférieurs.
9010 : « Impossible de récupérer les applications installées sur le serveur. » | Il s'agit peut-être d'une erreur interne.  | Si le problème ne se résout pas de lui-même dans les 24 heures, contactez le support technique.
8084 : « Impossible de découvrir les applications en raison d'une erreur VMware :  <Exception from VMware> » | L’appliance Azure Migrate utilise des API VMware pour découvrir des applications. Ce problème peut se produire si une exception est déclenchée par vCenter Server pendant la tentative de découverte des applications. Le message de défaillance de VMware apparaît dans le message d’erreur affiché dans le portail. | Recherchez le message dans la [documentation VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html) et suivez les étapes permettant de le résoudre. Si vous ne pouvez pas résoudre ce problème, contactez le support technique de Microsoft.
9012 : « Impossible de découvrir les applications installées sur le serveur » | Ce problème peut se produire en raison d’une erreur interne.  | Si le problème ne se résout pas de lui-même dans les 24 heures, contactez le support technique.
9013 : « Impossible de découvrir les applications installées sur le serveur » | Un nouveau profil temporaire est créé à chaque connexion à la machine virtuelle.  | Assurez-vous qu’aucun profil temporaire n’est créé pour l’utilisateur invité fourni.



## <a name="next-steps"></a>Étapes suivantes
Configurez une appliance pour [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) ou des [serveurs physiques](how-to-set-up-appliance-physical.md).
