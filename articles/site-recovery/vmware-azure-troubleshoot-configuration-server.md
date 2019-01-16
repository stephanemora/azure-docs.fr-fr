---
title: Résoudre les problèmes touchant le serveur de configuration durant la récupération d’urgence de serveurs physiques et machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article fournit des informations sur la résolution de problèmes de déploiement d’un serveur de configuration pour la récupération d’urgence de serveurs physiques et machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 597b8f59ef6991f7868d3de481e98ed9a459077b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050793"
---
# <a name="troubleshoot-configuration-server-issues"></a>Résoudre les problèmes de serveur de configuration

Cet article vous aide à résoudre des problèmes qui se produisent lors du déploiement et de la gestion du serveur de configuration [Azure Site Recovery](site-recovery-overview.md). Le serveur de configuration agit comme un serveur de gestion. Utilisez le serveur de configuration pour configurer la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery. Les sections suivantes décrivent les défaillances les plus courantes que vous pourriez rencontrer lorsque vous ajoutez et gérez un serveur de configuration.

## <a name="registration-failures"></a>Échecs d’enregistrement

La machine source s’inscrit auprès du serveur de configuration lorsque vous installez l’agent de mobilité. Vous pouvez déboguer toute défaillance au cours de cette étape en suivant les instructions suivantes :

1. Ouvrez le fichier C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (Le dossier ProgramData peut être masqué. Si vous ne le voyez pas, dans l’Explorateur de fichiers, sous l’onglet **Affichage**, dans la section **Afficher/Masquer**, activez la case à cocher **Éléments masqués**.) Des défaillances peuvent être causées par plusieurs problèmes.

2. Recherchez la chaîne **Aucune adresse IP valide trouvée**. Si la chaîne est trouvée :
    1. Vérifiez que l’ID hôte demandé est identique à celui de la machine source.
    2. Vérifiez que la machine source dispose d’au moins une adresse IP affectée à la carte réseau physique. Pour que l’inscription de l’agent auprès du serveur de configuration réussisse, la machine source doit avoir au moins une adresse IP v4 valide affectée à la carte physique.
    3. Exécutez l’une des commandes suivantes sur la machine source pour obtenir toutes les adresses IP de celle-ci :
      - Pour Windows : `> ipconfig /all`
      - Pour Linux : `# ifconfig -a`

3. Si la chaîne **Aucune adresse IP valide trouvée** n’est pas trouvée, recherchez la chaîne **Reason=>NULL**. Cette erreur se produit si la machine source utilise un hôte vide pour s’inscrire auprès du serveur de configuration. Si la chaîne est trouvée :
    - Après avoir résolu les problèmes, suivez les instructions de la section [Inscrire la machine source auprès du serveur de configuration](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) pour réessayer d’effectuer l’inscription manuellement.

4. Si la chaîne **Reason= >NULL** n’est pas trouvée, sur la machine source, ouvrez le fichier C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Le dossier ProgramData peut être masqué. Si vous ne le voyez pas, dans l’Explorateur de fichiers, sous l’onglet **Affichage**, dans la section **Afficher/Masquer**, activez la case à cocher **Éléments masqués**.) Des défaillances peuvent être causées par plusieurs problèmes. 

