---
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301443"
---
1. Copiez le programme d’installation dans un dossier local (par exemple /tmp) sur le serveur que vous souhaitez protéger. Dans un terminal, exécutez les commandes suivantes :
     ```
     cd /tmp;
     tar -xvzf Microsoft-ASR_UA*release.tar.gz
     ```
2. Pour installer le service Mobilité, exécutez la commande suivante :

     ```
     sudo ./install -d <Install Location> -r MS -v VmWare -q
     ```
3. Une fois l’installation terminée, le service Mobilité doit être inscrit auprès du serveur de configuration. Exécutez la commande suivante pour inscrire le service Mobilité auprès du serveur de configuration.

     ```
     /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
     ```

#### <a name="mobility-service-installer-command-line"></a>Ligne de commande du programme d’installation du service Mobilité

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Paramètre|Type|Description|Valeurs possibles|
|-|-|-|-|
|-r |Obligatoire|Spécifie si le service Mobilité (MS) ou MasterTarget(MT) doit être installé.|MS <br /> MT|
|-d |Facultatif|Emplacement où est installé le service Mobilité.|/usr/local/ASR|
|-v|Obligatoire|Spécifie la plateforme sur laquelle le service Mobilité est installé. <br /> <br />- **VMware**: Utilisez cette valeur si vous installez le Service mobilité sur une machine virtuelle en cours d’exécution *hôtes VMware vSphere ESXi*, *hôtes Hyper-V*, et *serveurs physiques*. <br /> - **Azure**: Utilisez cette valeur si vous installez un agent sur une machine virtuelle Azure IaaS.| VMware <br /> Azure|
|-q|Facultatif|Indique que le programme d’installation doit être exécuté en mode silencieux.| S.O.|

#### <a name="mobility-service-configuration-command-line"></a>Ligne de commande de configuration du service Mobilité

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Paramètre|Type|Description|Valeurs possibles|
|-|-|-|-|
|-i |Obligatoire|IP du serveur de configuration|Une adresse IP valide|
|-P |Obligatoire|Chemin complet du fichier dans lequel la phrase secrète de connexion est enregistrée|N’importe quel dossier valide|
<!--Update_Description: wording update-->
<!--ms.date: 03/05/2018-->