---
title: Résoudre les erreurs d’installation Push du service Mobilité lors de l’activation de la réplication dans le cadre d’une récupération d’urgence | Microsoft Docs
description: Résoudre les erreurs d’installation des services Mobilité lors de l’activation de la réplication dans le cadre d’une récupération d’urgence
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 01/18/2019
ms.openlocfilehash: e397540d33df8a509e10f52fde41fc178cdba67e
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411745"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Résoudre les problèmes d’installation Push du service Mobilité

L’installation du service Mobilité est une étape clé de l’activation de la réplication. La réussite de cette étape dépend uniquement de la satisfaction des conditions préalables et de l’utilisation des configurations prises en charge. Les erreurs les plus courantes que vous rencontrez au cours de l’installation du service Mobilité sont dues aux :

* Erreurs d’informations d’identification/de privilèges
* Échecs de connexion
* Erreurs de connectivité
* Systèmes d’exploitation non pris en charge
* Échecs d’installation VSS

Lorsque vous activez la réplication, Azure Site Recovery tente une installation Push de l’agent du service Mobilité sur votre machine virtuelle. Dans le cadre de cette installation, le serveur de configuration essaie de se connecter à la machine virtuelle et de copier l’agent. Pour réussir l’installation, suivez les instructions de dépannage étape par étape ci-dessous.

## <a name="credentials-check-errorid-95107--95108"></a>Vérification des informations d’identification (ErrorID : 95107 & 95108)

* Vérifiez si le compte d’utilisateur choisi lors de l’activation de la réplication est **valide et précis**.
* Azure Site Recovery requiert le compte **ROOT** ou le compte d’utilisateur avec des **privilèges d’administrateur** pour effectuer l’installation Push. Autrement, l’installation Push sera bloquée sur la machine source.
  * Pour Windows (**erreur 95107**), vérifiez si le compte d’utilisateur a un accès administratif, local ou de domaine, à la machine source.
  * Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur l’ordinateur local.
    * Pour ce faire, sous la clé de Registre HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez un nouveau DWORD : LocalAccountTokenFilterPolicy. Définissez la valeur sur 1. Pour exécuter cette étape, exécutez la commande suivante depuis l’invite de commande :

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Pour Linux (**erreur 95108**), vous devez choisir le compte racine pour une installation réussie de l’agent de mobilité. De plus, les services SFTP doivent être en cours d’exécution. Pour activer l’authentification par mot de passe et le sous-système SFTP dans le fichier sshd_config :
    1. Connectez-vous en tant qu’utilisateur racine.
    2. Accédez au fichier /etc/ssh/sshd_config, et recherchez la ligne commençant par PasswordAuthentication.
    3. Supprimez les marques de commentaire de la ligne et remplacez la valeur actuelle par yes.
    4. Recherchez la ligne commençant par Subsystem, puis supprimez les marques de commentaire de la ligne.
    5. Redémarrez le service sshd.

Si vous souhaitez modifier les informations d’identification du compte d’utilisateur choisi, suivez les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Échec en raison de privilèges insuffisants (ErrorID : 95517)

Lorsque l’utilisateur choisi pour installer l’agent de mobilité n’a pas les privilèges d’administrateur, le serveur de configuration/serveur de processus de scale-out ne sera pas autorisé à copier le logiciel agent de mobilité sur la machine source. Par conséquent, cette erreur est un échec de type Accès refusé. Assurez-vous que le compte d’utilisateur dispose des privilèges d’administrateur.

Si vous souhaitez modifier les informations d’identification du compte d’utilisateur choisi, suivez les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Échec en raison de privilèges insuffisants (ErrorID : 95518)

Si l’établissement d’une relation d’approbation de domaine entre le domaine primaire et la station de travail échoue alors que vous essayez de vous connecter à la machine source, l’installation de l’agent de mobilité échoue avec l’erreur id 95518. Ainsi, assurez-vous que le compte d’utilisateur employé pour installer l’agent de mobilité possède les privilèges d’administration pour se connecter via le domaine primaire de la machine source.

Si vous souhaitez modifier les informations d’identification du compte d’utilisateur choisi, suivez les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failure-errorid-95519"></a>Échec de connexion (ErrorID : 95519)

