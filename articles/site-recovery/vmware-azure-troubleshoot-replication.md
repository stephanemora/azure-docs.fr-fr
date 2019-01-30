---
title: Résoudre des problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article fournit des informations sur la résolution de problèmes de réplication durant la récupération d’urgence de serveurs physiques et de machines virtuelles VMware sur Azure avec Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/18/2019
ms.author: ramamill
ms.openlocfilehash: 5c2d33b39614ded95ac38e07c844b0a8cafa7cd2
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54411473"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Résoudre les problèmes de réplication pour les serveurs physiques et machines virtuelles VMware

Il se peut que vous receviez un message d’erreur spécifique lorsque vous protégez vos serveurs physiques ou machines virtuelles VMware avec Azure Site Recovery. Cet article décrit certains problèmes courants que vous pouvez rencontrer lors de la réplication de serveurs physiques et machines virtuelles VMware locaux sur Azure avec [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problèmes de réplication initiale

Les défaillances de réplication initiales résultent souvent de problèmes de connectivité entre le serveur source et le serveur de processus, ou entre celui-ci et Azure. Dans la plupart des cas, vous pouvez résoudre ces problèmes en procédant de la manière décrite dans les sections suivantes.

### <a name="check-the-source-machine"></a>Vérifier la machine source

La liste suivante répertorie les manières dont vous pouvez vérifier la machine source :

*  Dans la ligne de commande sur le serveur source, utilisez Telnet pour effectuer un test ping du serveur de processus via le port HTTPS (par défaut, le port 9443) en exécutant la commande suivante. La commande vérifie la présence de problèmes de connectivité réseau et de problèmes qui bloquent le port du pare-feu.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Utilisez Telnet pour tester la connectivité. N’utilisez pas `ping`. Si Telnet n’est pas installé, procédez de la manière décrite dans [Installer le client Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Si vous ne pouvez pas vous connecter au serveur de processus, autorisez la porte d’entrée 9443 sur celui-ci. Par exemple, vous pouvez être amené à autoriser le port d’entrée 9443 sur le serveur de processus si votre réseau dispose d’un réseau de périmètre ou d’un sous-réseau filtré. Ensuite, vérifiez si le problème persiste.

*  Vérifiez l’état du service **InMage Scout VX Agent – Sentinel/OutpostStart**. Si le service ne s’exécute pas, démarrez-le, puis vérifiez si le problème persiste.   

### <a name="check-the-process-server"></a>Vérifier le serveur de processus

La liste suivante répertorie les manières dont vous pouvez vérifier le serveur de processus :

*  **Vérifiez si le serveur de processus transmet activement des données à Azure**.

   1. Sur le serveur de processus, ouvrez le Gestionnaire des tâches (appuyez sur Ctrl+Maj+Échap).
   2. Sélectionnez l’onglet **Performances**, puis sélectionnez le lien **Ouvrir le Moniteur de ressources**. 
   3. Dans la page **Moniteur de ressources**, sélectionnez l’onglet **Réseau**. Sous **Processus avec activité réseau**, vérifiez si **cbengine.exe** envoie activement un volume important de données.

        ![Capture d’écran montrant les volumes sous Processus avec activité réseau](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Si cbengine.exe n’envoie pas un volume important de données, procédez de la manière décrite dans les sections suivantes.

*  **Vérifiez si le serveur de processus peut se connecter au Stockage Blob Azure**.

   Sélectionnez **cbengine.exe**. Sous **Connexions TCP**, vérifiez s’il y a une connectivité du serveur de processus à l’URL du Stockage Blob Azure.

   ![Capture d’écran montrant la connectivité entre cbengine.exe et l’URL du Stockage Blob Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   À défaut de connectivité du serveur de processus à l’URL du Stockage Blob Azure, dans le Panneau de configuration, sélectionnez **Services**. Vérifiez si les services suivants sont en cours d’exécution :

   *  cxprocessserver
   *  InMage Scout VX Agent – Sentinel/Outpost
   *  Agent Microsoft Azure Recovery Services
   *  Service Microsoft Azure Site Recovery
   *  tmansvc

   Démarrez ou redémarrez tout service qui n’est pas en cours d’exécution. Vérifiez si le problème persiste.

*  **Vérifiez si le serveur de processus peut se connecter à l’adresse IP publique Azure via le port 443**.

   Dans %programfiles%\Agent Microsoft Azure Recovery Services\Temp, ouvrez le dernier fichier CBEngineCurr.errlog. Dans le fichier, recherchez **443** ou la chaîne **Échec de la tentative de connexion**.

   ![Capture d’écran montrant les journaux d’erreur dans le dossier Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Si des problèmes apparaissent, dans la ligne de commande du serveur de processus, utilisez Telnet pour effectuer un test ping de votre adresse IP publique Azure (masquée dans l’image précédente). Vous trouverez votre adresse IP publique Azure dans le fichier CBEngineCurr.currLog via le port 443 :

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Si vous ne parvenez pas à vous connecter, vérifiez si le problème d’accès est dû à des paramètres de pare-feu ou de proxy, comme décrit à l’étape suivante.

*  **Vérifiez si le pare-feu basé sur l’adresse IP sur le serveur de processus bloque l’accès**.

   Si vous utilisez des règles de pare-feu basées sur l’adresse IP sur le serveur, téléchargez la liste complète des [Plages d’adresses IP de centres de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ajoutez les plages d’adresses IP à la configuration de votre pare-feu pour vous assurer que celui-ci autorise les communications vers Azure (et le port HTTPS par défaut, 443). Autorisez les plages d’adresses IP correspondant à la région Azure de votre abonnement et à la région Azure USA Ouest (utilisée pour le contrôle d’accès et la gestion des identités).

*  **Vérifiez si le pare-feu basé sur l’URL sur le serveur de processus bloque l’accès**.

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

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide, publiez votre question sur le [forum Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Nous avons une communauté active et l’un de nos ingénieurs peut vous aider.