5. Recherchez la chaîne **post request: (7) - Connexion au serveur impossible**. Si la chaîne est trouvée :
    1. Résolvez les problèmes de réseau entre la machine source et le serveur de configuration. Vérifiez que le serveur de configuration est accessible à partir de la machine source à l’aide d’outils réseau tels que ping et Détermination d’itinéraire, ou d’un navigateur web. Assurez-vous que la machine source peut atteindre le serveur de configuration via le port 443.
    2. Vérifiez si des règles de pare-feu sur la machine source bloquent la connexion entre celle-ci et le serveur de configuration. Collaborez avec vos administrateurs réseau pour débloquer d’éventuels problèmes de connexion.
    3. Vérifiez que les dossiers répertoriés dans [Site Recovery folder exclusions from antivirus programs](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) (Dossiers de Site Recovery exclus des programmes antivirus) sont exclus du logiciel antivirus.
    4. Une fois les problèmes de réseau résolus, recommencez l’inscription en suivant les instructions de la section [Inscrire la machine source auprès du serveur de configuration](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Si la chaîne **post request: (7) - Connexion au serveur impossible** n’est trouvée, dans le même fichier journal, recherchez la chaîne **request: (60) - Le certificat pair ne peut pas être authentifié avec les certificats d’autorité de certification donnés**. Cette erreur peut se produire si le certificat du serveur de configuration a expiré ou si la machine source ne prend en charge TLS 1.0 ou des protocoles SSL ultérieurs. Elle peut également se produire si un pare-feu bloque les communications SSL entre la machine source et le serveur de configuration. Si la chaîne est trouvée : 
    1. Pour résoudre le problème, connectez-vous à l’adresse IP du serveur de configuration via un navigateur web sur la machine source. Utilisez l’URI https :\/\/<adresse IP du serveur configuration\>: 443/. Assurez-vous que la machine source peut atteindre le serveur de configuration via le port 443.
    2. Vérifier si des règles de pare-feu sur la machine source doivent être ajoutées ou supprimées pour que celle-ci puisse communiquer avec le serveur de configuration. Compte tenu de la diversité des logiciels de pare-feu susceptibles d’être utilisés, nous ne pouvons pas répertorier toutes les configurations de pare-feu requises. Collaborez avec vos administrateurs réseau pour débloquer d’éventuels problèmes de connexion.
    3. Vérifiez que les dossiers répertoriés dans [Site Recovery folder exclusions from antivirus programs](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) (Dossiers de Site Recovery exclus des programmes antivirus) sont exclus du logiciel antivirus.  
    4. Après avoir résolu les problèmes, réessayez d’effectuer l’inscription en suivant les instructions de la section [Inscrire la machine source auprès du serveur de configuration](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. Sous Linux, si la valeur de la plateforme dans <INSTALLATION_DIR\>/etc/drscout.conf est endommagée, l’inscription échoue. Pour identifier ce problème, ouvrez le fichier /var/log/ua_install.log. Recherchez la chaîne **Abandon de la configuration car la valeur de VM_PLATFORM est null ou n’est pas VmWare/Azure**. La plateforme définie doit être **VmWare** ou **Azure**. Si le fichier drscout.conf est endommagé, il est recommandé de [désinstaller l’agent de mobilité](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service), puis de le réinstaller. Si la désinstallation échoue, procédez comme suit :
    1. Ouvrez le fichier Installation_Directory/uninstall.sh et commentez l’appel de la fonction **StopServices**.
    2. Ouvrez le fichier de Installation_Directory/Vx/bin/uninstall.sh et commentez l’appel de la fonction **stop_services**.
    3. Ouvrez le fichier Installation_Directory/Fx/uninstall et commentez toute la section qui tente d’arrêter le service Fx.
    4. [Désinstallez](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) l’agent de mobilité. Après la désinstallation, redémarrez le système, puis tentez de réinstaller l’agent de mobilité.

## <a name="installation-failure-failed-to-load-accounts"></a>Échec de l’installation : Échec du chargement des comptes

Cette erreur se produit quand le service ne parvient pas à lire les données de la connexion de transport au moment de l’installation de l’agent de mobilité et de l’inscription auprès du serveur de configuration. Pour résoudre ce problème, assurez-vous que TLS 1.0 est activé sur votre machine source.

## <a name="change-the-ip-address-of-the-configuration-server"></a>Modifier l’adresse IP du serveur de configuration

Il est fortement recommandé de ne pas modifier l’adresse IP d’un serveur de configuration. Assurez-vous que toutes les adresses IP affectées au serveur de configuration sont statiques. N’utilisez pas d’adresses IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008 : Le jeton SAML n'est pas valide

Pour éviter cette erreur, vérifiez que l’heure de votre système ne diffère pas de plus de 15 minutes de l’heure locale. Réexécutez le programme d’installation pour terminer l’inscription.

## <a name="failed-to-create-a-certificate"></a>Échec de création d’un certificat

Un certificat requis pour l’authentification de Site Recovery ne peut pas être créé. Recommencez la configuration après vous être assuré que vous opérez en tant qu’administrateur local.

## <a name="register-the-source-machine-with-the-configuration-server"></a>Inscrire la machine source auprès du serveur de configuration

### <a name="if-the-source-machine-runs-windows"></a>Si la machine source exécute Windows

Exécutez la commande suivante sur la machine source :

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

Paramètre | Détails
--- | ---
Usage | UnifiedAgentConfigurator.exe /CSEndPoint <adresse IP du serveur configuration\>/PassphraseFilePath <chemin d’accès du fichier de phrase secrète\>
Journaux de configuration de l’agent | Situés sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
/PassphraseFilePath |  Obligatoire. Emplacement de la phrase secrète. Utilisez n’importe quel chemin d’accès UNC ou local valide.

### <a name="if-the-source-machine-runs-linux"></a>Si la machine source exécute Linux

Exécutez la commande suivante sur la machine source :

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Paramètre | Détails
--- | ---
Usage | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <adresse IP du serveur configuration\> - P <chemin d’accès du fichier de phrase secrète\>
-i | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
-P |  Obligatoire. Chemin d’accès complet du fichier dans lequel la phrase secrète est enregistrée. Utilisez n’importe quel dossier valide.

