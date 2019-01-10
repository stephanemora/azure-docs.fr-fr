---
title: Installer manuellement le service Mobilité pour la reprise d'activité des serveurs physiques et des machines virtuelles VMware à l'aide d'Azure Site Recovery | Microsoft Docs
description: Découvrez comment installer l’agent du service Mobilité pour la reprise d’activité de machines virtuelles VMware et serveurs physiques sur Azure à l’aide du service Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 06430bf476c2e9f3af2102272fb54d201a3f1066
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790807"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Installer manuellement le service Mobilité sur des serveurs physiques et machines virtuelles VMware

Lorsque vous configurez la reprise d’activité des serveurs physiques et des machines virtuelles VMware à l'aide d'[Azure Site Recovery](site-recovery-overview.md), vous installez le [service Mobilité de Site Recovery](vmware-physical-mobility-service-overview.md) sur l'ensemble des serveurs physiques et des machines virtuelles VMware locaux.  Le service Mobilité enregistre les écritures de données sur la machine et les transmet au serveur de processus Site Recovery.

Cet article explique comment procéder à l'installation manuelle du service Mobilité sur chacune des machines que vous souhaitez protéger.

## <a name="create-a-passphrase"></a>Créer une phrase secrète

Commencez par créer une phrase secrète qui sera utilisée lors de l’installation.

1. Connectez-vous au serveur de configuration.
2. Ouvrez une invite de commandes en tant qu’administrateur.
3. Indiquez le dossier bin, puis créez un fichier de phrase secrète.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Stockez le fichier de phrase secrète dans un emplacement sécurisé. 


## <a name="install-the-service-from-the-ui"></a>Installer le service à partir de l’interface utilisateur

>[!IMPORTANT]
> Si vous répliquez une machine virtuelle IaaS Azure d’une région Azure vers une autre, n’utilisez pas cette méthode. Utilisez plutôt une invite de commandes.

1. Recherchez la version du programme d’installation correspondant au système d’exploitation de la machine. Les programmes d’installation se trouvent dans le dossier %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Identifiez](vmware-physical-mobility-service-overview.md#installer-files) le programme d’installation dont vous avez besoin.
2. Copiez le fichier d’installation sur la machine et exécutez-le.
3. Dans **Option d’installation**, sélectionnez **Installer le service Mobilité**.
4. Sélectionnez l’emplacement d’installation > **Installer**.

    ![Page de l’option d’installation du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Surveillez l’installation dans **Progression de l’installation**. Au terme de l’installation, sélectionnez **Passer à la configuration** pour inscrire le service auprès du serveur de configuration.

    ![Page d’inscription du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  Dans **Détails du serveur de configuration**, spécifiez l’adresse IP et la phrase secrète que vous avez configurée.  

    ![Page d’inscription du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Sélectionnez **Inscrire** pour terminer l’inscription.

    ![Page d’inscription finale du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Installer le service à partir de l'invite de commandes

### <a name="on-a-windows-machine"></a>Sur une machine Windows

1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Procédez à l’installation comme suit :

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Inscrivez l’agent auprès du serveur de configuration.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Paramètres d’installation
**Paramètre** | **Détails**
--- | ---
Usage | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform "VmWare" /Silent
Fichiers journaux d’installation | Sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Paramètre d’installation obligatoire. Spécifie si le service Mobilité (MS) ou Master Target(MT) doit être installé.
/InstallLocation| Paramètre facultatif. Spécifie l’emplacement d’installation du service Mobilité (n’importe quel dossier).
/Platform | Obligatoire. Spécifie la plateforme sur laquelle le service Mobilité est installé. **VMware** pour les serveurs physiques/machines virtuelles VMware ; **Azure** pour les machines virtuelles Azure. 
/Silent| facultatif. Indique si le programme d’installation doit être exécuté en mode silencieux.

#### <a name="registration-settings"></a>Paramètres d’inscription
**Paramètre** | **Détails**
--- | ---
Usage | UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Journaux de configuration de l’agent | Sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
/PassphraseFilePath |  Obligatoire. Emplacement de la phrase secrète. Utilisez n’importe quel chemin d’accès UNC ou local valide.


### <a name="on-a-linux-machine"></a>Sur une machine Linux

1. Copiez le programme d’installation dans un dossier local (par exemple /tmp) sur le serveur que vous souhaitez protéger. Dans un terminal, exécutez les commandes suivantes :
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Procédez à l’installation comme suit :

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Une fois l’installation terminée, le service Mobilité doit être inscrit auprès du serveur de configuration. Exécutez la commande suivante pour inscrire le service Mobilité auprès du serveur de configuration.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>Paramètres d’installation
**Paramètre** | **Détails**
--- | ---
Usage | ./install -d <Install Location> -r <MS|MT> -v VmWare -q
-r | Paramètre d’installation obligatoire. Spécifie si le service Mobilité (MS) ou Master Target(MT) doit être installé.
-d | Paramètre facultatif. Spécifie l’emplacement d’installation du service Mobilité : /usr/local/ASR.
-v | Obligatoire. Spécifie la plateforme sur laquelle le service Mobilité est installé. **VMware** pour les serveurs physiques/machines virtuelles VMware ; **Azure** pour les machines virtuelles Azure. 
-q | facultatif. Indique si le programme d’installation doit être exécuté en mode silencieux.

#### <a name="registration-settings"></a>Paramètres d’inscription
**Paramètre** | **Détails**
--- | ---
Usage | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
-i | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
-P |  Obligatoire. Chemin d’accès complet du fichier dans lequel la phrase secrète est enregistrée. Utilisez n’importe quel dossier valide

## <a name="next-steps"></a>Étapes suivantes
- [Configurer la reprise d'activité pour les machines virtuelles VMware](vmware-azure-tutorial.md)
- [Configurer la reprise d'activité pour les serveurs physiques](physical-azure-disaster-recovery.md)
