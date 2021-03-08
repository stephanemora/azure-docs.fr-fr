---
title: Répliquer des données via ExpressRoute avec la migration de serveur Azure Migrate
description: Utilisation d’Azure ExpressRoute pour une réplication avec la migration de serveur Azure Migrate
author: ms-deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: fe44ff423240cbe24d91cef0c0f4bb803ad8df98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743152"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Répliquer des données via ExpressRoute avec Azure Migrate : migration de serveur

Dans cet article, vous allez apprendre à configurer [Azure Migrate : migration de serveur](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) pour répliquer des données via un circuit ExpressRoute lors de la migration de serveurs vers Azure.

## <a name="understand-azure-expressroute-circuits"></a>Comprendre les circuits Azure ExpressRoute
Un circuit ExpressRoute (ER) vous permet de connecter votre infrastructure locale à Microsoft via un fournisseur de connectivité. Les circuits ExpressRoute peuvent être configurés pour utiliser le peering privé, le peering Microsoft, ou les deux. Consultez l’article sur les [circuits ExpressRoute et le peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) pour en savoir plus sur les différentes options de peering disponibles avec ExpressRoute.

L’outil de migration de serveur Azure Migrate vous aide à migrer des serveurs et des serveurs locaux d’autres clouds vers des machines virtuelles Azure. L’outil configure un flux de réplication en continu pour répliquer les données à partir des serveurs à migrer vers des disques managés dans votre abonnement Azure. Lorsque vous êtes prêt à migrer les serveurs, les données répliquées dans Azure sont utilisées pour migrer ces serveurs.

Les données répliquées à partir de vos serveurs locaux peuvent être configurées pour être envoyées à votre abonnement Azure via Internet (à l’aide d’une connexion chiffrée sécurisée) ou via une connexion ExpressRoute. Lorsque vous avez un grand nombre de serveurs à migrer, utiliser ExpressRoute pour la réplication peut vous aider à migrer les serveurs plus efficacement à l’aide de la bande passante approvisionnée disponible avec votre circuit ExpressRoute.

Cet article porte sur les points suivants :
> [!div class="checklist"]
>
> * Comment répliquer des données à l’aide d’un circuit ExpressRoute avec un peering privé.
> * Comment répliquer des données à l’aide d’un circuit ExpressRoute avec le peering Microsoft.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Répliquer des données à l’aide d’un circuit ExpressRoute avec un peering privé