Le compte d’utilisateur choisi au cours de Activer la réplication est désactivé. Pour activer le compte utilisateur, reportez-vous à l’article [ici](https://aka.ms/enable_login_user) ou exécutez la commande suivante en remplaçant le texte *username* par le nom d’utilisateur réel.
`net user 'username' /active:yes`

## <a name="login-failure-errorid-95520"></a>Échec de connexion (ErrorID : 95520)

Plusieurs échecs de nouvelles tentatives d’accès à la machine ont pour effet de verrouiller le compte d’utilisateur. L’échec peut être dû à ce qui suit :

* Les informations d’identification fournies lors de l’installation de Configuration sont incorrectes ou
* Le compte d’utilisateur choisi au cours de Activer la réplication est incorrect

Par conséquent, modifiez les informations d’identification choisies en suivant les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) et recommencez l’opération un peu plus tard.

## <a name="login-failure-errorid-95521"></a>Échec de connexion (ErrorID : 95521)

Cette erreur se produit lorsque les serveurs d’ouverture de session ne sont pas disponibles sur la machine source. L’indisponibilité des serveurs d’ouverture de session entraînera l’échec de la demande de connexion et l’agent de mobilité ne pourra donc pas être installé. Pour vous connecter, vérifiez que les serveurs d’ouverture de session sont disponibles sur la machine source et démarrez le service d’ouverture de session. Pour obtenir des instructions détaillées, cliquez [ici](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available).

## <a name="login-failure-errorid-95522"></a>Échec de connexion (ErrorID : 95522)

Le service de connexion n’est pas en cours d’exécution sur votre ordinateur source et a provoqué l’échec de la demande de connexion. Par conséquent, l’agent de mobilité ne peut pas être installé. Pour résoudre le problème, assurez-vous que le service d’ouverture de session fonctionne sur la machine source de sorte que la connexion réussisse. Pour démarrer le service d’ouverture de session, exécutez la commande « net start Logon » à partir de l’invite de commandes ou démarrez le service « NetLogon » à partir du Gestionnaire des tâches.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Échec de connectivité (ErrorID : 95117 & 97118)**

Le serveur de configuration/serveur de processus scale-out tente de se connecter à la machine virtuelle source pour installer l’agent Mobility. Cette erreur se produit lors de la machine source n’est pas accessible en raison de problèmes de connectivité réseau. Pour résoudre,

* Vérifiez que vous êtes en mesure d’effectuer un test ping sur votre machine source à partir du serveur de configuration. Si vous avez choisi un serveur de traitement avec évolution pour l’activation de réplication, assurez-vous de pouvoir effectuer un test ping sur votre machine source à partir du serveur de processus.
  * À partir de la ligne de commande du serveur source, utilisez Telnet pour effectuer un test Ping sur le serveur de configuration/de processus de scale-out avec le port https (135) comme indiqué ci-dessous. Cela vous indiquera s’il existe des problèmes de connectivité réseau ou des problèmes de blocage de port par le pare-feu.

     `telnet <CS/ scale-out PS IP address> <135>`
* Mais également, pour la **machine virtuelle Linux**,
  * Vérifiez que les derniers packages openssl, openssh-server et openssh sont installés.
  * Vérifiez que le protocole Secure Shell (SSH) est activé et s’exécute sur le port 22.
  * Les services SFTP doivent être en cours d’exécution. Pour activer l’authentification par mot de passe et le sous-système SFTP dans le fichier sshd_config,
    * Connectez-vous en tant qu’utilisateur racine.
    * Accédez au fichier /etc/ssh/sshd_config, et recherchez la ligne commençant par PasswordAuthentication.
    * Supprimez les marques de commentaire de la ligne et remplacez la valeur actuelle par yes
    * Recherchez la ligne commençant par Subsystem, puis supprimez les marques de commentaire de la ligne
    * Redémarrez le service sshd.
* Une tentative de connexion peut échouer si aucune réponse correcte n’est fournie après une période déterminée, ou la connexion établie échoue car l’hôte connecté n’a pas répondu.
* Il peut s’agir d’un problème lié au domaine, au réseau ou à la connectivité. Cela peut également être dû à un problème de résolution de nom DNS ou d’insuffisante de ports TCP. Vérifiez s’il existe des problèmes connus dans votre domaine.

