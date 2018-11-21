---
title: Gérer un serveur de processus pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment gérer un serveur de processus configuré pour la récupération d’urgence de machines virtuelles VMware et d’un serveur physique sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: ramamill
ms.openlocfilehash: d99b5d1fdca39466d5e09ca077329b7ffa8622bc
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568850"
---
# <a name="manage-process-servers"></a>Gérer des serveurs de processus

Par défaut, le serveur de processus utilisé lorsque vous répliquez des machines virtuelles VMware ou des serveurs physiques dans Azure est installé sur l’ordinateur du serveur de configuration local. Il existe deux cas de figure qui doivent vous amener à configurer un serveur de processus distinct :

- Pour les déploiements à grande échelle, il peut être nécessaire de faire évoluer la capacité de vos serveurs de processus locaux.
- Pour la restauration automatique, vous avez besoin d’un serveur de processus temporaire configuré dans Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée. 

Cet article résume les tâches de gestion courantes pour ces serveurs de processus supplémentaires.

## <a name="upgrade-a-process-server"></a>Mettre à niveau un serveur de processus

Pour mettre à niveau un serveur de processus exécuté en local ou dans Azure (en vue d’une restauration automatique), procédez comme suit :

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
  En règle générale, lorsque vous utilisez l’image de la galerie Azure pour créer un serveur de processus dans Azure, dans le cadre d’une restauration automatique, elle utilise la version la plus récente. L’équipe Site Recovery publie régulièrement des correctifs et des améliorations, aussi nous vous recommandons de mettre à jour vos serveurs de processus.



## <a name="reregister-a-process-server"></a>Réinscrire un serveur de processus

Si vous avez besoin de réinscrire un serveur de processus exécuté en local ou dans Azure, procédez comme suit au niveau du serveur de configuration :

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

Une fois les paramètres enregistrés, procédez comme suit :

1. Sur le serveur de processus, ouvrez une invite de commandes administrateur.
2. Accédez au dossier **%PROGRAMDATA%\ASR\Agent**, puis exécutez la commande suivante :

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>Modifier les paramètres de proxy pour un serveur de processus local

Si le serveur de processus utilise un proxy pour se connecter à Site Recovery dans Azure, utilisez cette procédure si vous devez modifier les paramètres de proxy existants.

1. Ouvrez une session sur l’ordinateur du serveur de processus. 
2. Ouvrez une fenêtre de commande d’administration PowerShell et exécutez la commande suivante :
  ```powershell
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
2. Accédez au dossier **%PROGRAMDATA%\ASR\Agent** et exécutez la commande suivante :
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```


## <a name="remove-a-process-server"></a>Supprimer un serveur de processus

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="manage-anti-virus-software-on-process-servers"></a>Gérer un logiciel antivirus sur les serveurs de processus

Si un logiciel antivirus est actif sur un serveur de processus autonome ou un serveur cible maître, excluez les dossiers suivants des opérations antivirus :


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- Répertoire d’installation du serveur de processus, par exemple : C:\Program Files (x86)\Microsoft Azure Site Recovery

