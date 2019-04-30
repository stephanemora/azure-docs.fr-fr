---
title: Gérer un serveur de processus pour la récupération d’urgence de machines virtuelles VMware et de serveurs physiques sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment gérer un serveur de processus configuré pour la récupération d’urgence de machines virtuelles VMware et d’un serveur physique sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0a0b6c83f800c0a479ba7a16c91b497d1a11da9e
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62732483"
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
>   En règle générale, lorsque vous utilisez l’image de la galerie Azure pour créer un serveur de processus dans Azure, dans le cadre d’une restauration automatique, elle utilise la version la plus récente. L’équipe Site Recovery publie régulièrement des correctifs et des améliorations, aussi nous vous recommandons de mettre à jour vos serveurs de processus.

## <a name="balance-the-load-on-process-server"></a>Équilibrer la charge sur le serveur de processus

Pour équilibrer la charge entre deux serveurs de processus,

1. Accédez à **Coffre Recovery Services** > **Gérer** > **Infrastructure Site Recovery** > **Pour les machines VMware et physiques** > **Serveurs de configuration**.
2. Cliquez sur le serveur de configuration avec lequel les serveurs de processus sont inscrits.
3. Liste des serveurs de processus inscrits pour les serveurs de configuration disponibles sur la page.
4. Cliquez sur le serveur de processus sur lequel vous souhaitez modifier la charge de travail.

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. Vous pouvez utiliser les options **Équilibrer la charge** ou **Échanger**, comme expliqué ci-dessous, selon les besoins.

### <a name="load-balance"></a>Équilibrer la charge

Avec cette option, vous pouvez sélectionner une ou plusieurs machines virtuelles et les transférer vers un autre serveur de processus.

1. Cliquez sur **Équilibrer la charge** et sélectionnez le serveur de processus cible dans la liste déroulante. Cliquez sur **OK**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. Cliquez sur **Sélectionner des machines**, choisissez les machines virtuelles que vous souhaitez déplacer à partir du serveur de processus actuel vers le serveur de processus cible. Des détails de la moyenne de modification des données sont affichés sur chaque machine virtuelle.
3. Cliquez sur **OK**. Suivez la progression du travail sous **Coffre Recovery Services** > **Surveillance** > **Travaux Site Recovery**.
4. Il faut 15 minutes pour que les modifications reflètent la réussite de cette opération OU pour [actualiser le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) pour un effet immédiat.

### <a name="switch"></a>Switch

Avec cette option, toute la charge de travail protégée sous un serveur de processus est placée sur un autre serveur de processus.

1. Cliquez sur **Échanger**, sélectionnez le serveur de processus cible et cliquez sur **OK**.

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. Suivez la progression du travail sous **Coffre Recovery Services** > **Surveillance** > **Travaux Site Recovery**.
3. Il faut 15 minutes pour que les modifications reflètent la réussite de cette opération OU pour [actualiser le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) pour un effet immédiat.

## <a name="process-server-selection-guidance"></a>Guide de sélection de serveur de processus

Azure Site Recovery identifie automatiquement si le serveur de processus est proche de ses limites d’utilisation. Aucune aide est fournie lorsque vous permettent de configurer une montée en puissance serveur de processus.

|État d'intégrité   |Explication  | Disponibilité des ressources  | Recommandation|
|---------|---------|---------|---------|
| Intègre (vert)    |   Serveur de processus est connecté et qu’il est sain      |Utilisation du processeur et mémoire est inférieure à 80 % ; Espace libre disponible est supérieure à 30 %| Ce serveur de processus peut être utilisé pour protéger les serveurs supplémentaires. Vérifiez que la nouvelle charge de travail s’inscrit dans le [définie par les limites du processus serveur](vmware-azure-set-up-process-server-scale.md#sizing-requirements).
|Avertissement (Orange)    |   Serveur de processus est connecté, mais certaines ressources sont sur le point d’atteindre les limites maximales  |   Utilisation du processeur et mémoire est entre 80 % à 95 % ; Disponibilité de l’espace libre est entre 25 à 30 %       | Utilisation du serveur de processus est proche de valeurs de seuil. Ajout de nouveaux serveurs au même serveur de processus entraîne le passage de valeurs de seuil et peut avoir un impact sur les éléments protégés existants. Il est conseillé de [le programme d’installation d’un serveur de traitement de montée en puissance](vmware-azure-set-up-process-server-scale.md#before-you-start) pour les réplications de nouveau.
|Avertissement (Orange)   |   Serveur de processus est connecté mais de données n’a pas été téléchargées vers Azure dans les dernières 30 min  |   Utilisation des ressources est dans les limites de seuil       | Résoudre les problèmes [échecs de chargement de données](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) avant d’ajouter de nouvelles charges de travail **ou** [le programme d’installation d’un serveur de traitement de montée en puissance](vmware-azure-set-up-process-server-scale.md#before-you-start) pour les réplications de nouveau.
|Critique (rouge)    |     Serveur de processus est peut-être déconnecté  |  Utilisation des ressources est dans les limites de seuil      | Résoudre les problèmes [traiter les problèmes de connectivité server](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) ou [le programme d’installation d’un serveur de traitement de montée en puissance](vmware-azure-set-up-process-server-scale.md#before-you-start) pour les réplications de nouveau.
|Critique (rouge)    |     L’utilisation des ressources a dépassé les limites de seuil |  Utilisation du processeur et mémoire est supérieure à 95 % ; Disponibilité de l’espace libre est inférieur à 25 %.   | Ajout de nouvelles charges de travail au même serveur de processus est désactivé en tant que seuil ressource limites sont déjà remplies. Par conséquent, [le programme d’installation d’un serveur de traitement de montée en puissance](vmware-azure-set-up-process-server-scale.md#before-you-start) pour les réplications de nouveau.
Critique (rouge)    |     Données n’a pas été téléchargées à partir d’Azure vers Azure dans la dernière 45 minutes. |  Utilisation des ressources est dans les limites de seuil      | Résoudre les problèmes [échecs de chargement de données](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues) avant d’ajouter de nouvelles charges de travail au même serveur de processus ou [le programme d’installation d’un serveur de traitement de montée en puissance](vmware-azure-set-up-process-server-scale.md#before-you-start)

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
- Répertoire d’installation du serveur de traitement, par exemple : C:\Program Files (x86)\Microsoft Azure Site Recovery