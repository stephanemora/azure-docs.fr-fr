---
title: Résoudre les erreurs d’installation Push du service Mobilité lors de l’activation de la réplication dans le cadre d’une récupération d’urgence | Microsoft Docs
description: Résoudre les erreurs d’installation des services Mobilité lors de l’activation de la réplication dans le cadre d’une récupération d’urgence
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 10/29/2018
ms.openlocfilehash: c12683fee7022b84e097c2b71628776271611429
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210990"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Résoudre les problèmes d’installation Push du service Mobilité

L’installation du service Mobilité est une étape clé de l’activation de la réplication. La réussite de cette étape dépend uniquement de la satisfaction des conditions préalables et de l’utilisation des configurations prises en charge. Les erreurs les plus courantes que vous rencontrez au cours de l’installation du service Mobilité sont dues aux

* Erreurs d’informations d’identification/de privilèges
* Erreurs de connectivité
* Systèmes d’exploitation non pris en charge

Lorsque vous activez la réplication, Azure Site Recovery tente une installation Push de l’agent du service Mobilité sur votre machine virtuelle. Dans le cadre de cette installation, le serveur de configuration essaie de se connecter à la machine virtuelle et de copier l’agent. Pour réussir l’installation, suivez les instructions de dépannage étape par étape ci-dessous.

## <a name="credentials-check-errorid-95107--95108"></a>Vérification des informations d’identification (ErrorID : 95107 et 95108)

* Vérifiez si le compte d’utilisateur choisi lors de l’activation de la réplication est **valide et précis**.
* Azure Site Recovery requiert des **privilèges d’administrateur** pour effectuer l’installation Push.
  * Pour Windows, vérifiez si le compte d’utilisateur a un accès administratif, local ou de domaine, à la machine source.
  * Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur l’ordinateur local.
    * Pour ce faire, sous la clé de Registre HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez un nouveau DWORD : LocalAccountTokenFilterPolicy. Définissez la valeur sur 1. Pour exécuter cette étape, exécutez la commande suivante depuis l’invite de commande :

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Pour Linux, vous devez choisir le compte racine pour une installation réussie de l’agent de mobilité.

Si vous souhaitez modifier les informations d’identification du compte d’utilisateur choisi, suivez les instructions fournies [ici](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="connectivity-check-errorid-95117--97118"></a>**Vérification des informations d’identification (ErrorID : 95117 et 97118)**

* Vérifiez que vous êtes en mesure d’effectuer un test ping sur votre machine source à partir du serveur de configuration. Si vous avez choisi un serveur de traitement avec évolution pour l’activation de réplication, assurez-vous de pouvoir effectuer un test ping sur votre machine source à partir du serveur de processus.
  * À partir de la ligne de commande du serveur source, utilisez Telnet pour effectuer un test Ping sur le serveur de configuration/de traitement avec évolution avec le port HTTPS (par défaut 9443) comme indiqué ci-dessous. Cela vous indiquera s’il existe des problèmes de connectivité réseau ou des problèmes de blocage de port par le pare-feu.

     `telnet <CS/ scale-out PS IP address> <port>`

  * Si vous ne parvenez pas à vous connecter, autorisez le port entrant 9443 sur le serveur de configuration/de traitement avec évolution.
  * Vérifier l’état du service **InMage Scout VX Agent - Sentinel/Outpost**. Démarrez le service s’il n’est pas en cours d’exécution.

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
* Il peut s’agir d’un problème lié au domaine, au réseau ou à la connectivité. Cela peut également être dû à un problème de résolution de nom DNS ou d’insuffisante de ports TCP. Veuillez vérifier s’il existe des problèmes connus dans votre domaine.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Vérification des services de partage de fichiers et d’imprimantes (ErrorID : 95105 et 95106)

Après une vérification de la connectivité, vérifiez si le service de partage de fichiers et d’imprimantes est activé sur votre machine virtuelle.

Pour **Windows 2008 R2 et les versions antérieures**,

* Pour activer le partage de fichiers et d’impression via le Pare-feu Windows,
  * Ouvrez le panneau de contrôle -> Système et sécurité -> Pare-feu Windows -> sur le volet gauche, cliquez sur Paramètres avancés -> cliquez sur Règles de trafic entrant dans l’arborescence de la console.
  * Recherchez les règles de partage de fichiers et d’imprimantes (NB-Session-In) et de partage de fichiers et d’imprimantes (SMB-In). Pour chaque règle, cliquez avec le bouton droit sur la règle, puis sur **Activer la règle**.
* Pour activer le partage de fichiers avec la stratégie de groupe,
  * Accédez au menu Démarrer, tapez gpmc.msc et recherchez.
  * Dans le volet de navigation, ouvrez les dossiers suivants : Stratégie ordinateur local, Configuration utilisateur, Modèles d’administration, Composants Windows et Partage réseau.
  * Dans le volet d’informations, double-cliquez sur **Prevent users from sharing files within their profile** (Empêcher les utilisateurs de partager des fichiers dans leur profil). Pour désactiver le paramètre de stratégie de groupe et activer la capacité d’utilisateur à partager des fichiers, cliquez sur Désactivé. Cliquez sur OK pour enregistrer vos modifications. Cliquez [ici](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))pour en savoir plus.

Pour **les versions ultérieures**, suivez les instructions fournies [ici](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) pour activer le partage de fichiers et d’imprimantes

## <a name="windows-management-instrumentation-wmi-configuration-check"></a>Vérification de la configuration de Windows Management Instrumentation (WMI)

Après avoir vérifié les services de fichiers et d’imprimantes, activez le service WMI via le pare-feu.

* Dans le panneau de configuration, cliquez sur Sécurité, puis sur Pare-feu Windows.
* Cliquez sur Modifier les paramètres, puis sur l’onglet Exceptions.
* Dans la fenêtre Exceptions, sélectionnez la case à cocher de Windows Management Instrumentation (WMI) pour activer le trafic WMI à travers le pare-feu. 

Vous pouvez également activer le trafic WMI via le pare-feu à l’invite de commandes. Utilisez la commande `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` suivante
Vous pouvez trouver d’autres articles concernant la résolution des problèmes WMI dans les articles suivants.

* [Test de base WMI](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [Résolution des problèmes WMI](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [Résolution des problèmes liés aux scripts et aux services WMI](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Systèmes d’exploitation non pris en charge

Un échec peut aussi être dû à un système d’exploitation non pris en charge. Vérifiez que vous êtes sur la version du noyau/du système d’exploitation prise en charge pour réussir l’installation du service Mobilité.

Pour en savoir plus sur les systèmes d’exploitation pris en charge par Azure Site Recovery, reportez-vous à notre [document de matrice de prise en charge](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment](vmware-azure-tutorial.md) configurer la récupération d’urgence de machines virtuelles VMware.