## <a name="connectivity-failure-errorid-95523"></a>Échec de connectivité (ErrorID : 95523)

Cette erreur se produit lorsque le réseau dans lequel réside la machine source n’est pas trouvé ou pourrait avoir été supprimé ou n’est plus disponible. La seule façon de résoudre l’erreur est de s’assurer que le réseau existe.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Vérification des services de partage de fichiers et d’imprimantes (ErrorID : 95105 & 95106)

Après une vérification de la connectivité, vérifiez si le service de partage de fichiers et d’imprimantes est activé sur votre machine virtuelle. Ces paramètres sont requis pour copier l’agent Mobility sur la machine source.

Pour **Windows 2008 R2 et les versions antérieures**,

* Pour activer le partage de fichiers et d’impression via le Pare-feu Windows,
  * Ouvrez le panneau de contrôle -> Système et sécurité -> Pare-feu Windows -> sur le volet gauche, cliquez sur Paramètres avancés -> cliquez sur Règles de trafic entrant dans l’arborescence de la console.
  * Recherchez les règles de partage de fichiers et d’imprimantes (NB-Session-In) et de partage de fichiers et d’imprimantes (SMB-In). Pour chaque règle, cliquez avec le bouton droit sur la règle, puis sur **Activer la règle**.
* Pour activer le partage de fichiers avec la stratégie de groupe,
  * Accédez au menu Démarrer, tapez gpmc.msc et recherchez.
  * Dans le volet de navigation, ouvrez les dossiers suivants : Stratégie ordinateur local, Configuration utilisateur, Modèles d’administration, Composants Windows et Partage réseau.
  * Dans le volet d’informations, double-cliquez sur **Prevent users from sharing files within their profile** (Empêcher les utilisateurs de partager des fichiers dans leur profil). Pour désactiver le paramètre de stratégie de groupe et activer la capacité d’utilisateur à partager des fichiers, cliquez sur Désactivé. Cliquez sur OK pour enregistrer vos modifications. Cliquez [ici](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))pour en savoir plus.

Pour **les versions ultérieures**, suivez les instructions fournies [ici](vmware-azure-install-mobility-service.md) pour activer le partage de fichiers et d’imprimantes.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Vérification de la configuration de Windows Management Instrumentation (WMI) (code erreur : 95103)

Après avoir vérifié les services de fichiers et d’imprimantes, activez le service WMI pour les profils privés, publics et de domaine via le pare-feu. Ces paramètres sont requis pour terminer l’exécution à distance sur la machine source. Pour activer,

* Accédez au panneau de configuration, cliquez sur Sécurité, puis sur Pare-feu Windows.
* Cliquez sur Modifier les paramètres, puis sur l’onglet Exceptions.
* Dans la fenêtre Exceptions, sélectionnez la case à cocher de Windows Management Instrumentation (WMI) pour activer le trafic WMI à travers le pare-feu. 

Vous pouvez également activer le trafic WMI via le pare-feu à l’invite de commandes. Utilisez la commande `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` suivante
Vous pouvez trouver d’autres articles concernant la résolution des problèmes WMI dans les articles suivants.