> [!NOTE]
> La réplication via un circuit avec peering privé est actuellement prise en charge uniquement pour [la migration sans agent de machines virtuelles VMware vers Azure](./tutorial-migrate-vmware.md). Une prise en charge des points de terminaison privés pour d’autres [méthodes de réplication](./migrate-services-overview.md#azure-migrate-server-migration-tool) sera bientôt disponible.

Dans la méthode sans agent de migration de machines virtuelles VMware vers Azure, l’appliance Azure Migrate charge d’abord les données de réplication vers un compte de stockage (compte de stockage de cache) dans votre abonnement. Les données répliquées à partir du compte de stockage de cache sont ensuite déplacées vers des disques managés de réplica dans votre abonnement à l’aide du service Azure Migrate. Afin d’utiliser un circuit avec peering privé pour la réplication, vous allez créer et attacher un point de terminaison privé à l’utilisation du compte de stockage de cache. Les points de terminaison privés utilisent une ou plusieurs adresses IP privées de votre réseau virtuel (VNet), plaçant de fait le compte de stockage dans votre réseau virtuel Azure. Le point de terminaison privé permet à l’appliance Azure Migrate de se connecter au compte de stockage de cache à l’aide du peering privé ExpressRoute et de transférer les données directement sur l’adresse IP privée. <br/>  

![Processus de réplication](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - En plus des données de réplication, l’appliance Azure Migrate communique avec le service Azure Migrate pour ses activités de plan de contrôle, y compris l’orchestration de la réplication. Les communications du plan de contrôle entre l’appliance Azure Migrate et le service Azure Migrate se poursuivent via Internet sur le point de terminaison public du service Azure Migrate.
> - Le point de terminaison privé du compte de stockage doit être accessible à partir du réseau sur lequel l’appliance Azure Migrate est déployée.
> - Le DNS doit être configuré pour résoudre les requêtes DNS par l’appliance Azure Migrate pour le point de terminaison de service blob du compte de stockage du cache sur l’adresse IP privée du point de terminaison privé attaché au compte de stockage de cache.
> - Le compte de stockage de cache doit être accessible sur son point de terminaison public. (Le service Azure Migrate utilise le point de terminaison public du compte de stockage de cache pour déplacer des données du compte de stockage vers des disques managés de réplica.) 


### <a name="1-pre-requisites"></a>1. Conditions préalables

L’utilisateur Azure qui crée le point de terminaison privé doit disposer des autorisations suivantes sur le groupe de ressources et le réseau virtuel dans lesquels le point de terminaison privé sera créé.

**Cas d’usage** | **autorisations** 
--- | --- 
 Créer et gérer des points de terminaison privés. | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action  
|Attacher un point de terminaison privé à un réseau virtuel/sous-réseau.<br/>Cette autorisation est nécessaire sur le réseau virtuel où le point de terminaison privé sera créé.| Microsoft.Network/virtualNetworks/subnet/join/action  Microsoft.Network/virtualNetworks/join/action
|Lier le point de terminaison privé à un compte de stockage. <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|Créer une interface réseau et la joindre à un groupe de sécurité réseau. | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action (facultatif)
Créer et gérer des zones DNS privées.| Rôle Collaborateur de zone DNS privée <br/> _Ou_ <br/> Microsoft.Network/privateDnsZones/A/* <br/>  Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/>  Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="2-identify-the-cache-storage-account"></a>2. Identifier le compte de stockage de cache 
 
Azure Migrate crée automatiquement un compte de stockage de cache lorsque vous configurez la réplication (à l’aide de l’expérience Portail Azure) pour une machine virtuelle pour la première fois dans un projet Azure Migrate. Le compte de stockage est créé dans le même abonnement et le même groupe de ressources que ceux dans lesquels vous avez créé le projet Azure Migrate.

Pour créer et rechercher le compte de stockage :

1. Utilisez l’expérience Portail Azure pour Azure Migrate afin de répliquer une ou plusieurs machines virtuelles dans le projet.
2. Accédez au groupe de ressources du projet Azure Migrate.
3. Localisez le compte de stockage de cache en identifiant le préfixe **« lsa »** dans le nom du compte de stockage.

![Affichage du groupe de ressources](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Si vous disposez de plusieurs comptes de stockage avec le préfixe **« lsa »** dans votre groupe de ressources, vous pouvez vérifier le compte de stockage en accédant aux paramètres de réplication et au menu de configuration cible pour l’une des machines virtuelles de réplication dans le projet. <br/> 
> ![Vue d’ensemble des paramètres de réplication](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. Mettre à niveau un compte de stockage de cache vers Usage général v2 

Vous pouvez créer des points de terminaison privés uniquement sur un compte de stockage Usage général v2 (GPv2). Si le compte de stockage de cache n’est pas un compte de stockage GPv2, effectuez la mise à niveau vers GPv2 en procédant comme suit :

1. Accédez à votre compte de stockage.
2. Sélectionnez **Configuration**.
3. Sous **Type de compte**, sélectionnez **Mettre à niveau**.
4. Sous **Confirmer la mise à niveau**, saisissez le nom de votre compte.
5. Sélectionnez **Mettre à niveau** en bas de la page.

![Mettre à niveau un compte de stockage](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. Créer un point de terminaison privé pour le compte de stockage

1. Accédez à votre compte de stockage, sélectionnez **Mise en réseau** dans le menu de gauche, puis sélectionnez l’onglet **Connexions des points de terminaison privés**.  
2. Sélectionnez **+ Point de terminaison privé**.

    a. Dans la fenêtre **Créer un point de terminaison privé**, sélectionnez l’**abonnement** et le **groupe de ressources**. Nommez votre point de terminaison privé et sélectionnez la région du compte de stockage.  
    ![Fenêtre de configuration PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Dans l’onglet **Ressource**, indiquez le **nom de l’abonnement** dans lequel se trouve le compte de stockage. Choisissez **Microsoft.Storage/storageAccounts** comme **type de ressource**. Dans **Ressource**, indiquez le nom du compte de stockage de réplication de type GPv2. Sélectionnez **Blob** comme **Sous-ressource cible**.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. Sous l’onglet **Configuration**, sélectionnez le **réseau virtuel** et le **sous-réseau** du point de terminaison privé du compte de stockage.  

    > [!Note]
    > Le réseau virtuel doit contenir le point de terminaison de la passerelle ExpressRoute ou doit être connecté au réseau virtuel avec la passerelle ExpressRoute. 

    Dans la section **intégration à un DNS privé**, sélectionnez **Oui** et effectuez une intégration à une zone DNS privée. L’option **Oui** associe automatiquement la zone DNS au réseau au réseau virtuel sélectionné et ajoute les enregistrements DNS nécessaires à la résolution DNS des adresses IP et des noms de domaine complets créés pour le point de terminaison privé. En savoir plus sur les [zones DNS privées.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. Vous pouvez également ajouter des **étiquettes** à votre point de terminaison privé.  

    e. Passez à **Vérifier + créer** lorsque vous avez terminé la saisie des informations requises. Une fois la validation terminée, sélectionnez **Créer** pour créer le point de terminaison privé.

    > [!Note]
    > Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du compte de stockage, le point de terminaison privé sera automatiquement approuvé. Dans le cas contraire, le propriétaire doit approuver l’utilisation du point de terminaison privé. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Créer des zones DNS privées et ajouter des enregistrements DNS manuellement (facultatif)

Si vous n’avez pas sélectionné l’option d’intégration à une zone DNS privée lors de la création du point de terminaison privé, suivez les étapes de cette section pour créer manuellement une zone DNS privée. 

> [!Note]
> Si vous avez sélectionné **Oui** pour l’intégration à une zone DNS privée, vous pouvez ignorer cette section. 

1. Créez une zone DNS privée. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a.  Dans la page **Zones DNS privées**, sélectionnez le bouton **+Ajouter** pour commencer à créer une zone.  
    b.  Dans la page **Créer une zone DNS privée**, entrez les informations requises. Entrez le nom de la zone DNS privée, sous la forme _privatelink_.blob.core.windows.net. c. Passez à l’onglet **Vérifier + créer** pour vérifier et créer la zone DNS.

2. Liez la zone DNS privée à votre réseau virtuel.  

    La zone DNS privée créée ci-dessus doit être liée au réseau virtuel auquel le point de terminaison privé est attaché.

    a. Accédez à la zone DNS privée créée à l’étape précédente, puis, sur le côté gauche de la page, accédez aux liens de réseau virtuel. Sélectionnez le bouton **+Ajouter**.   
    b. Renseignez les champs obligatoires. Vous devez renseigner les champs **Subscription** (Abonnement) et **Virtual network** (Réseau virtuel) avec les paramètres correspondants du réseau virtuel auquel votre point de terminaison privé est attaché. Vous pouvez laisser les autres champs tels quels.

3. L’étape suivante consiste à ajouter des enregistrements DNS à la zone DNS. Ajoutez à votre zone DNS privée une entrée pour le nom de domaine complet du compte de stockage.

    a. Accédez à votre zone DNS privée, puis, sur le côté gauche de la page, accédez à la section **Vue d’ensemble**. Sélectionnez **+Jeu d’enregistrements** pour commencer à ajouter des enregistrements.  

    b. Dans la page **Ajouter un jeu d’enregistrements**, ajoutez une entrée pour le nom de domaine complet et l’adresse IP privée en tant qu’enregistrement de type A.

> [!Important]
> Vous pouvez avoir besoin de paramètres DNS supplémentaires pour résoudre l’adresse IP privée du point de terminaison privé du compte de stockage à partir de l’environnement source. [Consultez cet article](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) pour connaître la configuration DNS requise.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Répliquer des données à l’aide d’un circuit ExpressRoute avec le peering Microsoft

Vous pouvez utiliser le peering Microsoft ou un domaine de peering public existant (déconseillé pour les nouvelles connexions ExpressRoute) afin d’acheminer le trafic de réplication via un circuit ExpressRoute, comme illustré dans le diagramme ci-dessous.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Même si les données de réplication transitent via le circuit de peering Microsoft, vous aurez toujours besoin d’une connectivité Internet depuis le site local pour les autres communications (plan de contrôle) avec le service Azure Migrate. Il existe des URL supplémentaires, non accessibles via ExpressRoute, auxquelles l’hôte Hyper-V ou l’appliance de réplication doit avoir accès pour orchestrer le processus de réplication. Vous pouvez passer en revue les exigences en matière d’URL en fonction du scénario de migration, des [migrations sans agent VMware](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) ou des [migrations basées sur un agent](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance).  

Si vous utilisez un proxy sur votre site local et souhaitez utiliser ExpressRoute pour le trafic de réplication, vous devez configurer un contournement proxy pour les URL correspondantes sur l’appliance locale. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Configurer des règles de contournement proxy sur l’appliance Azure Migrate (pour les migrations sans agent VMware)

1. Connectez-vous (Bureau à distance) à l’appliance Azure Migrate.   
2. Ouvrez le fichier C:/ProgramData/MicrosoftAzure/Config/appliance.json à l’aide du bloc-notes.
3. Dans le fichier, remplacez la ligne « EnableProxyBypassList »: « false » par « EnableProxyBypassList »: « true ». Enregistrez les modifications, puis redémarrez l’appliance.
4. Après le redémarrage, lorsque vous ouvrez le gestionnaire de configuration de l’appliance, vous pouvez voir l’option de contournement proxy dans l’interface utilisateur de l’application web. Ajoutez les URL ci-dessous à la liste de contournement proxy.   
    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com
    - .*.blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Configurer des règles de contournement proxy sur l’appliance de réplication (pour les migrations basées sur un agent)

Suivez les étapes ci-dessous pour configurer la liste de contournement proxy sur le serveur de configuration et les serveurs de traitement :

1. [Téléchargez l’outil PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) pour accéder au contexte de l’utilisateur système.
2. Ouvrez Internet Explorer dans le contexte de l’utilisateur système en exécutant la ligne de commande suivante : psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
3. Ajouter les paramètres proxy dans Internet Explorer.
4. Dans la liste de contournement, ajoutez l’URL de stockage Azure.*.blob.core.windows.net.  

Les règles de contournement ci-dessus garantissent que le trafic de réplication peut transiter via ExpressRoute, tandis que la communication de gestion peut accéder à Internet via le proxy.  

En outre, vous devez publier des itinéraires dans le filtre de routage pour les communautés BGP suivantes afin que le trafic de réplication Azure Migrate traverse un circuit ExpressRoute au lieu d’Internet.  

- Communauté BGP régionale pour la région Azure source (région du projet Azure Migrate)
- Communauté BGP régionale pour la région Azure cible (région pour la migration)
- Communauté BGP pour Azure Active Directory (12076:5060)

En savoir plus sur les [filtres de routage](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) et la liste des [communautés BGP pour ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp). 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [circuits ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- En savoir plus sur les [domaines de routage ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- En savoir plus sur les [points de terminaison privés](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).