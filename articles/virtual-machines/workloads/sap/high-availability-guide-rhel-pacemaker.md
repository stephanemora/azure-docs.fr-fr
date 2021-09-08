---
title: Configuration de Pacemaker sur RHEL dans Azure | Microsoft Docs
description: Configuration de Pacemaker sur Red Hat Entreprise Linux dans Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.custom: subject-rbac-steps
ms.date: 08/26/2021
ms.author: radeltch
ms.openlocfilehash: 412bbd6f7414cdeaab1c116210b511bc8000c270
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109798"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Configuration de Pacemaker sur Red Hat Entreprise Linux dans Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Commencez par lire les notes et publications SAP suivantes :

* Note SAP [1928533], qui contient :
  * La liste des tailles de machines virtuelles Azure prises en charge pour le déploiement de logiciels SAP.
  * Des informations importantes sur la capacité en fonction de la taille des machines virtuelles Azure.
  * Les logiciels SAP pris en charge et les combinaisons entre système d’exploitation et base de données.
  * La version du noyau SAP requise pour Windows et Linux sur Microsoft Azure.
* La note SAP [2015553] répertorie les conditions préalables au déploiement de logiciels SAP pris en charge par SAP sur Azure.
* La note SAP [2002167] recommande des paramètres de système d’exploitation pour Red Hat Enterprise Linux
* La note SAP [2009879] conseille sur SAP HANA pour Red Hat Enterprise Linux
* La note SAP [2178632] contient des informations détaillées sur toutes les métriques de surveillance rapportées pour SAP sur Azure.
* La note SAP [2191498] contient la version requise de l’agent hôte SAP pour Linux sur Azure.
* La note SAP [2243692] contient des informations sur les licences SAP sur Linux dans Azure.
* La note SAP [1999351] contient des informations de dépannage supplémentaires pour l’extension d’analyse Azure améliorée pour SAP.
* Le [WIKI de la communauté SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contient toutes les notes SAP requises pour Linux.
* [Planification et implémentation de machines virtuelles Azure pour SAP sur Linux][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP sur Linux (cet article)][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP sur Linux][dbms-guide]
* [Réplication de système SAP HANA dans un cluster Pacemaker](https://access.redhat.com/articles/3004101)
* Documentation RHEL générale
  * [Vue d’ensemble des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Référence des modules complémentaires de haute disponibilité](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Stratégies de prise en charge pour les clusters haute disponibilité RHEL – sbd et fence_sbd](https://access.redhat.com/articles/2800691)
* Documentation RHEL spécifique à Azure :
  * [Stratégies de prise en charge des clusters à haute disponibilité RHEL - Machines virtuelles Microsoft Azure en tant que membres du cluster](https://access.redhat.com/articles/3131341)
  * [Installation et configuration d’un cluster à haute disponibilité Red Hat Enterprise Linux 7.4 (et versions ultérieures) sur Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Considérations relatives à l’adoption de RHEL 8 - Haute disponibilité et clusters](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [Configure SAP S/4HANA ASCS/ERS with Standalone Enqueue Server 2 (ENSA2) in Pacemaker on RHEL 7.6](https://access.redhat.com/articles/3974941) (Configurer SAP S/4HANA ASC/ERS avec le serveur de file d’attente autonome 2 (ENSA2) dans Pacemaker sur RHEL 7.6)
  * [Offres RHEL pour SAP sur Azure](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Installation du cluster

![Présentation de Pacemaker sur RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat ne prend pas en charge la surveillance émulée par logiciel. Red Hat ne prend pas en charge SBD sur des plateformes cloud. Pour plus d’informations, voir [Stratégies de prise en charge pour les clusters haute disponibilité RHEL – sbd et fence_sbd](https://access.redhat.com/articles/2800691).
> Le seul mécanisme de délimitation pris en charge pour des clusters Pacemaker Red Hat Enterprise Linux sur Azure est l’agent de délimitation Azure.  

Les éléments suivants sont précédés de **[A]** (applicable à tous les nœuds), de **[1]** (applicable uniquement au nœud 1) ou de **[2]** (applicable uniquement au nœud 2).

1. **[A]** Inscrire. Cette étape n’est pas requise si vous utilisez des images RHEL SAP compatibles avec la haute disponibilité.  

   Inscrivez vos machines virtuelles et les attacher à un pool qui contient des référentiels pour RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   En attachant un pool à une image PAYG RHEL de la Place de marché Azure, vous serez doublement facturé pour votre utilisation de RHEL : une fois pour l’image PAYG et une fois pour le droit d’utilisation de RHEL dans le pool que vous attachez. Pour limiter ceci, Azure fournit désormais des images RHEL BYOS. Pour plus d’informations, consultez [Images Azure bring-your-own-subscription (Apportez votre propre abonnement) de Red Hat Enterprise Linux](../redhat/byos.md).

1. **[A]** Activer RHEL pour les référentiels SAP. Cette étape n’est pas requise si vous utilisez des images RHEL SAP compatibles avec la haute disponibilité.  

   Pour installer les packages requis, activez les référentiels suivants.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Installer le module complémentaire RHEL HA

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Nous vous recommandons d’utiliser les versions suivantes de l’agent de clôture Azure (ou versions ultérieures) pour que les clients bénéficient d’un délai de basculement plus rapide, en cas d’échec d’un arrêt de ressource ou si les nœuds de cluster ne peuvent plus communiquer les uns avec les autres :  
   > RHEL 7.7 et version ultérieure utilisent la dernière version disponible du package fence-agents  
   > RHEL 7.6 : fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5 : fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4 : fence-agents-4.0.11-66.el7_4.12  
   > Pour plus d’informations, consultez [Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711).

   Vérifiez la version de l’agent de clôture Azure. Si nécessaire, mettez-le à jour vers une version ultérieure ou égale à celle indiquée ci-dessus.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Si vous devez mettre à jour l’agent de clôture Azure et si vous utilisez un rôle personnalisé, mettez à jour ce rôle personnalisé en y ajoutant l’action **powerOff**. Pour plus d’informations, consultez [Créer un rôle personnalisé pour l’agent de clôture](#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Configurer la résolution de nom d’hôte

   Vous pouvez utiliser un serveur DNS ou modifier le fichier /etc/hosts sur tous les nœuds. Cet exemple montre comment utiliser le fichier /etc/hosts.
   Remplacez l’adresse IP et le nom d’hôte dans les commandes suivantes.  

   >[!IMPORTANT]
   > Si vous utilisez des noms d’hôte dans la configuration du cluster, il est essentiel de disposer d’une résolution de nom d’hôte fiable. Les communications de cluster échouent, si les noms ne sont pas disponibles et peuvent entraîner des retards de basculement de cluster.
   > L’avantage d’utiliser/etc/hosts réside dans le fait que votre cluster devient indépendant du serveur DNS, ce qui peut aussi être un point de défaillance unique.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Insérez les lignes suivantes dans le fichier /etc/hosts. Modifiez l’adresse IP et le nom d’hôte en fonction de votre environnement

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Changer le mot de passe hacluster pour utiliser le même mot de passe

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Ajouter des règles de pare-feu pour Pacemaker

   Ajoutez les règles de pare-feu suivantes à toutes les communications de cluster entre les nœuds de cluster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Activer les services de cluster de base

   Exécutez les commandes suivantes pour activer le service Pacemaker et le démarrer.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Créer le cluster Pacemaker

   Exécutez les commandes suivantes pour authentifier les nœuds et créer le cluster. Définissez le jeton sur 30000 pour autoriser la maintenance avec préservation de la mémoire. Pour plus d’informations, consultez [cet article pour Linux][virtual-machines-linux-maintenance].  
   
   Si vous créez un cluster sur **RHEL 7.x**, utilisez les commandes suivantes :  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Si vous créez un cluster sur **RHEL 8.x**, utilisez les commandes suivantes :  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Surveillez l’état du cluster en exécutant la commande suivante :  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Définir les votes attendus. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Si vous créez un cluster à plusieurs nœuds qui est un cluster avec plus de deux nœuds, ne définissez pas la valeur 2 aux votes.    

1. **[1]** Autoriser les actions de clôture simultanées

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>Créer l’appareil STONITH

L’appareil STONITH utilise un principal de service pour l’autorisation sur Microsoft Azure. Pour créer un principal de service, effectuez les étapes suivantes.

1. Accédez à <https://portal.azure.com>
1. Ouvrez le panneau Azure Active Directory  
   Accédez aux propriétés et notez l’ID du répertoire. Il s’agit de **l’ID du locataire**.
1. Cliquez sur Inscriptions d’applications
1. Cliquez sur Nouvelle inscription
1. Entrez un nom, sélectionnez « Comptes dans cet annuaire d’organisation uniquement » 
2. Sélectionnez le type d’application « Web », entrez une URL de connexion (par exemple http:\//localhost), puis cliquez sur Ajouter  
   L’URL de connexion n’est pas utilisée et peut être une URL valide
1. Sélectionnez Certificats et secrets, puis sélectionnez Nouveau secret client
1. Entrez une description pour la nouvelle clé, sélectionnez « N’expire jamais » et cliquez sur Ajouter
1. Définissez la valeur d’un nœud. Cette valeur est utilisée comme **mot de passe** pour le principal de service
1. Sélectionnez Vue d’ensemble. Notez l’ID de l’application. Cet identifiant est utilisé comme nom d’utilisateur (**ID de connexion** dans la procédure ci-dessous) du principal de service

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Créer un rôle personnalisé pour l’agent d’isolation

Par défaut, le principal de service ne possède pas les autorisations d’accéder à vos ressources Azure. Vous devez accorder au principal du service les autorisations de démarrer et d’arrêter (mettre hors tension) toutes les machines virtuelles du cluster. Si vous n’avez pas encore créé le rôle personnalisé, vous pouvez le créer à l’aide de [PowerShell](../../../role-based-access-control/role-assignments-powershell.md) ou de l’[interface de ligne de commande Azure](../../../role-based-access-control/role-assignments-cli.md).

Utilisez le contenu suivant pour le fichier d’entrée. Vous devez adapter le contenu à vos abonnements, c’est-à-dire remplacer c276fc76-9cd4-44c9-99a7-4fd71546436e et e91d47c4-76f3-4271-a796-21b4ecfe3624 par les ID de vos abonnements. Si vous n’avez qu’un seul abonnement, supprimez la deuxième entrée dans AssignableScopes.

```json
{
      "Name": "Linux Fence Agent Role",
      "description": "Allows to power-off and start virtual machines",
      "assignableScopes": [
              "/subscriptions/e663cc2d-722b-4be1-b636-bbd9e4c60fd9",
              "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
      ],
      "actions": [
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/powerOff/action",
              "Microsoft.Compute/virtualMachines/start/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Affecter le rôle personnalisé au principal de service

Affectez au principal de service le rôle personnalisé Linux Fence Agent Role (Rôle d’agent d’isolation Linux) créé dans la section précédente. N’utilisez plus le rôle Propriétaire ! Pour connaître les étapes détaillées, consultez [Attribuer des rôles Azure à l’aide du portail Azure](../../../role-based-access-control/role-assignments-portal.md).   
Veillez à attribuer le rôle pour les deux nœuds de cluster.    
      
### <a name="1-create-the-stonith-devices"></a>**[1]** Créer les appareils STONITH

Une fois que vous avez modifié les autorisations pour les machines virtuelles, vous pouvez configurer les appareils STONITH dans le cluster.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> L’option « pcmk_host_map » est requise dans la commande UNIQUEMENT si les noms d’hôte RHEL et les noms de nœud Azure ne sont PAS identiques. Spécifiez le mappage au format **nom-d-hôte:nom-machine-virtuelle**.
> Consultez la section en gras dans les commandes. Pour plus d’informations, consultez [Quel format dois-je utiliser pour spécifier des mappages de nœuds pour des appareils stonith dans pcmk_host_map](https://access.redhat.com/solutions/2619961)

Pour RHEL **7.x**, utilisez la commande suivante pour configurer l’appareil de clôture :    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 pcmk_delay_max=15 \
op monitor interval=3600
</code></pre>

Pour RHEL **8.x**, utilisez la commande suivante pour configurer l’appareil de clôture :  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 pcmk_delay_max=15 \
op monitor interval=3600
</code></pre>

> [!TIP]
> Configurez l’attribut `pcmk_delay_max` uniquement dans des clusters Pacemaker à deux nœuds. Pour plus d’informations sur la prévention des situations de clôture dans un cluster Pacemaker à deux nœuds, consultez [Retarder l’isolation dans un cluster à deux nœuds afin d’éviter les situations de clôture des scénarii de « fence death »](https://access.redhat.com/solutions/54829). 
 

> [!IMPORTANT]
> Les opérations de surveillance et de clôture sont désérialisées. Par conséquent, s’il y a une opération de surveillance plus longue et un événement de clôture simultanés, il n’y a aucun délai pour le basculement du cluster en raison de l’opération de surveillance en cours d’exécution.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Activer l’utilisation d’un appareil STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>L’agent d’isolation Azure requiert une connectivité sortante vers les points de terminaison publics comme indiqué, ainsi que des solutions possibles évoquées dans [Connectivité de point de terminaison public pour les machines virtuelles utilisant un équilibreur de charge interne standard](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


## <a name="optional-stonith-configuration"></a>Configuration STONITH facultative  

> [!TIP]
> Cette section s’applique uniquement si vous souhaitez configurer un appareil de clôture spécial `fence_kdump`.  

S’il est nécessaire de collecter des informations de diagnostic au sein de la machine virtuelle, il peut être utile de configurer un appareil STONITH supplémentaire, en fonction de l’agent de clôture `fence_kdump`. L’agent `fence_kdump` peut détecter qu’un nœud est entré dans la récupération d’urgence kdump et peut autoriser le service de récupération après incident à se terminer, avant d’appeler d’autres méthodes de clôture. Notez que `fence_kdump` ne remplace pas les mécanismes de clôture traditionnels, tels que l’agent de clôture Azure lors de l’utilisation de machines virtuelles Azure.   

> [!IMPORTANT]
> Sachez que lorsque `fence_kdump` est configuré comme un premier niveau de stonith, il introduira des retards dans les opérations de clôture et les retards respectifs dans le basculement des ressources de l’application.  
> 
> Si un vidage sur incident est correctement détecté, la clôture sera retardée jusqu’à ce que le service de récupération après incident se termine. Si le nœud défaillant est inaccessible ou s’il ne répond pas, les clôtures seront retardées dans le temps, selon le nombre d’itérations configuré et le `fence_kdump` délai d’expiration. Pour plus d’informations, consultez [Comment configurer fence_kdump dans un cluster Pacemaker Red Hat](https://access.redhat.com/solutions/2876971).  
> Il peut être nécessaire d’adapter le délai d’expiration de la fence_kdump proposée à l’environnement spécifique.
>     
> Nous vous recommandons de configurer `fence_kdump` stonith uniquement lorsque cela est nécessaire pour collecter les diagnostics au sein de la machine virtuelle et toujours en combinaison avec la méthode de clôture traditionnelle comme agent de clôture Azure.   

Les Bases de connaissances Red Hat suivantes contiennent des informations importantes sur la configuration de `fence_kdump` stonith :

* [Comment configurer fence_kdump dans un cluster Pacemaker Red Hat](https://access.redhat.com/solutions/2876971).
* [Comment configurer/gérer des niveaux STONITH dans le cluster RHEL avec Pacemaker](https://access.redhat.com/solutions/891323)
* [fence_kdump échoue avec « délai d’expiration après X secondes » dans un cluster à haute disponibilité RHEL 6 ou 7 avec kexec-tools antérieurs à 2.0.14](https://access.redhat.com/solutions/2388711)
* Pour plus d’informations sur la modification du délai d’expiration par défaut, consultez [Comment configurer kdump pour une utilisation avec le module complémentaire à haute disponibilité RHEL 6, 7, 8](https://access.redhat.com/articles/67570)
* Pour plus d’informations sur la réduction du délai de basculement, lors de l’utilisation de `fence_kdump`, consultez [ Puis-je réduire le délai attendu de basculement lors de l’ajout de la configuration fence_kdump](https://access.redhat.com/solutions/5512331)
   
Exécutez les étapes facultatives suivantes pour ajouter `fence_kdump` en tant que configuration de premier niveau STONITH, en plus de la configuration de l’agent de clôture Azure. 


1. **[A]** Vérifiez que kdump est actif et configuré.  
    ```
    systemctl is-active kdump
    # Expected result
    # active
    ```
2. **[A]**  Installez l’agent de clôture `fence_kdump`.  
    ```
    yum install fence-agents-kdump
    ```
3. **[1]** Créez`fence_kdump` l’appareil stonith dans le cluster.   
    <pre><code>
    pcs stonith create rsc_st_kdump fence_kdump pcmk_reboot_action="off" <b>pcmk_host_list="prod-cl1-0 prod-cl1-1</b>" timeout=30
    </code></pre>

4. **[1]** Configurez des niveaux stonith, de sorte que le mécanisme de clôture `fence_kdump` est engagé en premier.  
    <pre><code>
    pcs stonith create rsc_st_kdump fence_kdump pcmk_reboot_action="off" <b>pcmk_host_list="prod-cl1-0 prod-cl1-1</b>"
    pcs stonith level add 1 <b>prod-cl1-0</b> rsc_st_kdump
    pcs stonith level add 1 <b>prod-cl1-1</b> rsc_st_kdump
    pcs stonith level add 2 <b>prod-cl1-0</b> rsc_st_azure
    pcs stonith level add 2 <b>prod-cl1-1</b> rsc_st_azure
    # Check the stonith level configuration 
    pcs stonith level
    # Example output
    # Target: <b>prod-cl1-0</b>
    # Level 1 - rsc_st_kdump
    # Level 2 - rsc_st_azure
    # Target: <b>prod-cl1-1</b>
    # Level 1 - rsc_st_kdump
    # Level 2 - rsc_st_azure
    </code></pre>

5. **[A]** Autorisez les ports requis pour `fence_kdump` via le pare-feu
    ```
    firewall-cmd --add-port=7410/udp
    firewall-cmd --add-port=7410/udp --permanent
    ```

6. **[A]** Veillez à ce que le fichier d’images `initramfs` contienne les fichiers `fence_kdump` et `hosts`. Pour plus d’informations, consultez [Comment configurer fence_kdump dans un cluster Pacemaker Red Hat](https://access.redhat.com/solutions/2876971).   
    ```
    lsinitrd /boot/initramfs-$(uname -r)kdump.img | egrep "fence|hosts"
    # Example output 
    # -rw-r--r--   1 root     root          208 Jun  7 21:42 etc/hosts
    # -rwxr-xr-x   1 root     root        15560 Jun 17 14:59 usr/libexec/fence_kdump_send
    ```

7. **[A]** Réalisez la configuration `fence_kdump_nodes` dans `/etc/kdump.conf` afin d’éviter `fence_kdump`l’échec avec délai d’attente pour certaines versions `kexec-tools`. Pour plus d’informations, consultez [fence_kdump expire lorsque fence_kdump_nodes n’est pas spécifié avec kexec-tools version 2.0.15 ou ultérieure](https://access.redhat.com/solutions/4498151) et [fence_kdump échoue avec « délai d’expiration après X secondes » dans un cluster à haute disponibilité RHEL 6 ou 7 avec des versions de kexec-tools antérieures à 2.0.14](https://access.redhat.com/solutions/2388711). L’exemple de configuration pour un cluster à deux nœuds est présenté ci-dessous. Après avoir apporté une modification dans `/etc/kdump.conf`, l’image kdump doit être régénérée. Cela peut être obtenu en redémarrant le service `kdump`.  

    <pre><code>
    vi /etc/kdump.conf
    # On node <b>prod-cl1-0</b> make sure the following line is added
    fence_kdump_nodes  <b>prod-cl1-1</b>
    # On node <b>prod-cl1-1</b> make sure the following line is added
    fence_kdump_nodes  <b>prod-cl1-0</b>

    # Restart the service on each node
    systemctl restart kdump
    </code></pre>

8. Testez la configuration en bloquant un nœud. Pour plus d’informations, consultez [Comment configurer fence_kdump dans un cluster Pacemaker Red Hat](https://access.redhat.com/solutions/2876971).  

    > [!IMPORTANT]
    > Si le cluster est déjà utilisé en mode productif, planifiez le test en conséquence, car le blocage d’un nœud aura un impact sur l’application.   

    ```
    echo c > /proc/sysrq-trigger
    ```
## <a name="next-steps"></a>Étapes suivantes

* [Planification et implémentation de machines virtuelles Azure pour SAP][planning-guide]
* [Déploiement de machines virtuelles Azure pour SAP][deployment-guide]
* [Déploiement SGBD de machines virtuelles Azure pour SAP][dbms-guide]
* Pour savoir comment établir une haute disponibilité et planifier la récupération d’urgence de SAP HANA sur des machines virtuelles Azure, consultez [Haute disponibilité de SAP HANA sur des machines virtuelles Azure][sap-hana-ha]