* [Test de base WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Résolution des problèmes WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Résolution des problèmes liés aux scripts et aux services WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Systèmes d’exploitation non pris en charge

Un échec peut aussi être dû à un système d’exploitation non pris en charge. Vérifiez que vous êtes sur la version du noyau/du système d’exploitation prise en charge pour réussir l’installation du service Mobilité. Évitez l’utilisation des correctifs privés.
Pour voir la liste des systèmes d’exploitation et versions du noyau pris en charge par Azure Site Recovery, reportez-vous à notre [document avec la matrice de prise en charge](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Les partitions / volumes de démarrage et système ne sont pas le même disque (ErrorID : 95309)

Avant la version 9.20, les volumes/partitions de démarrage et système résidant sur des disques n’étaient pas pris en charge. À partir de la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), cette configuration est prise en charge. Utilisez la dernière version de ce support.

## <a name="boot-disk-not-found-errorid-95310"></a>Disque de démarrage introuvable (ErrorID : 95310)

Une machine virtuelle sans disque de démarrage ne peut pas être protégée. L’objectif est de garantir une récupération fluide de la machine virtuelle durant une opération de basculement. L’absence de disque de démarrage occasionne l’échec du démarrage de la machine après le basculement. Vérifiez que la machine virtuelle contient un disque de démarrage et recommencez l’opération. Notez également que les machines comprenant plusieurs disques de démarrage ne sont pas prises en charge.

## <a name="multiple-boot-disks-found-errorid-95311"></a>Plusieurs disques de démarrage trouvés (ErrorID : 95311)

Une machine virtuelle dotée de plusieurs disques de démarrage ne constitue pas une [configuration prise en charge](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partition système sur plusieurs disques (ErrorID : 95313)

Avant la version 9.20, le logiciel ne prenait pas en charge la partition racine ou un volume placé sur plusieurs disques. À partir de la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), cette configuration est prise en charge. Utilisez la dernière version de ce support.

## <a name="grub-uuid-failure-errorid-95320"></a>GRUB UUID failure (ErrorID: 95320)

Si le GRUB de la machine source utilise le nom de l’appareil au lieu de l’UUID, l’installation de l’agent de mobilité échoue. Adressez-vous à l’administrateur système pour qu’il apporte les modifications nécessaires au fichier GRUB.

## <a name="lvm-support-from-920-version"></a>Prise en charge de LVM à partir de la version 9.20

Avant la version 9.20, LVM était pris en charge pour les disques de données uniquement. /boot doit se trouver sur une partition de disque et ne doit pas être un volume LVM.

À partir de la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), le [Disque du système d’exploitation sur LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) est pris en charge. Utilisez la dernière version de ce support.

## <a name="insufficient-space-errorid-95524"></a>Espace insuffisant (ErrorID : 95524)

Lorsque l’agent Mobility est copié sur la machine source, au moins 100 Mo d’espace libre sont nécessaires. Donc, assurez-vous que votre machine source a besoin d’espace libre et réessayez l’opération.

## <a name="vss-installation-failures"></a>Échecs d’installation VSS

L’installation VSS fait partie de l’installation de l’agent Mobilité. Ce service est utilisé dans le processus de génération de points de récupération cohérents d’application. Les échecs lors de l’installation VSS peuvent avoir diverses raisons. Pour identifier les erreurs exactes, reportez-vous à **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Certaines erreurs courantes et leur procédure de résolution sont mises en évidence dans la section suivante.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erreur VSS -2147023170 [0x800706BE] - code de sortie 511

Ce problème se produit généralement quand un logiciel antivirus bloque le fonctionnement des services Azure Site Recovery. Pour le résoudre,

1. Excluez tous les dossiers mentionnés [ici](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Suivez les recommandations publiées par votre fournisseur d’antivirus pour débloquer l’inscription des DLL dans Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erreur VSS 7 [0x7] - code de sortie 511

Il s’agit d’une erreur d’exécution due à une mémoire insuffisante pour installer VSS. Veillez à augmenter l’espace disque pour la réussite de cette opération.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erreur VSS -2147023824 [0x80070430] - code de sortie 517

Cette erreur se produit lorsque le service fournisseur VSS d’Azure Site Recovery est [marqué pour suppression](https://msdn.microsoft.com/en-us/library/ms838153.aspx). Essayez d’installer manuellement VSS sur la machine source en exécutant la ligne de commande suivante

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erreur VSS -2147023841 [0x8007041F] - code de sortie 512

Cette erreur se produit lorsque la base de données de service fournisseur VSS d’Azure Site Recovery est [verrouillée](https://msdn.microsoft.com/en-us/library/ms833798.aspx). Essayez d’installer manuellement VSS sur la machine source en exécutant la ligne de commande suivante

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>Code de sortie VSS 806

Cette erreur se produit lorsque le compte d’utilisateur utilisé pour l’installation n’a pas d’autorisations pour exécuter la commande CSScript. Fournissez les autorisations nécessaires pour le compte d’utilisateur afin d’exécuter le script, et recommencez l’opération.

### <a name="other-vss-errors"></a>Autres erreurs VSS

Essayez d’installer manuellement le service fournisseur VSS sur la machine source en exécutant la ligne de commande suivante

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](vmware-azure-tutorial.md) configurer la récupération d’urgence de machines virtuelles VMware.
