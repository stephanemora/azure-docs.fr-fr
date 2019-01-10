---
title: Résoudre les problèmes touchant le serveur de configuration durant la reprise d’activité de serveurs physiques et machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article décrit des solutions aux problèmes courants pour permettre le déploiement d’un serveur de configuration durant la reprise d’activité de serveurs physiques et machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998963"
---
# <a name="troubleshoot-configuration-server-issues"></a>Résoudre les problèmes de serveur de configuration

Cet article vous aide à résoudre les problèmes lors du déploiement et de la gestion d’un serveur de configuration [Azure Site Recovery](site-recovery-overview.md). Le serveur de configuration fait office de serveur d'administration et est utilisé pour configurer la récupération d'urgence des machines virtuelles VMware et des serveurs physiques locaux sur Azure à l'aide de Site Recovery. Les sections suivantes traitent des échecs les plus courants rencontrés lors de l'ajout et de l'administration d'un serveur de configuration.

## <a name="registration-failures"></a>Échecs d'inscription

L'ordinateur source s'inscrit auprès du serveur de configuration lors de l'installation de l'agent de mobilité. Tout échec rencontré au cours de cette étape peut être débogué en suivant les instructions ci-dessous :

1. Accédez au fichier C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. ProgramData peut être un dossier masqué. Si vous ne parvenez pas à le localiser, essayez de l'afficher. Les échecs peuvent avoir plusieurs causes.
2. Recherchez la chaîne « Aucune adresse IP valide trouvée ». Si la chaîne est trouvée,
    - Déterminez si l'ID d'hôte demandé est identique à celui de l'ordinateur source.
    - Au moins une adresse IP doit être attribuée à la carte réseau physique sur l'ordinateur source pour que l'inscription de l'agent auprès du serveur de configuration aboutisse.
    - Exécutez la commande sur l'ordinateur source `> ipconfig /all` (pour Windows) et `# ifconfig -a` (pour Linux) afin d'obtenir toutes les adresses IP de l'ordinateur source.
    - Veuillez noter que l'inscription de l'agent nécessite qu'une adresse IP v4 valide soit attribuée à la carte réseau physique.
3. Si la chaîne ci-dessus est introuvable, recherchez la chaîne "Raison"=>"NULL". Si elle est trouvée,
    - Cette erreur survient lorsque l'ordinateur source utilise un hôte vide pour s'inscrire auprès du serveur de configuration.
    - Une fois les problèmes résolus, suivez les instructions fournies [ici](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) pour retenter l'inscription manuellement.
