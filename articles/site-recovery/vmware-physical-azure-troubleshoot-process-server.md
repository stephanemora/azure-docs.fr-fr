---
title: Dépannage du serveur de processus Azure Site Recovery
description: Cet article décrit comment résoudre les problèmes avec le serveur de processus Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: rayne
ms.openlocfilehash: 926e5b685369f8660daf6221f818734f6f12d2b5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928406"
---
# <a name="troubleshoot-the-process-server"></a>Dépannage du serveur de processus

Le [Site Recovery](site-recovery-overview.md) serveur de processus est utilisé lorsque vous configurez la récupération d’urgence vers Azure pour les machines virtuelles VMware en local et des serveurs physiques. Cet article décrit comment résoudre les problèmes avec le serveur de processus, y compris les problèmes de connectivité et de réplication.

[En savoir plus](vmware-physical-azure-config-process-server-overview.md) sur le serveur de processus.

## <a name="before-you-start"></a>Avant de commencer

Avant de commencer la résolution des problèmes :

1. Assurez-vous que vous comprenez comment [surveiller les serveurs de processus](vmware-physical-azure-monitor-process-server.md).
2. Passez en revue les meilleures pratiques ci-dessous.
3. Assurez-vous que vous respectez [considérations relatives à la capacité](site-recovery-plan-capacity-vmware.md#capacity-considerations)et utiliser le Guide de dimensionnement pour les [serveur de configuration](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou [serveurs de processus autonomes](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Meilleures pratiques pour le déploiement de serveur de processus

Pour optimiser les performances des serveurs de processus, nous avons résumé un nombre des meilleures pratiques générales.

**Il est recommandé** | **Détails**
--- |---
**Utilisation** | Assurez-vous que le serveur de processus de configuration/server (autonome) sont utilisés uniquement pour le rôle prévu. N’exécutez pas rien d’autre sur l’ordinateur.
**Adresse IP** | Assurez-vous que le serveur de processus a une adresse IPv4 statique et n’a pas configuré pour NAT.
**Contrôler l’utilisation de mémoire/processeur** |Conserver sous utilisation du processeur et mémoire à 70 %.
**Garantir un espace libre** | Espace libre fait référence à l’espace disque de cache sur le serveur de processus. Les données de réplication sont stockées dans le cache avant d’être téléchargée vers Azure.<br/><br/> Gardez à l’espace libre supérieur à 25 %. Si elle est inférieure à 20 %, la réplication est limitée pour les machines répliquées qui sont associés au serveur de processus.

## <a name="check-process-server-health"></a>Vérifier l’intégrité du serveur de processus

La première étape de dépannage consiste à vérifier l’intégrité et l’état du serveur de processus. Pour ce faire, passez en revue toutes les alertes, vérifiez que les services requis sont en cours d’exécution et vérifiez qu’il existe une pulsation du serveur de traitement. Ces étapes sont résumées dans le graphique suivant, suivie de différentes procédures pour vous aider à effectuer les étapes.

![Résoudre les problèmes d’intégrité du serveur de processus](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Étape 1 : Résoudre les alertes d’intégrité du serveur processus

Le serveur de traitement génère un nombre d’alertes d’intégrité. Ces alertes et les actions recommandées, sont résumées dans le tableau suivant.

**Type d’alerte** | **Error** | **Résolution des problèmes**
--- | --- | --- 
![Healthy][green] | Aucun  | Serveur de processus est connecté et sain.
![Avertissement][yellow] | Services spécifiés ne sont pas en cours d’exécution. | 1. Vérifiez que les services sont en cours d’exécution.<br/> 2. Si les services s’exécutent comme prévu, suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).
![Avertissement][yellow]  | Processeur utilisation > 80 % au cours des 15 dernières minutes. | 1. Ne pas ajouter les nouveaux ordinateurs.<br/>2. Vérifiez que le nombre de machines virtuelles en utilisant le serveur de processus s’aligne sur [limites définies](site-recovery-plan-capacity-vmware.md#capacity-considerations)et envisagez de configurer un [serveur de traitement supplémentaire](vmware-azure-set-up-process-server-scale.md).<br/>3. Suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).
![Critique][red] |  Processeur utilisation > 95 % pendant les 15 dernières minutes. | 1. Ne pas ajouter les nouveaux ordinateurs.<br/>2. Vérifiez que le nombre de machines virtuelles en utilisant le serveur de processus s’aligne sur [limites définies](site-recovery-plan-capacity-vmware.md#capacity-considerations)et envisagez de configurer un [serveur de traitement supplémentaire](vmware-azure-set-up-process-server-scale.md).<br/>3. Suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).<br/> 4. Si le problème persiste, exécutez le [Deployment Planner](http://aka.ms/asr-v2a-deployment-planner) pour la réplication de serveur physique/VMware.
![Avertissement][yellow] | Utilisation de mémoire > 80 % au cours des 15 dernières minutes. |  1. Ne pas ajouter les nouveaux ordinateurs.<br/>2. Vérifiez que le nombre de machines virtuelles en utilisant le serveur de processus s’aligne sur [limites définies](site-recovery-plan-capacity-vmware.md#capacity-considerations)et envisagez de configurer un [serveur de traitement supplémentaire](vmware-azure-set-up-process-server-scale.md).<br/>3. Suivez les instructions associées à l’avertissement.<br/> 4. Si le problème persiste, suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).
![Critique][red] | Utilisation de mémoire > 95 % des 15 dernières minutes. | 1. Ne pas ajouter de nouveaux ordinateurs et que vous envisagez de configurer un [serveur de traitement supplémentaire](vmware-azure-set-up-process-server-scale.md).<br/> 2. Suivez les instructions associées à l’avertissement.<br/> 3. 4. Si le problème persiste, suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).<br/> 4. Si le problème persiste, exécutez le [Deployment Planner](http://aka.ms/asr-v2a-deployment-planner) de problèmes de réplication de serveur physique/VMware.
![Avertissement][yellow] | Cache dossier espace libre < 30 % au cours des 15 dernières minutes. | 1. Ne pas ajouter de nouveaux ordinateurs et envisagez de configurer un [serveur de traitement supplémentaire](vmware-azure-set-up-process-server-scale.md).<br/>2. Vérifiez que le nombre de machines virtuelles en utilisant le serveur de processus s’aligne sur [instructions](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).
![Critique][red] |  Espace libre < 25 % au cours des 15 minutes | 1. Suivez les instructions associées à l’avertissement pour ce problème.<br/> 2. 3. Suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).<br/> 3. Si le problème persiste, exécutez le [Deployment Planner](http://aka.ms/asr-v2a-deployment-planner) pour la réplication de serveur physique/VMware.
![Critique][red] | Aucune pulsation du serveur de traitement pour 15 minutes ou plus. Le service tmansvs ne communique pas avec le serveur de configuration. | (1) Vérifiez que le serveur de processus est en cours d’exécution.<br/> 2. Vérifiez que le tmassvc s’exécute sur le serveur de processus.<br/> 3. Suivez les instructions ci-dessous pour [résoudre les problèmes de connectivité et de réplication](#check-connectivity-and-replication).


![Clé de table](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Étape 2 : Vérifier les services de serveur de processus

Les services qui doivent être en cours d’exécution sur le serveur de processus sont résumées dans le tableau suivant. Il existe de légères différences dans les services, en fonction de la façon dont le serveur de processus est déployé. 

Pour tous les services à l’exception de l’Agent Microsoft Azure Recovery Services (obengine), vérifiez que le StartType est défini sur **automatique** ou **automatique (début différé)**.
 
**Déploiement** | **Services en cours d’exécution**
--- | ---
**Serveur de traitement sur le serveur de configuration** | ProcessServer ; ProcessServerMonitor ; cxprocessserver ; InMage PushInstall ; Service de téléchargement de journal (LogUpload) ; InMage Scout Application Service ; Microsoft Azure Recovery Services Agent (obengine) ; InMage Scout VX Agent-Sentinel/Outpost (svagents) ; tmansvc ; World Wide Web (W3SVC) ; de Service de publication MySQL ; Service Microsoft Azure Site Recovery (dra)
**Serveur de processus en cours d’exécution en tant que serveur autonome** | ProcessServer ; ProcessServerMonitor ; cxprocessserver ; InMage PushInstall ; Service de téléchargement de journal (LogUpload) ; InMage Scout Application Service ; Microsoft Azure Recovery Services Agent (obengine) ; InMage Scout VX Agent-Sentinel/Outpost (svagents) ; tmansvc.
**Serveur de processus déployé dans Azure pour la restauration automatique** | ProcessServer ; ProcessServerMonitor ; cxprocessserver ; InMage PushInstall ; Service de téléchargement de journal (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Étape 3 : Vérification de pulsation du serveur de processus

S’il n’existe aucune pulsation du serveur de traitement (code d’erreur 806), procédez comme suit :

1. Vérifiez que la machine virtuelle du serveur de processus est en cours d’exécution.
2. Vérifiez ces journaux d’erreurs.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Vérifiez la connectivité et la réplication

 Échecs de réplication initiales et actuelles sont souvent provoquées par des problèmes de connectivité entre les ordinateurs source et le serveur de processus, ou entre le serveur de processus et Azure. Ces étapes sont résumées dans le graphique suivant, suivie de différentes procédures pour vous aider à effectuer les étapes.

![Résoudre les problèmes de connectivité et réplication](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Étape 4 : Vérifier la synchronisation de l’heure sur l’ordinateur source

Assurez-vous que la date/heure système de la machine répliquée est synchronisée. [En savoir plus](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Étape 5 : Vérifier un logiciel antivirus sur l’ordinateur source

Vérifiez qu’aucun logiciel antiviru sur l’ordinateur répliquée ne bloque la récupération de Site. Si vous avez besoin exclure la récupération de Site à partir de programmes antivirus, passez en revue [cet article](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Étape 6 : Vérifiez la connectivité à partir de la machine source


1. Installer le [client Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) sur la machine source, si vous avez besoin. N’utilisez pas Ping.
2. À partir de la machine source, un test ping sur le serveur de processus sur le port HTTPS avec Telnet. Par défaut 9443 est le port HTTPS pour le trafic de réplication.

    `telnet <process server IP address> <port>`

3. Vérifiez si la connexion est établie.


**Connectivité** | **Détails** | **Action**
--- | --- | ---
**Réussie** | Telnet affiche un écran vide, et le serveur de processus est accessible. | Aucune autre action requise.
**Ayant échoué** | Vous ne pouvez pas vous connecter | Assurez-vous que le port entrant 9443 est autorisé sur le serveur de processus. Par exemple, si vous avez un réseau de périmètre ou un sous-réseau filtré. Vérifiez la connectivité à nouveau.
**Partiellement réussie** | Vous pouvez vous connecter, mais la machine source indique que le serveur de processus ne peut pas être atteint. | Poursuivez la procédure de dépannage suivante.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Étape 7 : Résoudre les problèmes d’un serveur de processus inaccessible

Si le serveur de processus n’est pas accessible à partir de la machine source, erreur 78186 s’affichera. Si ne pas traité, ce problème entraîne à la fois à cohérence des applications et des points de récupération cohérents d’incident ne pas en cours de génération comme prévu.

Résoudre les problèmes en vérifiant si la machine source peut atteindre l’adresse IP du serveur de processus et exécutez l’outil de cxpsclient sur l’ordinateur source, pour vérifier la connexion de bout en bout.


### <a name="check-the-ip-connection-on-the-process-server"></a>Vérifiez la connexion IP sur le serveur de processus

Si telnet réussit mais que la machine source signale que le serveur de processus ne peut pas être atteint, vérifiez si vous pouvez atteindre l’adresse IP du serveur de processus.

1. Dans un navigateur web, essayez d’atteindre IP adresse https://<PS_IP>:<PS_Data_Port>/.
2. Si cette vérification indique une erreur de certificat HTTPS, qui est normal. Si vous ignorez l’erreur, vous devez voir un 400 – demande incorrecte. Cela signifie que le serveur ne peut pas traiter la demande de navigateur, et que la connexion HTTPS standard convient.
3. Si cette vérification ne fonctionne pas, puis notez le message d’erreur de navigateur. Par exemple, une erreur 407 indique un problème avec l’authentification du proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Vérifiez la connexion avec cxpsclient

En outre, vous pouvez exécuter l’outil cxpsclient pour vérifier la connexion de bout en bout.

1. Exécutez l’outil comme suit :

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Sur le serveur de processus, vérifiez les journaux générés dans ces dossiers :

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Vérifier le chargement des journaux de machine virtuelle de source pour les échecs (erreur 78028)

Problème avec les téléchargements de données bloqué à partir de machines sources pour le service de processus peut entraîner les deux points de récupération cohérents d’incident et cohérence des applications ne pas en cours de génération. 

1. Pour résoudre les échecs de téléchargement de réseau, vous pouvez rechercher des erreurs dans ce journal :

    C:\Program fichiers (x86) \Microsoft Azure Site Recovery\agent\svagents*.log 

2. Utilisez le reste des procédures de cet article peut vous aider à résoudre les problèmes de chargement de données.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Étape 8 : Vérifiez si le serveur de processus/envoie des données

Vérifiez si le serveur de traitement transmet activement des données vers Azure.

  1. Sur le serveur de processus, ouvrez le Gestionnaire des tâches (appuyez sur Ctrl+Maj+Échap).
  2. Sélectionnez le **performances** onglet > **ouvrir le moniteur de ressource**.
  3. Dans **moniteur de ressource** page, sélectionnez le **réseau** onglet. Sous **processus avec activité réseau**, vérifiez si cbengine.exe envoie un vNotolume volumineux de données.

       ![Volumes sous les processus avec activité réseau](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Si cbengine.exe n’envoie pas un volume important de données, procédez de la manière décrite dans les sections suivantes.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Étape 9 : Vérifiez la connexion du serveur de processus vers le stockage blob Azure

1. Dans le moniteur de ressource, sélectionnez **cbengine.exe**.
2. Sous **connexions TCP**, vérifiez s’il existe une connectivité entre le serveur de processus et le stockage Azure.

  ![Connectivité entre cbengine.exe et l’URL de stockage d’objets Blob Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Vérifier les services

S’il n’existe pas de connectivité entre le serveur de processus à l’URL de stockage d’objets blob Azure, vérifiez que les services sont en cours d’exécution.

1. Dans le panneau de configuration, sélectionnez **Services**.
2. Vérifiez que les services suivants sont en cours d’exécution :

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Agent Microsoft Azure Recovery Services
    - Service Microsoft Azure Site Recovery
    - tmansvc

3. Démarrez ou redémarrez tout service qui n’est pas en cours d’exécution.
4. Vérifiez que le serveur de processus est connecté et accessible. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Étape 10 : vérifier la connexion de serveur de processus à l’adresse IP publique Azure

1. Sur le serveur de traitement dans **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, ouvrez le dernier fichier CBEngineCurr.errlog.
2. Dans le fichier, recherchez **443**, ou pour la chaîne **tentative de connexion a échoué**.

  ![Journaux d’erreurs dans le dossier Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Si vous voyez des problèmes, trouve votre adresse IP publique Azure dans le fichier du fichier CBEngineCurr.currLog via le port 443 :

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Sur la ligne de commande sur le serveur de processus, utilisez Telnet pour un test ping sur votre adresse IP publique Azure.
6. Si vous ne pouvez pas vous connecter, suivez la procédure suivante.

## <a name="step-11-check-process-server-firewall-settings"></a>Étape 11 : Vérifiez les paramètres de pare-feu de serveur de processus. 

Vérifiez si le pare-feu fondées sur l’adresse IP sur le serveur de processus bloque l’accès.

1. Pour les règles de pare-feu fondées sur l’adresse IP :

    (a) Télécharger la liste complète des [plages IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    (b) ajouter des plages d’adresses IP à votre configuration du pare-feu, pour vous assurer que le pare-feu autorise les communications vers Azure (et le port HTTPS par défaut, 443).

    Autoriser (c) plages d’adresses IP pour la région de votre abonnement Azure et pour la région Azure ouest des États-Unis (utilisées pour la gestion d’identité et contrôle d’accès).

2. Si un pare-feu basé sur l’URL, ajoutez les URL répertoriées dans le tableau suivant pour la configuration du pare-feu.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Étape 12 : Vérifiez les paramètres de proxy de serveur de processus 

1. Si vous utilisez un serveur proxy, vérifiez que son nom est résolu par le serveur DNS. Vérifiez la valeur que vous avez fourni lorsque vous configurez le serveur de configuration dans la clé de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Assurez-vous que les mêmes paramètres sont utilisés par l’agent Azure Site Recovery pour envoyer des données.

    (a) recherchez **sauvegarde Microsoft Azure**.

    (b) Ouvrez **sauvegarde Microsoft Azure**, puis sélectionnez **Action** > **modifier les propriétés**.

    c) dans le **Configuration du Proxy** onglet, l’adresse de proxy doit être identique à l’adresse de proxy qui est indiqué dans les paramètres du Registre. Si ce n’est pas le cas, modifiez-la.

## <a name="step-13-check-bandwidth"></a>Étape 13 : Vérifier la bande passante

Augmenter la bande passante entre le serveur de processus et Azure et vérifiez si le problème persiste.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide, publiez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png