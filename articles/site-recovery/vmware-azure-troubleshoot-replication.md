---
title: Résoudre des problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article fournit des informations sur la résolution de problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094699"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Résoudre les problèmes de réplication pour les serveurs physiques et machines virtuelles VMware

Il se peut que vous receviez un message d’erreur spécifique lorsque vous protégez vos serveurs physiques ou machines virtuelles VMware avec Azure Site Recovery. Cet article décrit certains problèmes courants que vous pouvez rencontrer lors de la réplication de serveurs physiques et machines virtuelles VMware locaux sur Azure avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Analyser le fonctionnement du serveur de processus pour éviter les problèmes de réplication

Il est recommandé d’analyser le fonctionnement du serveur de processus sur le portail de sorte que la réplication progresse pour les machines sources associées. Dans le coffre, accédez à Gérer > Infrastructure Site Recovery > Serveurs de configuration. Dans le panneau Serveur de configuration, cliquez sur le serveur de processus sous Serveurs associés. Le panneau Serveur de processus s’ouvre sur ses statistiques d’intégrité. Vous pouvez suivre l’utilisation du processeur, l’utilisation de la mémoire, l’état des services du serveur de processus nécessaire à la réplication, la date d’expiration du certificat et l’espace libre. L’état de toutes les statistiques doit être vert. 

