---
title: Résoudre les problèmes de déploiement et de découverte d’appliance Azure Migrate
description: Obtenir de l’aide sur le déploiement d’appliance et la détection de serveurs.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: c952fe33b434aac972be6a1eb03b63698eb64fc6
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782314"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Résoudre les problèmes d’appliance et de découverte Azure Migrate

Cet article vous aide à résoudre les problèmes liés au déploiement de l’appliance [Azure Migrate](migrate-services-overview.md) et à l’utilisation de l’appliance pour découvrir des serveurs locaux.

## <a name="whats-supported"></a>Prise en charge

[Passez en revue](migrate-appliance.md) la configuration requise pour la prise en charge de l’appliance.

## <a name="invalid-ovf-manifest-entry"></a>« entrée de manifeste OVF non valide »

Si vous recevez l’erreur « Le fichier manifeste indiqué n’est pas valide : entrée de manifeste OVF non valide », procédez comme suit :

1. Vérifiez si le fichier OVA de l’appliance Azure Migrate est correctement téléchargé en contrôlant sa valeur de hachage. [Plus d’informations](./tutorial-discover-vmware.md) Si la valeur de hachage ne correspond pas, téléchargez à nouveau le fichier OVA et recommencez le déploiement.
2. Si le déploiement échoue une nouvelle fois et vous utilisez le client VMware vSphere pour déployer le fichier OVF, essayez plutôt de le déployer à l’aide du client web vSphere. Si le déploiement échoue de nouveau, essayez un autre navigateur web.
3. Si vous utilisez le client web vSphere et tentez de le déployer sur vCenter Server 6.5 ou 6.7, essayez de déployer le fichier OVA directement sur l’hôte ESXi :
   - Connectez-vous directement à l’hôte ESXi (au lieu de vCenter Server) avec le client web (https://<*adresse IP de l’hôte*>/ui).
   - Dans **Accueil** > **Inventaire**, sélectionnez **Fichier** > **Déployer le modèle OVF**. Accédez au fichier OVA et terminez le déploiement.
4. Si le déploiement échoue toujours, contactez le support Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Impossible de se connecter à Internet

Cela peut se produire si le serveur de l’appliance se trouve derrière un proxy.

- Veillez à fournir les informations d’identification pour l’autorisation si le proxy en a besoin.
- Si vous utilisez un proxy de pare-feu basé sur des URL pour contrôler la connectivité sortante, ajoutez [ces URL](migrate-appliance.md#url-access) à une liste d’autorisation.
- Si vous utilisez un proxy d’interception pour vous connecter à Internet, importez le certificat de proxy sur l’appliance en suivant [ces étapes](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Impossible de se connecter à Azure à partir de l’application web de l’appliance

L’erreur « Désolé, nous n'avons pas pu vous connecter » s’affiche si vous utilisez un compte Azure incorrect pour vous connecter à Azure. Cette erreur se produit pour deux raisons :

- Si vous vous connectez à l’application web de l’appliance pour le cloud public, à l’aide des informations d’identification du compte d’utilisateur pour le portail cloud Government.
- Si vous vous connectez à l’application web de l’appliance pour le cloud Government à l’aide des informations d’identification du compte d’utilisateur pour le portail du cloud privé.

Vérifiez que vous utilisez les informations d’identification qui conviennent.

## <a name="datetime-synchronization-error"></a>Erreur de synchronisation de date/heure

Une erreur de synchronisation de la date et de l’heure (802) indique que l’horloge du serveur peut être désynchronisée de plus de cinq minutes par rapport à l’heure actuelle. Modifiez l’heure de l’horloge sur le serveur du collecteur afin qu’elle corresponde à l’heure actuelle :

1. Ouvrez une invite de commandes d’administration sur le serveur.
2. Pour vérifier le fuseau horaire, exécutez **w32tm /tz**.
3. Pour synchroniser l’heure, exécutez **w32tm /resync**.

## <a name="unabletoconnecttoserver"></a>« UnableToConnectToServer »

Si cette erreur de connexion s’affiche, il est possible que vous ne puissiez pas vous connecter à vCenter Server *Nom_Serveur*.com:9443. Les détails de l’erreur indiquent qu’aucun des points de terminaison qui écoutent `https://\*servername*.com:9443/sdk` ne peut accepter le message.

- Vérifiez si vous exécutez la dernière version de l’appliance. Si ce n’est pas le cas, mettez-la à niveau vers la [dernière version](./migrate-appliance.md).
- Si le problème se produit encore avec la dernière version, il est possible que l’appliance ne parvienne pas à résoudre le nom du serveur vCenter Server spécifié ou que le port indiqué soit incorrect. Par défaut, si le port n’est pas spécifié, le collecteur tente de se connecter au port numéro 443.

    1. Effectuez un test ping sur *Nom_Serveur*.com à partir de l’appliance.
    2. Si l’étape 1 échoue, essayez de vous connecter au serveur vCenter à l’aide de l’adresse IP.
    3. Identifiez le numéro de port correct pour se connecter à vCenter Server.
    4. Vérifiez que vCenter Server est opérationnel.

## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erreur 60052/60039 : L’appliance n’est peut-être pas inscrite

- L’erreur 60052 « L’appliance n’est peut-être pas inscrite correctement dans le projet » se produit si le compte Azure utilisé pour inscrire l’appliance ne dispose pas des autorisations suffisantes.
    - Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
    - [Apprenez-en davantage](./migrate-appliance.md#appliance---vmware) sur les rôles et autorisations Azure requis.
- L’erreur 60039 « L’appliance n’est peut-être pas inscrite correctement dans le projet » peut se produire si l’inscription échoue, car le projet utilisé pour inscrire l’appliance est introuvable.
    - Dans le Portail Azure, vérifiez si le projet se trouve dans le groupe de ressources.
    - Si le projet n’existe pas, créez un projet dans votre groupe de ressources et réinscrivez l’appliance. [Découvrez comment](./create-manage-projects.md#create-a-project-for-the-first-time) créer un nouveau projet.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Error 60030/60031 : Échec de l’opération de gestion de Key Vault

Si l’erreur 60030 ou 60031 s’affiche, « Une opération de gestion d’Azure Key Vault a échoué », procédez comme suit :

- Vérifiez que le compte d’utilisateur Azure utilisé pour inscrire l’appliance dispose au moins d’autorisations Contributeur pour l’abonnement.
- Vérifiez que le compte a accès au coffre de clés spécifié dans le message d’erreur, puis retentez l’opération.
- Si le problème persiste, contactez le support technique Microsoft.
- [Apprenez-en davantage](./migrate-appliance.md#appliance---vmware) sur les rôles et autorisations Azure requis.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erreur 60028 : Impossible de démarrer la découverte

Erreur 60028 : « Impossible de démarrer la découverte en raison d’une erreur. L’opération a échoué pour la liste spécifiée d’hôtes ou de clusters » indique que la détection n’a pas pu être démarrée sur les hôtes répertoriés dans l’erreur en raison d’un problème d’accès ou de récupération des informations du serveur. Les autres hôtes ont bien été ajoutés.

- Ajoutez de nouveau les hôtes listés dans l’erreur, à l’aide de l’option **Ajouter un hôte**.
- En cas d’erreur de validation, reportez-vous au guide de correction pour résoudre l’erreur, puis réessayez l’option **Enregistrer et lancer la découverte**.

## <a name="error-60025-azure-ad-operation-failed"></a>Erreur 60025 : Une opération Azure AD a échoué

Erreur 60025 : « Une opération Azure AD a échoué. L’erreur s’est produite lors de la création ou de la mise à jour de l’application Azure AD » se produit lorsque le compte d’utilisateur Azure utilisé pour lancer la découverte est différent de celui utilisé pour inscrire l’appliance. Effectuez l’une des actions suivantes :

- Vérifiez que le compte d’utilisateur qui lance la découverte est identique à celui utilisé pour inscrire l’appliance.
- Accordez des autorisations d’accès à l’application Azure Active Directory au compte d’utilisateur pour lequel l’opération de découverte échoue.
- Supprimez le groupe de ressources créé précédemment pour le projet. Créez un autre groupe de ressources pour recommencer.
- [En savoir plus](./migrate-appliance.md#appliance---vmware) sur les autorisations d’application Azure Active Directory.

## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erreur 50004 : Impossible de se connecter à l’hôte ou au cluster

Erreur 50004 : « Impossible de se connecter à un hôte ou à un cluster, car le nom du serveur ne peut pas être résolu. Code d’erreur WinRM : 0x803381B9 » peut se produire si le service Azure DNS pour l’appliance ne peut pas résoudre le nom de cluster ou d’hôte que vous avez fourni.

- Si vous voyez cette erreur sur le cluster, le nom de domaine complet du cluster.
- Vous pouvez également voir cette erreur pour les hôtes d’un cluster. Cela indique que l’appliance peut se connecter au cluster, mais que le cluster renvoie les noms d’hôte qui ne sont pas des noms de domaine complets. Pour résoudre cette erreur, mettez à jour le fichier hôtes sur l’appliance en ajoutant un mappage de l’adresse IP et des noms d’hôtes :
    1. Ouvrez le Bloc-notes en tant qu’admin.
    2. Ouvrez le fichier C:\Windows\System32\Drivers\etc\hosts.
    3. Ajoutez l’adresse IP et le nom d’hôte dans une ligne. Répétez cette opération pour chaque hôte ou cluster pour lequel vous voyez cette erreur.
    4. Enregistrez et fermez le fichier hôtes.
    5. Vérifiez si l’appliance peut se connecter aux hôtes en utilisant l’application de gestion de l’appliance. Au bout de 30 minutes, vous devriez voir les informations à jour sur ces hôtes dans le Portail Azure.

## <a name="error-60001-unable-to-connect-to-server"></a>Erreur 60001 : Connexion au serveur impossible

- Assurez-vous qu’il existe de la connectivité entre l’appliance et le serveur
- S’il s’agit d’un serveur Linux, assurez-vous que l’authentification par mot de passe est activée en procédant comme suit :
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier config ssh à l’aide de la commande « vi /etc/ssh/sshd_config ».
    2. Définissez l’option « PasswordAuthentication » sur Oui. Enregistrez le fichier .
    3. Redémarrez le service ssh en exécutant « service sshd restart ».
- S’il s’agit d’un serveur Windows, assurez-vous que le port 5985 est ouvert pour permettre les appels WMI distants.
- Si vous découvrez un serveur GCP Linux et que vous utilisez un utilisateur root, utilisez les commandes suivantes pour modifier le paramètre par défaut de la connexion racine.
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier config ssh à l’aide de la commande « vi /etc/ssh/sshd_config ».
    2. Définissez l’option « PermitRootLogin » sur Oui.
    3. Redémarrez le service ssh en exécutant « service sshd restart ».

## <a name="error-no-suitable-authentication-method-found"></a>Erreur : Aucune méthode d’authentification appropriée n’a été trouvée

Assurez-vous que l’authentification par mot de passe est activée sur le serveur Linux en procédant comme suit :
    1. Connectez-vous au serveur Linux, puis ouvrez le fichier config ssh à l’aide de la commande « vi /etc/ssh/sshd_config ».
    2. Définissez l’option « PasswordAuthentication » sur Oui. Enregistrez le fichier .
    3. Redémarrez le service ssh en exécutant « service sshd restart ».

## <a name="discovered-servers-not-in-portal"></a>Les serveurs détectés n’apparaissent pas dans le portail

Si l’état de découverte est « Découverte en cours », mais que vous ne voyez pas encore les serveurs dans le portail, patientez quelques minutes :

- Il faut environ 15 minutes pour un serveur sur VMware.
- Il faut environ deux minutes pour chaque hôte ajouté pour la découverte de serveurs sur Hyper-V.

Si vous attendez et que l’état ne change pas, sélectionnez **Actualiser** sous l’onglet **Serveurs**. Ceci doit normalement montrer le nombre de serveurs découverts dans Azure Migrate : Découverte et évaluation et Azure Migrate : Migration de serveurs.

Si cela ne fonctionne pas et que vous découvrez des serveurs VMware :

- Vérifiez que les autorisations du compte vCenter que vous avez spécifié ont été définies correctement, avec un accès à au moins un serveur.
- Azure Migrate ne peut pas découvrir les serveurs sur VMware si le compte vCenter s’est vu octroyer un accès au niveau du dossier de machine virtuelle vCenter. [En savoir plus](set-discovery-scope.md) sur la définition de l’étendue de la découverte.

## <a name="server-data-not-in-portal"></a>Données de serveur absentes du portail

Si des serveurs découverts n’apparaissent pas dans le portail ou si des données de serveur sont obsolètes, patientez quelques minutes. Il faut jusqu’à 30 minutes pour que les modifications apportées aux données de configuration du serveur découvert apparaissent dans le portail. L’affichage des modifications dans les données de l’inventaire logiciel peut prendre plusieurs heures. Si aucune donnée n’apparaît après ce délai, essayez d’actualiser comme suit.

1. Dans **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, sélectionnez **Vue d’ensemble**.
2. Sous **Gérer**, sélectionnez **Agent Health**.
3. Sélectionnez **Actualiser l’agent**.
4. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="deleted-servers-appear-in-portal"></a>Les serveurs supprimés s’affichent dans le portail

Si vous supprimez des serveurs et qu’ils apparaissent toujours dans le portail, attendez 30 minutes. Si elles apparaissent toujours, actualisez comme décrit ci-dessus.

## <a name="discovered-software-inventory-and-sql-server-instances-and-databases-not-in-portal"></a>L’inventaire logiciel et les instances et bases de données SQL Server n’apparaissent pas dans le portail

Une fois que vous avez lancé la découverte sur l’appliance, jusqu’à 24 heures peuvent s’écouler avant que les données d’inventaire commencent à s’afficher dans le portail.

Si vous n’avez pas fourni les informations d’identification d’authentification Windows ou SQL Server sur le gestionnaire de configuration de l’appliance, ajoutez les informations d’identification afin que l’appliance puisse les utiliser pour se connecter aux instances SQL Server respectives.

Une fois connectée, l’appliance recueille les données de configuration et de performances des instances et bases de données SQL Server. Les données de configuration SQL Server sont mises à jour toutes les 24 heures, et les données de performances capturées toutes les 30 secondes. Par conséquent, jusqu’à 24 heures peuvent s’écouler avant la mise à jour sur le portail de toute modification des propriétés de l’instance et des bases de données SQL Server, telles que l’état de la base de données, le niveau de compatibilité, etc.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>L’instance SQL Server s’affiche dans l’état « Non connecté » sur le portail

Pour voir les problèmes rencontrés lors de la découverte des instances et bases de données SQL Server, cliquez sur l’état « Non connecté » dans la colonne État de la connexion de la page « Serveurs détectés » de votre projet.

La création d’une évaluation sur des serveurs contenant des instances SQL qui n’ont pas été complètement découvertes ou qui sont dans un état non connecté peut avoir pour effet que l’état de préparation soit marqué comme « Inconnu ».

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Je ne vois pas les données de performances pour certaines cartes réseau sur mes serveurs physiques

Cela peut se produire si la virtualisation Hyper-V est activée sur le serveur physique. Le débit du réseau est capturé sur les cartes réseau virtuelles détectées en raison d’un écart de production.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Erreur : Le fichier chargé n’est pas au format attendu

Certains outils ont des paramètres régionaux qui créent le fichier CSV avec un point-virgule comme délimiteur. Modifiez les paramètres afin que le délimiteur soit une virgule.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>J’ai importé un CSV, mais je vois le message « Détection en cours »

Cet état s’affiche si le chargement de votre CSV a échoué en raison d’une défaillance de validation. Réessayez d’importer le CSV. Vous pouvez télécharger le rapport d’erreurs du chargement précédent et suivre les instructions de correction fournies dans le fichier pour corriger les erreurs. Le rapport d’erreurs peut être téléchargé à partir de la section « Détails de l’importation » de la page « Découvrir des serveurs ».

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>Je ne vois pas les détails de l’inventaire logiciel, même après la mise à jour des informations d’identification d’invité

La détection de l’inventaire logiciel est effectuée une fois toutes les 24 heures. Si vous voulez voir les détails immédiatement, actualisez comme suit. L’opération peut prendre quelques minutes en fonction du nombre de serveurs découverts.

1. Dans **Windows, Linux et SQL Server** > **Azure Migrate : Découverte et évaluation**, sélectionnez **Vue d’ensemble**.
2. Sous **Gérer**, sélectionnez **Agent Health**.
3. Sélectionnez **Actualiser l’agent**.
4. Attendez que l’opération d’actualisation se termine. Vous devez maintenant voir les informations à jour.

## <a name="unable-to-export-software-inventory"></a>Impossible d’exporter l’inventaire logiciel

Vérifiez que l’utilisateur qui télécharge l’inventaire à partir du portail dispose de privilèges de contributeur sur l’abonnement.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Aucune méthode d’authentification appropriée n’a été trouvée pour terminer l’authentification (PublicKey)

L’authentification par clé ne fonctionnera pas, utilisez l’authentification par mot de passe.

## <a name="common-app-discovery-errors"></a>Erreurs courantes de découverte d’applications

Azure Migrate prend en charge la détection de l’inventaire logiciel à l’aide d’Azure Migrate : Découverte et évaluation. La découverte des applications est actuellement prise en charge pour VMware uniquement. [En savoir plus](how-to-discover-applications.md) sur les exigences et les étapes de configuration de la découverte d’application.

Les erreurs classiques de découverte d’application sont résumées dans le tableau.

| **Error** | **Cause** | **Action** |
|--|--|--|
| 9000 : Impossible de détecter l'état de VMware Tools. | Les outils VMware ne sont peut-être pas installés ou sont endommagés. | Vérifiez que VMware Tools est installé et exécuté sur le serveur. |
| 9001 : VMware Tools n’est pas installé. | Les outils VMware ne sont peut-être pas installés ou sont endommagés. | Vérifiez que VMware Tools est installé et exécuté sur le serveur. |
| 9002 : VMware Tools n’est pas en cours d'exécution. | Les outils VMware ne sont peut-être pas installés ou sont endommagés. | Vérifiez que VMware Tools est installé et exécuté sur le serveur. |
| 9003 : Type de système d’exploitation non pris en charge pour la détection de serveur invité. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Les types de système d’exploitation pris en charge sont Windows et Linux uniquement. Si le serveur est effectivement Windows ou Linux, vérifiez le type de système d’exploitation spécifié dans vCenter Server. |
| 9004 : Le serveur n’est pas en cours d’exécution. | Le serveur est hors tension. | Vérifiez que le serveur est sous tension. |
| 9005 : Type de système d’exploitation non pris en charge pour la détection de serveur invité. | Type de système d’exploitation non pris en charge pour la détection de serveur invité. | Les types de système d’exploitation pris en charge sont Windows et Linux uniquement. |
| 9006 : L’URL permettant de télécharger le fichier de métadonnées à partir de l’invité est vide. | Cela peut se produire si l’agent de détection ne fonctionne pas correctement. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support technique Microsoft. |
| 9007 : Le processus exécutant la tâche de détection dans le serveur invité est introuvable. | Cela peut se produire si l’agent de découverte ne fonctionne pas correctement. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support technique Microsoft. |
| 9008 : Impossible de récupérer l’état du processus de serveur invité. | Ce problème peut se produire en raison d’une erreur interne. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support technique Microsoft. |
| 9009 : Le contrôle de compte d'utilisateur Windows a empêché l’exécution de la tâche de détection sur le serveur. | Les paramètres de contrôle de compte d’utilisateur (UAC) Windows sur le serveur sont restrictifs et empêchent la détection de l’inventaire logiciel installé. | Dans les paramètres « Contrôle de compte d’utilisateur » sur le serveur, configurez le paramètre UAC du serveur sur l’un des deux niveaux inférieurs. |
| 9010 : Le serveur est hors tension. | Le serveur est hors tension. | Vérifiez que le serveur est sous tension. |
| 9011 : Fichier de métadonnées détecté introuvable dans le système de fichiers du serveur invité. | Ce problème peut se produire en raison d’une erreur interne. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support technique Microsoft. |
| 9012 : Le fichier de métadonnées détecté est vide. | Ce problème peut se produire en raison d’une erreur interne. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support technique Microsoft. |
| 9013 : Un nouveau profil temporaire est créé pour chaque connexion. | Un nouveau profil temporaire est créé pour chaque connexion au serveur sur VMware. | Contactez le Support Microsoft pour la résoudre. |
| 9014 : Impossible de récupérer les métadonnées du système de fichiers du serveur invité. | Aucune connectivité à l’hôte ESXi | Vérifiez que l’appliance peut se connecter au port 443 sur l’hôte ESXi qui exécute le serveur. |
| 9015 : Le rôle Opérations invité n’est pas activé sur le compte d’utilisateur vCenter. | Le rôle Opérations Guest n’est pas activé sur le compte d’utilisateur vCenter. | Vérifiez que le rôle Opérations invité est activé sur le compte d’utilisateur vCenter. |
| 9016 : Détection impossible parce que l’agent des opérations invité est obsolète. | Les outils VMware ne sont pas installés correctement ou ne sont pas à jour. | Assurez-vous que les outils VMware sont correctement installés et à jour. |
| 9017 : Le fichier contenant les métadonnées découvertes est introuvable sur le serveur. | Ce problème peut se produire en raison d’une erreur interne. | Contactez le Support Microsoft pour la résoudre. |
| 9018 : PowerShell n’est pas installé sur les serveurs invités. | PowerShell n’est pas disponible sur le serveur invité. | Installez PowerShell sur le serveur invité. |
| 9019 : Détection impossible en raison d’échecs d’opérations du serveur invité. | Échec de l’opération de l’invité VMware sur le serveur. | Vérifiez que les informations d’identification du serveur sont valides et que le nom d’utilisateur fourni dans les informations d’identification du serveur invité est au format UPN. |
| 9020 : L’autorisation de création de fichier est refusée. | Le rôle associé à l’utilisateur ou à la stratégie de groupe empêche l’utilisateur de créer le fichier dans le dossier | Vérifiez si l’utilisateur invité fourni dispose de l’autorisation de créer le fichier dans le dossier. Consultez **Notifications** dans Azure Migrate : Découverte et évaluation pour connaître le nom du dossier. |
| 9021 : Impossible de créer le fichier dans le chemin d’accès System Temp. | VMware Tools signale System Temp au lieu du chemin d’accès Users Temp. | Mettez à niveau votre outil VMware vers une version supérieure à 10287 (format client NGC/VI). |
| 9022 : L’accès à l’objet WMI est refusé. | Le rôle associé à l’utilisateur ou à la politique de groupe restreint l’accès de l’utilisateur à l’objet WMI. | Contactez le support Microsoft. |
| 9023 : Impossible d’exécuter PowerShell, car la valeur de la variable d’environnement SystemRoot est vide. | La valeur de la variable d’environnement SystemRoot est vide pour le serveur invité. | Contactez le Support Microsoft pour la résoudre. |
| 9024 : Impossible de détecter si la valeur de la variable d’environnement TEMP est vide. | La valeur de la variable d’environnement TEMP est vide pour le serveur invité. | Contactez le support Microsoft. |
| 9025 : PowerShell est endommagé sur les serveurs invités. | PowerShell est endommagé sur le serveur invité. | Réinstallez PowerShell sur le serveur invité et vérifiez si PowerShell peut y être exécuté. |
| 9026 : Impossible d’exécuter les opérations d’invité sur le serveur. | L’état du serveur n’autorise pas l’exécution des opérations d’invité sur le serveur. | Contactez le Support Microsoft pour la résoudre. |
| 9027 : L’agent des opérations d’invité n’est pas en cours d’exécution sur le serveur. | Impossible de contacter l’agent des opérations d’invité en cours d’exécution dans le serveur virtuel. | Contactez le Support Microsoft pour la résoudre. |
| 9028 : Impossible de créer le fichier en raison d’un stockage sur disque insuffisant dans le serveur. | Espace insuffisant sur le disque | Assurez-vous que l’espace disponible est suffisant dans le stockage sur disque du serveur. |
| 9029 : Aucun accès à PowerShell sur les informations d’identification du serveur invité qui ont été fournies. | L’accès à PowerShell n’est pas disponible pour l’utilisateur. | Vérifiez que l’utilisateur ajouté sur l’appliance peut accéder à PowerShell sur le serveur invité. |
| 9030 : Impossible de collecter les métadonnées détectées, car l’hôte ESXi est déconnecté. | L’hôte ESXi est dans un état déconnecté. | Vérifiez que l’hôte ESXi exécutant le serveur est connecté. |
| 9031 : Impossible de collecter les métadonnées détectées, car l’hôte ESXi ne répond pas. | L’hôte distant n’est pas dans un état valide. | Vérifiez que l’hôte ESXi exécutant le serveur est exécuté et connecté. |
| 9032 : Détection impossible en raison d’une erreur interne. | Ce problème peut se produire en raison d’une erreur interne. | Contactez le Support Microsoft pour la résoudre. |
| 9033 : Détection impossible, car le nom d’utilisateur du serveur contient des caractères non valides. | Des caractères non valides ont été détectés dans le nom d’utilisateur. | Fournissez à nouveau les informations d’identification du serveur en vérifiant qu’il n’y a pas de caractères non valides. |
| 9034 : Le nom d’utilisateur fourni n’est pas au format UPN. | Le nom d’utilisateur n’est pas au format UPN. | Assurez-vous que le nom d’utilisateur est au format User Principal Name (UPN). |
| 9035 : Impossible de détecter si le mode de langage PowerShell n’est pas défini sur « Full Language » (Langage complet). | Le mode de langage pour PowerShell dans le serveur invité n’est pas défini sur « Full Language » (Langage complet). | Assurez-vous que le mode de langage PowerShell est défini sur « Full Language » (Langage complet). |
| 9037 : La collecte de données a été suspendue temporairement, car le temps de réponse du serveur est trop élevé. | Le serveur détecté prend trop de temps pour répondre | Aucune action requise. Une nouvelle tentative sera effectuée dans 24 heures pour la détection de l’inventaire logiciel et 3 heures pour l’analyse des dépendances (sans agent). |
| 10000 : Le type de système d'exploitation n'est pas pris en charge. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Les types de système d’exploitation pris en charge sont Windows et Linux uniquement. |
| 10001 : Le script pour la détection de serveur est introuvable sur l’appliance. | Le bot de détection ne fonctionne pas comme prévu. | Contactez le Support Microsoft pour la résoudre. |
| 10002 : La tâche de détection n’a pas été terminée dans les temps. | L’agent de détection ne fonctionne pas comme prévu. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support technique Microsoft. |
| 10003 : Le processus d’exécution de la tâche de détection a rencontré une erreur. | Le processus d’exécution de la tâche de détection a rencontré une erreur. | Le problème devrait être résolu automatiquement dans les 24 heures. Si le problème persiste, contactez le support Microsoft. |
| 10004 : Informations d'identification non fournies pour le type de système d'exploitation invité. | Les informations d’identification permettant d’accéder aux serveurs de ce type de système d’exploitation n’ont pas été fournies dans l’appliance Azure Migrate. | Ajouter des informations d’identification pour les serveurs sur l’appliance |
| 10005 : Les informations d'identification fournies ne sont pas valides. | Les informations d’identification fournies pour que l’appliance accède au serveur sont incorrectes. | Mettez à jour les informations d’identification fournies dans l’appliance et vérifiez que le serveur est accessible avec ces informations d’identification. |
| 10006 : Type de système d’exploitation invité non pris en charge par la banque d’informations d’identification. | Le système d’exploitation en cours d’exécution sur le serveur n’est ni Windows, ni Linux. | Les types de système d’exploitation pris en charge sont Windows et Linux uniquement. |
| 10007 : Impossible de traiter les métadonnées récupérées. | Cette erreur s’est produite lors de la tentative de désérialisation du JSON. | Contactez le Support Microsoft pour la résoudre. |
| 10008 : Impossible de créer un fichier sur le serveur. | Ce problème peut se produire en raison d’une erreur interne. | Contactez le Support Microsoft pour la résoudre. |
| 10009 : Impossible d’écrire les métadonnées détectées dans un fichier sur le serveur. | Ce problème peut se produire en raison d’une erreur interne. | Contactez le Support Microsoft pour la résoudre. |

## <a name="next-steps"></a>Étapes suivantes

Configurez une appliance pour [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) ou des [serveurs physiques](how-to-set-up-appliance-physical.md).
