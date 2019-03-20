---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116248"
---
1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger. Exécutez les commandes suivantes en tant qu’administrateur à l’invite de commandes :

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. Pour installer le service Mobilité, exécutez la commande suivante :

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Désormais, l’agent doit être inscrit auprès du serveur de configuration.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Arguments de ligne de commande du programme d’installation du service Mobilité

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Paramètre|Type|Description|Valeurs possibles|
|-|-|-|-|
|/Role|Obligatoire|Spécifie si le service Mobilité (MS) ou MasterTarget(MT) doit être installé.|MS </br> MT|
|/InstallLocation|Facultatif|Emplacement où est installé le service Mobilité.|N’importe quel dossier sur l’ordinateur|
|/Platform|Obligatoire|Spécifie la plateforme sur laquelle le service Mobilité est installé. </br> </br>- **VMware**: Utilisez cette valeur si vous installez le Service mobilité sur une machine virtuelle en cours d’exécution *hôtes VMware vSphere ESXi*, *hôtes Hyper-V*, et *serveurs physiques*. </br> - **Azure**: Utilisez cette valeur si vous installez un agent sur une machine virtuelle Azure IaaS. | VMware </br> Azure|
|/Silent|Facultatif|Indique que le programme d’installation doit être exécuté en mode silencieux.| S.O.|

>[!TIP]
> Les journaux d’installation se trouvent sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Arguments de ligne de commande de l’inscription du service Mobilité

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Paramètre|Type|Description|Valeurs possibles|
  |-|-|-|-|
  |/CSEndPoint |Obligatoire|Adresse IP du serveur de configuration| Une adresse IP valide|
  |/PassphraseFilePath|Obligatoire|Emplacement de la phrase secrète |N’importe quel chemin d’accès UNC ou local valide|


>[!TIP]
> Les journaux de configuration de l’agent se trouvent sous %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