**Il est recommandé de maintenir une utilisation de la mémoire et du processeur inférieure à 70 % et un espace libre supérieur à 25 %**. L’espace libre correspond à l’espace disque de cache du serveur de processus utilisé pour stocker les données de réplication des machines sources avant de les charger sur Azure. S’il passe au-dessous de 20 %, la réplication sera limitée pour toutes les machines sources associées. Suivez [l’aide relative à la capacité](./site-recovery-plan-capacity-vmware.md#capacity-considerations) afin de comprendre la configuration requise pour répliquer les ordinateurs sources.

Vérifiez que les services suivants s’exécutent sur l’ordinateur du serveur de processus. Démarrez ou redémarrez tout service qui n’est pas en cours d’exécution.

**Serveur de processus prédéfini**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* Installation Push InMage
* Service de chargement de journaux (LogUpload)
* Service d’application InMage Scout
* Agent Microsoft Azure Recovery Services (obengine)
* Agent VX InMage Scout – Sentinel/Outpost (svagents)
* tmansvc
* Service de publication World Wide Web (W3SVC)
* MySQL
* Service Microsoft Azure Site Recovery (dra)

**Serveur de processus avec montée en charge**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* Installation Push InMage
* Service de chargement de journaux (LogUpload)
* Service d’application InMage Scout
* Agent Microsoft Azure Recovery Services (obengine)
* Agent VX InMage Scout – Sentinel/Outpost (svagents)
* tmansvc

**Serveur de processus dans Azure pour la restauration automatique**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* Installation Push InMage
* Service de chargement de journaux (LogUpload)

Vérifiez que le type de démarrage de tous les services est défini sur **Automatique ou Automatique (démarrage différé)**. Il n’est pas nécessaire de définir ainsi le type de démarrage du service d’agent Microsoft Azure Recovery Services (obengine).

## <a name="replication-issues"></a>Problèmes de réplication

Échecs de réplication initiales et actuelles sont souvent provoquées par des problèmes de connectivité entre le serveur source et le serveur de processus ou entre le serveur de processus et Azure. Dans la plupart des cas, vous pouvez résoudre ces problèmes en procédant de la manière décrite dans les sections suivantes.

>[!Note]
>Assurez-vous que :
>1. Le système de date heure pour l’élément protégé est synchronisé.
>2. Aucun logiciel antiviru ne bloque Azure Site Recovery. En savoir plus [plus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sur les exclusions de dossiers requises pour Azure Site Recovery.

### <a name="check-the-source-machine-for-connectivity-issues"></a>Examinez la machine source pour les problèmes de connectivité

Liste montre les suivantes vous pouvez vérifier la machine source.

*  En ligne de commande, sur le serveur source, utilisez Telnet pour effectuer un test ping du serveur de processus via le port HTTPS en exécutant la commande suivante. Le port HTTPS 9443 est le port utilisé par défaut par le serveur de processus pour envoyer et recevoir le trafic de réplication. Vous pouvez modifier ce port au moment de l’inscription. La commande suivante vérifie la présence de problèmes de connectivité réseau et de problèmes qui bloquent le port du pare-feu.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Utilisez Telnet pour tester la connectivité. N’utilisez pas `ping`. Si Telnet n’est pas installé, procédez de la manière décrite dans [Installer le client Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Si telnet parvient à se connecter au port du serveur de processus, un écran vide apparaît.

   Si vous ne pouvez pas vous connecter au serveur de processus, autorisez la porte d’entrée 9443 sur celui-ci. Par exemple, vous pouvez être amené à autoriser le port d’entrée 9443 sur le serveur de processus si votre réseau dispose d’un réseau de périmètre ou d’un sous-réseau filtré. Ensuite, vérifiez si le problème persiste.

*  Si telnet réussit, mais que l’ordinateur source signale qu’il ne parvient pas à atteindre le serveur de processus, ouvrez le navigateur web sur l’ordinateur source et vérifiez que l’adresse https://<IP_serveur_processus>:<port_données_serveur_processus>/ est accessible.

    L’erreur de certificat HTTPS est attendue lors de l’accès à cette adresse. Si vous poursuivez en ignorant l’erreur de certificat, vous obtiendrez l’erreur 400 – Demande incorrecte, ce qui signifie que le serveur ne peut pas traiter la demande du navigateur et que la connexion HTTPS standard au serveur fonctionne bien.

    En cas d’échec, des informations sur le message d’erreur sont fournies par le navigateur. Par exemple, si l’authentification du proxy est incorrecte, le serveur proxy retourne 407 – Authentification du proxy requise, ainsi que les actions requises, dans le message d’erreur. 

*  Dans les journaux suivants, sur la machine virtuelle source, vérifiez la présence d’erreurs liées à des échecs du chargement réseau :

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>Vérifiez que le serveur de processus pour les problèmes de connectivité

La liste suivante répertorie les manières dont vous pouvez vérifier le serveur de processus :

> [!NOTE]
> Le serveur de processus doit avoir une adresse IPv4 statique sans adresse IP NAT configurée dessus.

* **Vérifier la connectivité entre les ordinateurs sources et le serveur de processus**
* Si vous parvenez à établir une connexion telnet à partir de la machine source, mais que le serveur de processus n’est pas accessible à partir de la source, vérifiez la connexion de bout en bout avec cxprocessserver à partir de la machine virtuelle source en exécutant l’outil cxpsclient sur la machine :

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   Consultez les journaux générés sur le serveur de processus dans les répertoires suivants pour en savoir plus sur les erreurs correspondantes :

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* Vérifiez les journaux suivants sur le serveur de traitement au cas où aucune pulsation de PS. Cela est identifié par **code d’erreur 806** sur le portail.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **Vérifiez si le serveur de processus transmet activement des données à Azure**.

  1. Sur le serveur de processus, ouvrez le Gestionnaire des tâches (appuyez sur Ctrl+Maj+Échap).
  2. Sélectionnez l’onglet **Performances**, puis sélectionnez le lien **Ouvrir le Moniteur de ressources**. 
  3. Dans la page **Moniteur de ressources**, sélectionnez l’onglet **Réseau**. Sous **Processus avec activité réseau**, vérifiez si **cbengine.exe** envoie activement un volume important de données.

       ![Capture d’écran montrant les volumes sous Processus avec activité réseau](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  Si cbengine.exe n’envoie pas un volume important de données, procédez de la manière décrite dans les sections suivantes.

* **Vérifiez si le serveur de processus peut se connecter au Stockage Blob Azure**.

  Sélectionnez **cbengine.exe**. Sous **Connexions TCP**, vérifiez s’il y a une connectivité du serveur de processus à l’URL du Stockage Blob Azure.

  ![Capture d’écran montrant la connectivité entre cbengine.exe et l’URL du Stockage Blob Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  À défaut de connectivité du serveur de processus à l’URL du Stockage Blob Azure, dans le Panneau de configuration, sélectionnez **Services**. Vérifiez si les services suivants sont en cours d’exécution :

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Agent Microsoft Azure Recovery Services
  *  Service Microsoft Azure Site Recovery
  *  tmansvc

  Démarrez ou redémarrez tout service qui n’est pas en cours d’exécution. Vérifiez si le problème persiste.

* **Vérifiez si le serveur de processus peut se connecter à l’adresse IP publique Azure via le port 443**.

  Dans %programfiles%\Agent Microsoft Azure Recovery Services\Temp, ouvrez le dernier fichier CBEngineCurr.errlog. Dans le fichier, recherchez **443** ou la chaîne **Échec de la tentative de connexion**.

  ![Capture d’écran montrant les journaux d’erreur dans le dossier Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  Si des problèmes apparaissent, dans la ligne de commande du serveur de processus, utilisez Telnet pour effectuer un test ping de votre adresse IP publique Azure (masquée dans l’image précédente). Vous trouverez votre adresse IP publique Azure dans le fichier CBEngineCurr.currLog via le port 443 :

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  Si vous ne parvenez pas à vous connecter, vérifiez si le problème d’accès est dû à des paramètres de pare-feu ou de proxy, comme décrit à l’étape suivante.

* **Vérifiez si le pare-feu basé sur l’adresse IP sur le serveur de processus bloque l’accès**.

  Si vous utilisez des règles de pare-feu basées sur l’adresse IP sur le serveur, téléchargez la liste complète des [Plages d’adresses IP de centres de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ajoutez les plages d’adresses IP à la configuration de votre pare-feu pour vous assurer que celui-ci autorise les communications vers Azure (et le port HTTPS par défaut, 443). Autorisez les plages d’adresses IP correspondant à la région Azure de votre abonnement et à la région Azure USA Ouest (utilisée pour le contrôle d’accès et la gestion des identités).

* **Vérifiez si le pare-feu basé sur l’URL sur le serveur de processus bloque l’accès**.

  Si vous utilisez une règle de pare-feu basée sur l’URL sur le serveur, ajoutez les URL répertoriées dans le tableau suivant à la configuration du pare-feu :

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Vérifiez si les paramètres de proxy sur le serveur de processus bloquent l’accès**.

   Si vous utilisez un serveur proxy, vérifiez que son nom est résolu par le serveur DNS. Pour vérifier la valeur que vous avez fournie lors de la configuration du serveur de configuration, accédez à la clé de Registre **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

   Assurez-vous ensuite que les mêmes paramètres sont utilisés par l’agent Azure Site Recovery pour l’envoi de données : 
      
   1. Recherchez **Sauvegarde Microsoft Azure**. 
   2. Ouvrez **Sauvegarde Microsoft Azure**, puis sélectionnez **Action** > **Modifier les propriétés**. 
   3. Sous l’onglet **Configuration du Proxy**, vous devriez voir l’adresse proxy. Celle-ci doit être identique à l’adresse proxy indiquée dans les paramètres du Registre. Si ce n’est pas le cas, modifiez-la.

*  **Vérifiez si la bande passante est limitée sur le serveur de processus**.

   Augmentez la bande passante, puis vérifiez si le problème persiste.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Ordinateur source non répertorié sur le portail Azure

Lorsque vous tentez de sélectionner la machine source pour activer la réplication avec Site Recovery, il se peut que cette machine ne soit pas disponible pour l’une des raisons suivantes :

* **Deux machines virtuelles avec le même UUID d’instance** : Si deux machines virtuelles sous le vCenter ont le même UUID d’instance, la première machine découverte par le serveur de configuration apparaît sur le portail Azure. Pour résoudre ce problème, assurez-vous que deux machines virtuelles n’ont pas le même UUID d’instance. Ce scénario est courant quand une machine virtuelle de sauvegarde devient active et qu’elle est connectée à nos enregistrements de découverte. Consultez [Azure Site Recovery, WMware vers Azure : comment nettoyer les entrées en double ou périmées](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) pour résoudre le problème.
* **Informations d’identification de l’utilisateur vCenter incorrectes** : Vérifiez que vous avez ajouté les informations d’identification de vCenter correctes lors de la configuration du serveur de configuration en utilisant le modèle OVF ou une configuration unifiée. Pour vérifier les informations d’identification que vous avez ajoutées pendant la configuration, voir [Modifier les informations d’identification pour la découverte automatique](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **Privilèges insuffisants pour vCenter** : Si les autorisations fournies pour accéder à vCenter ne sont pas les autorisations requises, la découverte des machines virtuelles pourrait échouer. Assurez-vous que les autorisations décrites dans [Préparer un compte pour la découverte automatique](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) sont ajoutées au compte d’utilisateur de vCenter.
* **Serveurs d’administration Azure Site Recovery** : Si la machine virtuelle est utilisée comme serveur d’administration sous un ou plusieurs de ces rôles (serveur de configuration, serveur de processus avec scale-out, serveur cible maître), vous ne pourrez pas choisir la machine virtuelle à partir du portail. Les serveurs d’administration ne peuvent pas être répliqués.
* **Machine virtuelle déjà protégée/basculée par le biais des services Azure Site Recovery** : si la machine virtuelle est déjà protégée ou basculée par le biais de Site Recovery, vous ne pouvez pas la sélectionner à des fins de protection dans le portail. Vérifiez que la machine virtuelle que vous recherchez sur le portail n’est pas déjà protégée par un autre utilisateur ou sous un autre abonnement.
* **vCenter non connecté** : Vérifiez si vCenter est dans un état connecté. Pour cela, accédez au coffre Recovery Services > Infrastructure Site Recovery > Serveurs de configuration, puis cliquez sur un serveur de configuration. Un panneau s’ouvre à droite avec des détails sur les serveurs associés. Vérifiez si vCenter est connecté. S’il est dans un état « Non connecté », résolvez le problème, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) dans le portail. Après cela, la machine virtuelle est listée dans le portail.
* **ESXi hors tension** : Si l’hôte ESXi sous lequel la machine virtuelle réside est dans un état hors tension, la machine virtuelle n’est pas listée ou ne peut pas être sélectionnée dans le portail Azure. Mettez l’hôte ESXi sous tension, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server) dans le portail. Après cela, la machine virtuelle est listée dans le portail.
* **Redémarrage en attente** : si un redémarrage est en attente sur la machine virtuelle, vous ne pouvez pas sélectionner la machine dans le portail Azure. Veillez à effectuer les activités de redémarrage en attente, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Après cela, la machine virtuelle est listée dans le portail.
* **IP introuvable** : Si une adresse IP valide n’est pas associée à la machine virtuelle, vous ne pouvez pas sélectionner la machine dans le portail Azure Veillez à affecter une adresse IP valide à la machine virtuelle, puis [actualisez le serveur de configuration](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Après cela, la machine virtuelle est listée dans le portail.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Les machines virtuelles protégées apparaissent en grisé dans le portail

Les machines virtuelles répliquées sous Azure Site Recovery sont indisponibles sur le portail Azure s’il existe des entrées en double dans le système. Pour savoir comment supprimer des entrées périmées et résoudre le problème, consultez [Azure Site Recovery, WMware vers Azure : comment nettoyer les entrées en double ou périmées](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-recommended-steps-for-resolution"></a>Les étapes pour la résolution des erreurs courantes et recommandé

### <a name="initial-replication-issues-error-78169"></a>Problèmes de réplication [erreur 78169] initiale

Sur un ci-dessus garantir qu’il existe aucune connectivité, la bande passante ou l’heure de synchroniser les problèmes connexes, vérifiez que :

- Aucun logiciel antiviru ne bloque Azure Site Recovery. En savoir plus [plus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sur les exclusions de dossiers requises pour Azure Site Recovery.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>Point de récupération de la cohérence des applications [erreur 78144] manquant

 Cela se produit en raison de problèmes avec Volume Shadow copy Service (VSS). Pour résoudre ce problème : 
 
- Vérifiez que la version installée de l’agent Azure Site Recovery est au moins 9.22.2. 
- Vérifiez que le fournisseur VSS est installé en tant que service dans les Services Windows et vérifiez la console de Service de composant MMC pour vérifier que le fournisseur VSS Azure Site Recovery est répertorié.
- Si le fournisseur VSS n’est pas installé, consultez le [Échec de l’installation article sur le dépannage](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Si VSS est désactivé,
    - Vérifiez que le type de démarrage du service fournisseur VSS est défini sur **automatique**.
    - Redémarrez les services suivants :
        - Service VSS
        - Fournisseur VSS d’Azure Site Recovery
        - Service VDS

### <a name="high-churn-on-source-machine-error-78188"></a>Taux de variation élevé sur l’ordinateur Source [erreur 78188]

Causes possibles :
- Le taux de modification de données (octets/s d’écriture) sur les disques répertoriés de la machine virtuelle est supérieur à la [Azure Site Recovery prises en charge les limites](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) pour le type de compte de stockage cible la réplication.
- Il existe un pic soudain dans le taux d’évolution en raison de quelle quantité élevée de données est en attente pour le chargement.

Pour résoudre le problème :
- Assurez-vous que le type de compte de stockage cible (Standard ou Premium) est approvisionné selon les besoins de taux d’ATTRITION à la source.
- Si l’activité observée est temporaire, attendez quelques heures pour le chargement des données en attente pour rattraper le retard et créer des points de récupération.
- Si le problème persiste, utilisez l’ASR [Planificateur de déploiement](site-recovery-deployment-planner.md#overview) pour aider à planifier la réplication.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>Aucune pulsation à partir de la Machine Source [erreur 78174]

Cela se produit lorsque l’agent de mobilité Azure Site Recovery sur la Machine Source ne communique pas avec le serveur de Configuration (CS).

Pour résoudre ce problème, procédez comme suit pour vérifier la connectivité réseau à partir de la machine virtuelle source vers le serveur de configuration :

1. Vérifiez que l’ordinateur Source est en cours d’exécution.
2. Connectez-vous à l’ordinateur Source à l’aide d’un compte disposant des privilèges d’administrateur.
3. Vérifiez que les services suivants sont en cours d’exécution et si ne pas redémarrer les services :
   - Svagents (InMage Scout VX Agent)
   - Service d’application InMage Scout
4. Sur la Machine Source, examinez les journaux à l’emplacement pour les détails de l’erreur :

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>Aucune pulsation du serveur de processus [erreur 806]
Au cas où aucune pulsation à partir du serveur de processus, vérifiez que :
1. PS Machine virtuelle est en cours d’exécution
2. Vérifiez suivante ouvre une session sur le serveur de traitement pour les détails de l’erreur :

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>Aucune pulsation du serveur cible maître [erreur 78022]

Cela se produit lorsque l’agent de mobilité Azure Site Recovery sur le serveur cible maître ne communique pas avec le serveur de Configuration.

Pour résoudre ce problème, procédez comme suit pour vérifier l’état du service :

1. Vérifiez que la machine virtuelle cible de Master est en cours d’exécution.
2. Connectez-vous à la machine virtuelle cible maître à l’aide d’un compte disposant des privilèges d’administrateur.
    - Vérifiez que le service svagents est en cours d’exécution. Si elle est en cours d’exécution, redémarrez le service
    - Vérifiez les journaux à l’emplacement pour les détails de l’erreur :
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>Serveur de processus n’est pas accessible à partir de la Machine Source [erreur 78186]

Cette erreur entraîne l’application et blocage points cohérents ne pas générés si elle n’est pas traité. Pour résoudre ce problème, suivez les liens de dépannage ci-dessous :
1. Vérifiez que [PS Services sont en cours d’exécution](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Vérifiez les problèmes de connectivité de machine source](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Vérifiez les problèmes de connectivité de serveur de processus](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) et suivez les instructions fournies pour :
    - Vérification de la connectivité avec la source
    - Problèmes de pare-feu et proxy

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>Chargement de données bloqué à partir de la Machine Source vers le serveur de processus [erreur 78028]

Cette erreur entraîne l’application et blocage points cohérents ne pas générés si elle n’est pas traité. Pour résoudre ce problème, suivez les liens de dépannage ci-dessous :

1. Vérifiez que [PS Services sont en cours d’exécution](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Vérifiez les problèmes de connectivité de machine source](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Vérifiez les problèmes de connectivité de serveur de processus](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) et suivez les instructions fournies pour :
    - Vérification de la connectivité avec la source
    - Problèmes de pare-feu et proxy

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide, publiez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Nous avons une communauté active et l’un de nos ingénieurs peut vous aider.
