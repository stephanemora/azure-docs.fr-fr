---
title: À propos du service Mobilité pour la reprise d'activité des serveurs physiques et des machines virtuelles VMware à l'aide d'Azure Site Recovery | Microsoft Docs
description: Familiarisez-vous avec l'agent du service Mobilité pour la reprise d’activité des serveurs physiques et des machines virtuelles VMware sur Azure à l’aide du service Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 929a4e4366c9e94ed4e1915406914991624f6baa
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149629"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>À propos du service Mobilité pour serveurs physiques et machines virtuelles VMware

Lorsque vous configurez la reprise d’activité des serveurs physiques et des machines virtuelles VMware à l'aide d'[Azure Site Recovery](site-recovery-overview.md), vous installez le service Mobilité de Site Recovery sur l'ensemble des serveurs physiques et des machines virtuelles VMware locaux.  Le service Mobilité enregistre les écritures de données sur la machine et les transmet au serveur de processus Site Recovery. Vous pouvez déployer le service Mobilité à l’aide des méthodes suivantes :

- [Installation Push](#push-installation) : Site Recovery installe l’agent de mobilité sur le serveur lorsque la protection est activée via le portail Azure.
- Installez manuellement : Vous pouvez installer le service mobilité manuellement sur chaque ordinateur via [l’interface utilisateur](#install-mobility-agent-through-ui) ou [invite de commandes](#install-mobility-agent-through-command-prompt).
- [Déploiement automatisé](vmware-azure-mobility-install-configuration-mgr.md) : vous pouvez automatiser l’installation à l’aide d’outils de déploiement de logiciels tels que System Center Configuration Manager.

## <a name="anti-virus-on-replicated-machines"></a>Protection antivirus sur les machines répliquées

Si les machines que vous souhaitez répliquer ont un logiciel antivirus actif en cours d'exécution, assurez-vous d'exclure le dossier d'installation du service Mobilité des opérations antivirus (*C:\ProgramData\ASR\agent*). Cela garantit le bon fonctionnement de la réplication.

## <a name="push-installation"></a>Installation push

Installation push fait partie intégrante de «[activer la réplication](vmware-azure-enable-replication.md#enable-replication)« tâche déclenchée dans le portail. Après avoir choisi l’ensemble des machines virtuelles que vous souhaitez protéger et déclencher « Activer la réplication », serveur de configuration exécute un push de l’agent de mobilité sur les serveurs, installe l’agent et terminez l’inscription de l’agent avec le serveur de configuration. Pour la réussite de cette opération,

- Assurez-vous que tous les installation push [conditions préalables](vmware-azure-install-mobility-service.md) sont remplies.
- Assurez-vous que toutes les configurations de serveurs se situent sous [matrice de prise en charge de VMware pour le scénario de récupération d’urgence Azure](vmware-physical-azure-support-matrix.md).

Détails de flux de travail installation push a été décrite dans les sections suivantes.

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>À partir de [9.23 version](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) et versions ultérieures

Pendant l’installation push de l’agent de mobilité, les étapes suivantes sont effectuées

1. Exécute un push de l’agent sur la machine source. Copie de l’agent sur l’ordinateur source peut échouer en raison de plusieurs erreurs de l’environnement. Visitez [nos conseils](vmware-azure-troubleshoot-push-install.md) pour résoudre les échecs d’installation push.
2. Une fois l’agent est copié sur les vérifications de configuration requise du serveur sont effectuées sur le serveur. L’installation échoue si une ou plusieurs de la [conditions préalables](vmware-physical-azure-support-matrix.md) ne sont pas remplies. Si toutes les conditions préalables sont remplies, l’installation est déclenchée.
3. Le fournisseur VSS de récupération de Site Azure est installé sur le serveur dans le cadre de l’installation de l’agent de mobilité. Ce fournisseur est utilisé pour générer des points de cohérence d’Application. En cas d’installation du fournisseur VSS, cette étape sera ignorée et l’installation de l’agent va continuer.
4. Si l’agent installation réussit, mais l’installation du fournisseur VSS échoue, état de la tâche est marquée comme « Avertissement ». Cela n’affecte pas la génération de points de cohérence sur incident.

    a. Pour générer des points de cohérence d’application, reportez-vous à [nos conseils](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) pour terminer l’installation du fournisseur VSS de récupération de Site manuellement. </br>
    b.  Si vous ne souhaitez pas de points cohérent d’application à générer, [modifier la stratégie de réplication](vmware-azure-set-up-replication.md#create-a-policy) pour désactiver les points de cohérence d’application.

### <a name="before-922-versions"></a>Avant les 9.22 versions

1. Exécute un push de l’agent sur la machine source. Copie de l’agent sur l’ordinateur source peut échouer en raison de plusieurs erreurs de l’environnement. Visitez [nos conseils](vmware-azure-troubleshoot-push-install.md) pour résoudre les échecs d’installation push.
2. Une fois l’agent est copié sur les vérifications de configuration requise du serveur sont effectuées sur le serveur. L’installation échoue si une ou plusieurs de la [conditions préalables](vmware-physical-azure-support-matrix.md) ne sont pas remplies. Si toutes les conditions préalables sont remplies, l’installation est déclenchée.
3. Le fournisseur VSS de récupération de Site Azure est installé sur le serveur dans le cadre de l’installation de l’agent de mobilité. Ce fournisseur est utilisé pour générer des points de cohérence d’Application. En cas d’installation du fournisseur VSS, installation de l’agent échoue. Pour éviter l’échec de l’installation de l’agent de mobilité, utilisez [9.23 version](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery) ou une version ultérieure pour générer des points cohérents d’incident et installez le fournisseur VSS manuellement.

## <a name="install-mobility-agent-through-ui"></a>Installer l’agent de mobilité via l’interface utilisateur

### <a name="prerequisite"></a>Configuration requise

- Assurez-vous que toutes les configurations de serveurs se situent sous [matrice de prise en charge de VMware pour le scénario de récupération d’urgence Azure](vmware-physical-azure-support-matrix.md).
- [Recherchez le programme d’installation](#locate-installer-files) basée sur le système d’exploitation du serveur.

>[!IMPORTANT]
> Si vous répliquez des machines virtuelles IaaS Azure à partir d’une région Azure vers un autre, n’utilisez pas cette méthode. Utilisez la méthode d’installation en ligne de commande à la place.

1. Copiez le fichier d’installation sur la machine et exécutez-le.
2. Dans **Option d’installation**, sélectionnez **Installer le service Mobilité**.
3. Sélectionnez l’emplacement d’installation > **Installer**.

    ![Page de l’option d’installation du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. Surveillez l’installation dans **Progression de l’installation**. Au terme de l’installation, sélectionnez **Passer à la configuration** pour inscrire le service auprès du serveur de configuration.

    ![Page d’inscription du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. dans **détails du serveur de Configuration**, spécifiez l’adresse IP et la phrase secrète que vous avez configuré.  

    ![Page d’inscription du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. Sélectionnez **Inscrire** pour terminer l’inscription.

    ![Page d’inscription finale du service Mobilité](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>Installer l’agent de mobilité via l’invite de commandes

### <a name="prerequisite"></a>Configuration requise

- Assurez-vous que toutes les configurations de serveurs se situent sous [matrice de prise en charge de VMware pour le scénario de récupération d’urgence Azure](vmware-physical-azure-support-matrix.md).
- [Recherchez le programme d’installation](#locate-installer-files) basée sur le système d’exploitation du serveur.

### <a name="on-a-windows-machine"></a>Sur une machine Windows

- Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger.

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- Procédez à l’installation comme suit :

    ```
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- Inscrivez l’agent auprès du serveur de configuration.

    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>Paramètres d’installation
**Paramètre** | **Détails**
--- | ---
Usage | UnifiedAgent.exe /Role < MS/MT > /InstallLocation <Install Location> /Platform « VmWare » /Silent
Fichiers journaux d’activité d’installation | Sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/Role | Paramètre d’installation obligatoire. Spécifie si le service Mobilité (MS) ou Master Target(MT) doit être installé.
/InstallLocation| Paramètre facultatif. Spécifie l’emplacement d’installation du service Mobilité (n’importe quel dossier).
/Platform | Obligatoire. Spécifie la plateforme sur laquelle le service Mobilité est installé. **VMware** pour les serveurs physiques/machines virtuelles VMware ; **Azure** pour les machines virtuelles Azure.
/Silent| facultatif. Indique si le programme d’installation doit être exécuté en mode silencieux.

#### <a name="registration-settings"></a>Paramètres d’inscription
**Paramètre** | **Détails**
--- | ---
Usage | UnifiedAgentConfigurator.exe /CSEndPoint \<CSIP > /PassphraseFilePath \<PassphraseFilePath >
Journaux d’activité de configuration de l’agent | Sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
/PassphraseFilePath |  Obligatoire. Emplacement de la phrase secrète. Utilisez n’importe quel chemin d’accès UNC ou local valide.

### <a name="on-a-linux-machine"></a>Sur une machine Linux

1. Copiez le programme d’installation dans un dossier local (par exemple /tmp) sur le serveur que vous souhaitez protéger. Dans un terminal, exécutez les commandes suivantes :

    ```
    cd /tmp ;
    tar -xvf Microsoft-ASR_UA*release.tar.gz
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
Usage | . /Install -d <Install Location> - r < MS/MT > v - VmWare - q
-r | Paramètre d’installation obligatoire. Spécifie si le service Mobilité (MS) ou Master Target(MT) doit être installé.
-d | Paramètre facultatif. Spécifie l’emplacement d’installation du service Mobilité : /usr/local/ASR.
-v | Obligatoire. Spécifie la plateforme sur laquelle le service Mobilité est installé. **VMware** pour les serveurs physiques/machines virtuelles VMware ; **Azure** pour les machines virtuelles Azure.
-q | facultatif. Indique si le programme d’installation doit être exécuté en mode silencieux.

#### <a name="registration-settings"></a>Paramètres d’inscription
**Paramètre** | **Détails**
--- | ---
Usage | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | Paramètre obligatoire. Spécifie l’adresse IP du serveur de configuration. Utilisez une adresse IP valide.
-P |  Obligatoire. Chemin d’accès complet du fichier dans lequel la phrase secrète est enregistrée. Utilisez n’importe quel dossier valide.

## <a name="azure-virtual-machine-agent"></a>Agent de machine virtuelle Azure

- **Machines virtuelles Windows** : à partir de la version 9.7.0.0 du service Mobilité, l’[agent de machine virtuelle Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent) est installé par le programme d’installation du service Mobilité. Ainsi, lorsque la machine bascule vers Azure, la machine virtuelle Azure remplit les conditions préalables à l’installation de l'agent pour utiliser une extension de machine virtuelle.
- **Machines virtuelles Linux** : l’agent [WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) doit être installé manuellement sur la machine virtuelle Azure après le basculement.

## <a name="locate-installer-files"></a>Localiser les fichiers de programme d’installation

Accédez au dossier %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository sur le serveur de configuration. Vérifiez le programme d’installation vous avez besoin en fonction de système d’exploitation. Le tableau suivant récapitule les fichiers de programme d’installation pour chaque VMware VM et le système d’exploitation de serveur physique. N'hésitez pas à consulter les [systèmes d’exploitation pris en charge](vmware-physical-azure-support-matrix.md#replicated-machines) avant de commencer.

**Fichier d’installation** | **Système d’exploitation (64 bits uniquement)**
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016 ; Windows Server 2012 R2; Windows Server 2012 ; Windows Server 2008 R2 SP1
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.*
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5
Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Serveur Ubuntu Linux 16.04 LTS
Microsoft-ASR_UA\*DEBIAN7 64\*release.tar.gz | Debian 7
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>Étapes suivantes

[Configurer l’installation Push du service Mobilité](vmware-azure-install-mobility-service.md)
