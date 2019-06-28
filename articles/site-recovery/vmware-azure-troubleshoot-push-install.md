---
title: Résoudre les erreurs d’installation Push du service Mobilité lors de l’activation de la réplication dans le cadre d’une récupération d’urgence | Microsoft Docs
description: Résoudre les erreurs d’installation des services Mobilité lors de l’activation de la réplication dans le cadre d’une récupération d’urgence
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 58c09c71aad2b6244f6e2f3d144c033665932f50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925577"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Résoudre les problèmes d’installation Push du service Mobilité

L’installation du service Mobilité est une étape clé de l’activation de la réplication. La réussite de cette étape dépend uniquement de la satisfaction des conditions préalables et de l’utilisation des configurations prises en charge. Les erreurs les plus courantes que vous rencontrez au cours de l’installation du service Mobilité sont dues aux :

* [Erreurs d’informations d’identification/de privilèges](#credentials-check-errorid-95107--95108)
* [Échecs de connexion](#login-failures-errorid-95519-95520-95521-95522)
* [Erreurs de connectivité](#connectivity-failure-errorid-95117--97118)
* [Erreurs de partage de fichiers et d’imprimantes](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Échecs WMI](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Systèmes d’exploitation non pris en charge](#unsupported-operating-systems)
* [Configurations de démarrage non prises en charge](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Échecs d’installation VSS](#vss-installation-failures)
* [Nom de l’appareil dans la configuration GRUB au lieu de l’UUID d’appareil](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume LVM](#lvm-support-from-920-version)
* [Avertissements de redémarrage](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

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

Si l’établissement d’une relation d’approbation de domaine entre le domaine primaire et la station de travail échoue alors que vous essayez de vous connecter à la machine source, l’installation de l’agent de mobilité échoue avec l’erreur ID 95518. Ainsi, assurez-vous que le compte d’utilisateur employé pour installer l’agent de mobilité possède les privilèges d’administration pour se connecter via le domaine primaire de la machine source.

Si vous souhaitez modifier les informations d’identification du compte d’utilisateur choisi, suivez les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Échecs de connexion (ID d’erreur : 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Les informations d’identification du compte d’utilisateur ont été désactivées (ID d’erreur : 95519)

Le compte d’utilisateur choisi au cours de Activer la réplication est désactivé. Pour activer le compte utilisateur, reportez-vous à l’article [ici](https://aka.ms/enable_login_user) ou exécutez la commande suivante en remplaçant le texte *username* par le nom d’utilisateur réel.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Les informations d’identification ont été verrouillées en raison de l’échec de plusieurs tentatives de connexion (ID d’erreur : 95520)

Plusieurs échecs de nouvelles tentatives d’accès à la machine ont pour effet de verrouiller le compte d’utilisateur. L’échec peut être dû à ce qui suit :

* Les informations d’identification fournies lors de l’installation de Configuration sont incorrectes ou
* Le compte d’utilisateur choisi au cours de Activer la réplication est incorrect

Par conséquent, modifiez les informations d’identification choisies en suivant les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) et recommencez l’opération un peu plus tard.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Les serveurs d’ouverture de session ne sont pas disponibles sur la machine source (ID d’erreur : 95521)

Cette erreur se produit lorsque les serveurs d’ouverture de session ne sont pas disponibles sur la machine source. L’indisponibilité des serveurs d’ouverture de session entraînera l’échec de la demande de connexion et l’agent de mobilité ne pourra donc pas être installé. Pour vous connecter, vérifiez que les serveurs d’ouverture de session sont disponibles sur la machine source et démarrez le service d’ouverture de session. Pour obtenir des instructions détaillées, consultez l’article KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) : Aucun serveur d’accès n’est actuellement disponible pour traiter la demande d’ouverture de session.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Le service d’ouverture de session ne s’exécute pas sur l’ordinateur source (ID d’erreur : 95522)

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
  * Dans le volet d’informations, double-cliquez sur **Prevent users from sharing files within their profile** (Empêcher les utilisateurs de partager des fichiers dans leur profil). Pour désactiver le paramètre de stratégie de groupe et activer la capacité d’utilisateur à partager des fichiers, cliquez sur Désactivé. Cliquez sur OK pour enregistrer vos modifications. Pour en savoir plus, consultez [Activer ou désactiver le partage de fichiers avec la stratégie de groupe](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Pour les **versions ultérieures**, suivez les instructions fournies dans [Installer le service Mobilité pour la reprise d’activité de machines virtuelles VMware et serveurs physiques](vmware-azure-install-mobility-service.md) pour activer le partage de fichiers et d’imprimantes.

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

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurations de disque de démarrage non prises en charge (ID d’erreur : 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Les partitions / volumes de démarrage et système ne sont pas le même disque (ErrorID : 95309)

Avant la version 9.20, les volumes/partitions de démarrage et système résidant sur des disques n’étaient pas pris en charge. À partir de la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), cette configuration est prise en charge. Utilisez la dernière version de ce support.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Le disque de démarrage n’est pas disponible (ID d’erreur : 95310)

Une machine virtuelle sans disque de démarrage ne peut pas être protégée. L’objectif est de garantir une récupération fluide de la machine virtuelle durant une opération de basculement. L’absence de disque de démarrage occasionne l’échec du démarrage de la machine après le basculement. Vérifiez que la machine virtuelle contient un disque de démarrage et recommencez l’opération. Notez également que les machines comprenant plusieurs disques de démarrage ne sont pas prises en charge.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Plusieurs disques de démarrage sont présents sur l’ordinateur source (ID d’erreur : 95311)

Une machine virtuelle dotée de plusieurs disques de démarrage ne constitue pas une [configuration prise en charge](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partition système sur plusieurs disques (ErrorID : 95313)

Avant la version 9.20, le logiciel ne prenait pas en charge la partition racine ou un volume placé sur plusieurs disques. À partir de la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), cette configuration est prise en charge. Utilisez la dernière version de ce support.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>L'activation de la protection a échoué, car le nom de l'appareil est mentionné dans la configuration GRUB au lieu de l'UUID (ID d'erreur : 95320)

**Cause possible :** </br>
Les fichiers de configuration GRUB (« /boot/grub/menu.lst », « /boot/grub/grub.cfg », « /boot/grub2/grub.cfg » ou « /etc/default/grub ») peuvent contenir la valeur des paramètres **root** et **resume** en tant que noms d'appareils en lieu et place de l'UUID. Site Recovery impose l'approche UUID car le nom de l'appareil peut changer lors du redémarrage de la machine virtuelle. Et si la machine virtuelle n'apparaît pas sous le même nom lors du basculement, des problèmes peuvent survenir. Par exemple : </br>


- La ligne suivante provient du fichier GRUB **/boot/grub2/grub.cfg**. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- La ligne suivante provient du fichier GRUB **/boot/grub/menu.lst**
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

Si vous observez la chaîne en gras ci-dessus, cela signifie que GRUB dispose du nom réel des appareils comme paramètres « root » et « resume » au lieu de l'UUID.
 
**Procédure de résolution :**<br>
Le nom de chaque appareil doit être remplacé par l'UUID correspondante.<br>


1. Recherchez l’UUID de l’appareil en exécutant la commande « blkid \<nom_appareil> ». Par exemple :<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Remplacez maintenant le nom de l’appareil par son UUID dans un format du type « root=UUID=\<UUID> ». Par exemple, si l’on remplace le nom des appareils par l’UUID pour les paramètres root et resume mentionnés plus haut dans les fichiers « /boot/grub2/grub.cfg », « /boot/grub2/grub.cfg » ou « /etc/default/grub », les lignes des fichiers se présentent ainsi. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. Redémarrez à nouveau la protection.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installation du service Mobilité terminée avec avertissement de redémarrage (ID d’erreur : 95265, 95266)

Le service Mobilité Site Recovery a de nombreux composants, et notamment le pilote de filtre. Le pilote de filtre ne se charge dans la mémoire système qu’au moment du redémarrage du système. Cela signifie que seul le chargement d’un nouveau pilote de filtre, qui se produit seulement lors du redémarrage système, permet d’apporter des correctifs au pilote de filtre.

**Veuillez noter** qu’il s’agit d’un avertissement ; la réplication existante fonctionnera toujours après la nouvelle mise à jour de l’agent. Vous pouvez choisir de redémarrer chaque fois que vous souhaitez profiter des avantages du nouveau pilote de filtre ; en l’absence de redémarrage, l’ancien pilote de filtre continue de fonctionner. Après une mise à jour sans redémarrage, hormis le pilote de filtre, **les autres améliorations et correctifs du service Mobilité offrent donc tous leurs avantages**. Par conséquent, il est recommandé mais non obligatoire de redémarrer après chaque mise à niveau. Pour savoir quand un redémarrage est obligatoire, consultez la section [Redémarrage de la machine source après la mise à niveau de l’agent de mobilité](https://aka.ms/v2a_asr_reboot) des mises à jour de service dans Azure Site Recovery.

> [!TIP]
>Pour connaître la meilleure pratique de planification des mises à niveau pendant votre fenêtre de maintenance, consultez [Prise en charge des dernières versions du système d’exploitation/noyau](https://aka.ms/v2a_asr_upgrade_practice) dans les mises à jour de service dans Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>Prise en charge de LVM à partir de la version 9.20

Avant la version 9.20, LVM était pris en charge pour les disques de données uniquement. /boot doit se trouver sur une partition de disque et ne doit pas être un volume LVM.

À partir de la [version 9.20](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery), le [Disque du système d’exploitation sur LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) est pris en charge. Utilisez la dernière version de ce support.

## <a name="insufficient-space-errorid-95524"></a>Espace insuffisant (ErrorID : 95524)

Lorsque l’agent Mobility est copié sur la machine source, au moins 100 Mo d’espace libre sont nécessaires. Donc, assurez-vous que votre machine source a besoin d’espace libre et réessayez l’opération.

## <a name="vss-installation-failures"></a>Échecs d’installation VSS

L’installation VSS fait partie de l’installation de l’agent Mobilité. Ce service est utilisé dans le processus de génération de points de récupération cohérents d’application. Les échecs lors de l’installation VSS peuvent avoir diverses raisons. Pour identifier les erreurs exactes, reportez-vous à **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**. Certaines erreurs courantes et leur procédure de résolution sont mises en évidence dans la section suivante.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erreur VSS -2147023170 [0x800706BE] - code de sortie 511

Ce problème se produit généralement lorsqu’un logiciel antivirus bloque le fonctionnement des services Azure Site Recovery. Pour résoudre ce problème :

1. Excluez tous les dossiers mentionnés [ici](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
2. Suivez les recommandations publiées par votre fournisseur d’antivirus pour débloquer l’inscription des DLL dans Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erreur VSS 7 [0x7] - code de sortie 511

Il s’agit d’une erreur d’exécution due à une mémoire insuffisante pour installer VSS. Veillez à augmenter l’espace disque pour la réussite de cette opération.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erreur VSS -2147023824 [0x80070430] - code de sortie 517

Cette erreur se produit lorsque le service fournisseur VSS d’Azure Site Recovery est [marqué pour suppression](https://msdn.microsoft.com/library/ms838153.aspx). Essayez d’installer manuellement VSS sur la machine source en exécutant la ligne de commande suivante

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erreur VSS -2147023841 [0x8007041F] - code de sortie 512

Cette erreur se produit lorsque la base de données de service fournisseur VSS d’Azure Site Recovery est [verrouillée](https://msdn.microsoft.com/library/ms833798.aspx). Essayez d’installer manuellement VSS sur la machine source en exécutant la ligne de commande suivante

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>Code de sortie VSS 806

Cette erreur se produit lorsque le compte d’utilisateur utilisé pour l’installation n’a pas d’autorisations pour exécuter la commande CSScript. Fournissez les autorisations nécessaires pour le compte d’utilisateur afin d’exécuter le script, et recommencez l’opération.

### <a name="other-vss-errors"></a>Autres erreurs VSS

Essayez d’installer manuellement le service fournisseur VSS sur la machine source en exécutant la ligne de commande suivante

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>Erreur VSS - 0x8004E00F

Cette erreur se produit généralement pendant l’installation de l’agent de mobilité en raison de problèmes liés à DCOM, tels que DCOM est dans un état critique.

Utilisez la procédure suivante pour déterminer la cause de l’erreur.

**Examiner les journaux d’activité d’installation**

1. Ouvrez le journal d’activité d’installation situé dans c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
2. La présence de l’erreur suivante indique ce problème :

    Unregistering the existing application... (Désinscription de l’application existante...)  Create the catalogue object (Créer l’objet de catalogue) Get the collection of Applications (Obtenir la collection d’applications) 

    ERROR:

    - Code d’erreur : -2147164145 [0x8004E00F]
    - Code de sortie : 802

Pour résoudre le problème :

Contactez l’[équipe de la plateforme Microsoft Windows](https://aka.ms/Windows_Support) pour obtenir de l’aide dans la résolution du problème DCOM.

Lorsque le problème DCOM est résolu, réinstallez manuellement le fournisseur VSS d’Azure Site Recovery à l’aide de la commande suivante :
 
**C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
Si la cohérence des applications n’est pas critique pour vos besoins de récupération d’urgence, vous pouvez contourner l’installation du fournisseur VSS. 

Pour contourner l’installation du fournisseur VSS d’Azure Site Recovery et installer manuellement le fournisseur VSS d’Azure Site Recovery après l’installation :

1. Installez le service Mobilité. 
   > [!Note]
   > 
   > L’installation échoue à l’étape de « configuration post-installation ». 
2. Pour contourner l’installation de VSS :
   1. Ouvrez le répertoire d’installation de Mobility Service d’Azure Site Recovery situé dans :
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. Modifiez les scripts d’installation du fournisseur VSS d’Azure Site Recovery **nMageVSSProvider_Install** et **InMageVSSProvider_Uninstall.cmd** pour qu’ils réussissent toujours en ajoutant les lignes suivantes :
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Réexécutez manuellement l’installation de l’agent de mobilité. 
4. Lorsque l’installation réussit et passe à l’étape suivante, **Configurer**, supprimez les lignes que vous avez ajoutées.
5. Pour installer le fournisseur VSS, ouvrez une fenêtre d’invite de commandes en tant qu’administrateur et exécutez la commande suivante :
   
    **C:\Program Files (x86)\Microsoft Azure Site Recovery\agent> .\InMageVSSProvider_Install.cmd**

9. Vérifiez que le fournisseur VSS d’ASR est installé en tant que service dans les services Windows et ouvrez la console MMC de services de composants pour vérifier que le fournisseur VSS d’ASR est répertorié.
10. Si l’installation du fournisseur VSS continue d’échouer, utilisez CX pour résoudre les erreurs d’autorisations dans CAPI2.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>L’installation du fournisseur VSS échoue du fait que le service de cluster est activé sur une machine non cluster

Ce problème entraîne un échec de l’installation de l’agent de mobilité d’Azure Site Recovery pendant l’étape d’installation du fournisseur VSS d’Azure Site Recovery en raison d’un problème lié à COM+ qui empêche l’installation du fournisseur VSS.
 
### <a name="to-identify-the-issue"></a>Pour identifier le problème

Dans le journal situé sur le serveur de configuration dans C:\ProgramData\ASRSetupLogs\UploadedLogs\<date-heure>UA_InstallLogFile.log, vous trouverez l’exception suivante :

COM+ n’a pas pu communiquer avec le Microsoft Distributed Transaction Coordinator (Exception de HRESULT : 0x8004E00F)

Pour résoudre le problème :

1.  Vérifiez que cette machine est une machine non cluster et que les composants du cluster ne sont pas utilisés.
3.  Si les composants ne sont pas utilisés, supprimez les composants du cluster de la machine.

## <a name="drivers-are-missing-on-the-source-server"></a>Pilotes manquants sur le serveur source

Si l’installation de l’agent de mobilité échoue, examinez les journaux d’activité situés sous C:\ProgramData\ASRSetupLogs pour déterminer si des pilotes requis sont manquants dans des jeux de contrôles.
 
Pour résoudre le problème :
  
1. À l’aide d’un éditeur du Registre tel que regedit.msc, ouvrez le Registre.
2. Ouvrez le nœud HKEY_LOCAL_MACHINE\SYSTEM.
3. Dans le nœud SYSTEM, localisez les jeux de contrôles.
4. Ouvrez chaque jeu de contrôles et vérifiez que les pilotes Windows suivants sont présents :

   - Atapi
   - Vmbus
   - Storflt
   - Storvsc
   - intelide
 
Réinstallez les pilotes manquants.

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](vmware-azure-tutorial.md) configurer la récupération d’urgence de machines virtuelles VMware.