4. Si la chaîne ci-dessus est introuvable, accédez à l'ordinateur source et consultez le journal C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log. ProgramData peut être un dossier masqué. Si vous ne parvenez pas à le localiser, essayez de l'afficher. Les échecs peuvent avoir plusieurs causes. Recherchez la chaîne « requête post : (7) - Connexion au serveur impossible ». Si elle est trouvée,
    - Résolvez les problèmes de réseau entre l'ordinateur source et le serveur de configuration. Vérifiez que le serveur de configuration est accessible depuis l'ordinateur source à l'aide d'outils réseau tels que ping, traceroute, un navigateur web, etc. Assurez-vous que l'ordinateur source est en mesure d'accéder au serveur de configuration via le port 443.
    - Déterminez si des règles de pare-feu sont activées sur l'ordinateur source et bloquent la connexion entre celui-ci et le serveur de configuration. Travaillez en collaboration avec l'administrateur réseau pour résoudre les problèmes de connexion.
    - Vérifiez que les dossiers mentionnés [ici](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sont exclus du logiciel antivirus.
    - Une fois les problèmes de réseau résolus, retentez l'inscription en suivant les instructions fournies [ici](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Si la chaîne est introuvable, dans le même journal, recherchez la chaîne « requête: (60) - Le certificat pair ne peut pas être authentifié avec les certificats d'autorité de certification donnés. ». Si elle est trouvée, 
    - Cette erreur peut être due au fait que le certificat du serveur de configuration a expiré ou que l'ordinateur source ne prend pas en charge les protocoles SSL TLS 1.0 ou versions ultérieures, ou encore à l'existence d'un pare-feu qui bloque la communication SSL entre l'ordinateur source et le serveur de configuration.
    - Pour résoudre ce problème, connectez-vous à l'adresse IP du serveur de configuration via un navigateur web sur l'ordinateur source à l'aide de l'URI https://<CSIPADDRESS>:443/. Assurez-vous que l'ordinateur source est en mesure d'accéder au serveur de configuration via le port 443.
    - Déterminez si des règles de pare-feu sont activées sur l'ordinateur source et bloquent la connexion entre celui-ci et le serveur de configuration. Travaillez en collaboration avec l'administrateur réseau pour résoudre les problèmes de connexion.
    - Vérifiez que les dossiers mentionnés [ici](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sont exclus du logiciel antivirus.  
    - Une fois les problèmes résolus, retentez l'inscription en suivant les instructions fournies [ici](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. Sous Linux, si la valeur de plateforme de <INSTALLATION_DIR>/etc/drscout.conf est corrompue, l'inscription échoue. Pour l'identifier, accédez au journal /var/log/ua_install.log. Vous trouverez la chaîne « Abandon de la configuration car la valeur de VM_PLATFORM est null ou n'est pas VmWare/Azure ». La plateforme doit être définie sur « VmWare » ou « Azure ». Le fichier drscout.conf étant corrompu, il est recommandé de [désinstaller](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) l'agent de mobilité, puis de le réinstaller. Si la désinstallation échoue, suivez les étapes ci-dessous :
    - Ouvrez le fichier Installation_Directory/uninstall.sh et commentez l'appel à la fonction *StopServices*.
    - Ouvrez le fichier Installation_Directory/Vx/bin/uninstall et commentez l'appel à la fonction `stop_services`.
    - Ouvrez le fichier Installation_Directory/Fx/uninstall et commentez la section complète qui tente d'arrêter le service Fx.
    - Essayez maintenant de [désinstaller](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) l'agent de mobilité. Au terme de la désinstallation, redémarrez le système et essayez à nouveau d'installer l'agent.

## <a name="installation-failure---failed-to-load-accounts"></a>Échec de l'installation - Échec du chargement des comptes

Cette erreur survient lorsque le service ne parvient pas à lire les données de la connexion de transport au moment de l'installation de l'agent de mobilité et de l'inscription auprès du serveur de configuration. Pour résoudre ce problème, assurez-vous que TLS 1.0 est activé sur votre ordinateur source.

## <a name="change-ip-address-of-configuration-server"></a>Modifier l'adresse IP du serveur de configuration

Il est fortement déconseillé de modifier l'adresse IP d'un serveur de configuration. Vérifiez que toutes les adresses IP attribuées au serveur de configuration sont des adresses IP statiques et non des adresses IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008 : Le jeton SAML n'est pas valide

Pour éviter cette erreur, vérifiez que l'horloge de votre système n'a pas plus de 15 minutes d'avance ou de retard sur l'heure locale. Réexécutez le programme d’installation pour terminer l’inscription.

## <a name="failed-to-create-certificate"></a>Échec de création du certificat

Impossible de créer le certificat requis pour s'authentifier auprès de Site Recovery. Relancez le programme d'installation après vous être assuré que vous exécutez l'installation en tant qu'administrateur local.

## <a name="register-source-machine-with-configuration-server"></a>Inscrire l'ordinateur source auprès du serveur de configuration

### <a name="if-source-machine-has-windows-os"></a>Si l'ordinateur source exécute le système d'exploitation Windows

Exécutez la commande suivante sur l'ordinateur source

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Paramètre** | **Détails**
--- | ---
Usage | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Journaux de configuration de l’agent | Sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
/PassphraseFilePath |  Obligatoire. Emplacement de la phrase secrète. Utilisez n’importe quel chemin d’accès UNC ou local valide.

### <a name="if-source-machine-has-linux-os"></a>Si l'ordinateur source exécute le système d'exploitation Linux

Exécutez la commande suivante sur l'ordinateur source

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Paramètre** | **Détails**
--- | ---
Usage | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
-P |  Obligatoire. Chemin d’accès complet du fichier dans lequel la phrase secrète est enregistrée. Utilisez n’importe quel dossier valide