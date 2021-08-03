---
title: Répliquer des données via ExpressRoute avec la migration de serveur Azure Migrate
description: Utilisez Azure ExpressRoute pour la réplication avec la migration de serveur Azure Migrate.
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: e26434ae1ff2f9d8829d3665807f7d9916233833
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2021
ms.locfileid: "110792235"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Répliquer des données via ExpressRoute avec Azure Migrate : migration de serveur

Dans cet article, vous allez apprendre à configurer l’outil [Azure Migrate : migration de serveur](./migrate-services-overview.md#azure-migrate-server-migration-tool) pour répliquer des données sur un circuit ExpressRoute lorsque vous migrez des serveurs vers Azure.

## <a name="understand-azure-expressroute-circuits"></a>Comprendre les circuits Azure ExpressRoute

Les circuits ExpressRoute vous permettent de connecter votre infrastructure locale à Microsoft par l’intermédiaire d’un fournisseur de connectivité. Ils peuvent être configurés pour utiliser le peering privé, le peering Microsoft ou les deux. Pour plus d’informations sur les options de peering avec ExpressRoute, consultez [Circuits ExpressRoute et peering](../expressroute/expressroute-circuit-peerings.md#peeringcompare).

L’outil Azure Migrate : migration de serveur permet de migrer des serveurs locaux et des serveurs d’autres clouds vers des Machines virtuelles Azure. Il configure un flux de réplication en continu pour répliquer les données des serveurs à migrer vers des disques managés de votre abonnement Azure. Les données répliquées dans Azure seront utilisées au moment de migrer les serveurs.

Vous pouvez configurer les données répliquées à partir de vos serveurs locaux pour les envoyer à votre abonnement Azure par Internet ou via une connexion ExpressRoute. Les données envoyées par Internet utilisent une connexion chiffrée sécurisée. Si vous avez un grand nombre de serveurs à migrer, vous pouvez utiliser ExpressRoute pour la réplication : la bande passante provisionnée disponible avec votre circuit ExpressRoute vous permet d’effectuer la migration plus efficacement.

Dans cet article, vous allez apprendre à répliquer des données dans deux cas de figure :
> [!div class="checklist"]
>
> * Un circuit ExpressRoute avec peering privé
> * Un circuit ExpressRoute avec peering Microsoft

## <a name="replicate-data-by-using-an-expressroute-circuit-with-private-peering"></a>Réplication des données à l’aide d’un circuit ExpressRoute avec peering privé

> [!Note]
> Cet article montre comment effectuer une réplication sur un circuit de peering privé pour la [migration sans agent de machines virtuelles VMware vers Azure](./tutorial-migrate-vmware.md). Pour exploiter la prise en charge [d’autres méthodes de réplication](./migrate-services-overview.md#azure-migrate-server-migration-tool) du point de terminaison privé, consultez [Azure Migrate avec des points de terminaison privés](./how-to-use-azure-migrate-with-private-endpoints.md).
 
Dans la méthode sans agent de migration de machines virtuelles VMware vers Azure, l’appliance Azure Migrate commence par charger les données de réplication dans un compte de stockage (compte de stockage de cache) de votre abonnement. Elle déplace ensuite les données répliquées du compte de stockage de cache vers des disques managés par un réplica de votre abonnement.

Pour utiliser un circuit avec peering privé lors de la réplication, vous allez créer et attacher un point de terminaison privé au compte de stockage de cache. Les points de terminaison privés emploient une ou plusieurs adresses IP privées de votre réseau virtuel : le compte de stockage est donc placé de fait dans votre réseau virtuel Azure. Le point de terminaison privé permet à l’appliance Azure Migrate de se connecter au compte de stockage de cache à l’aide du peering privé ExpressRoute. Les données peuvent ensuite être transférées directement sur l’adresse IP privée. <br/>

![Capture d’écran montrant le processus de réplication.](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
> - En plus des données de réplication, l’appliance Azure Migrate communique avec le service Azure Migrate pour ses activités de plan de contrôle, notamment l’orchestration de la réplication. Les communications du plan de contrôle se poursuivent entre l’appliance et le service Azure Migrate par Internet sur le point de terminaison public du service Azure Migrate.
> - Le point de terminaison privé du compte de stockage doit être accessible à partir du réseau sur lequel l’appliance Azure Migrate est déployée.
> - Le DNS doit être configuré pour résoudre les requêtes DNS par l’appliance Azure Migrate pour le point de terminaison de service blob du compte de stockage du cache sur l’adresse IP privée du point de terminaison privé attaché au compte de stockage de cache.
> - Le compte de stockage de cache doit être accessible sur son point de terminaison public. Le service Azure Migrate utilise le point de terminaison public du compte de stockage de cache pour déplacer des données du compte de stockage vers des disques managés par un réplica.

### <a name="prerequisites"></a>Prérequis

Vous devez disposer des autorisations suivantes sur le groupe de ressources et le réseau virtuel où le point de terminaison privé sera créé.

Cas d’utilisation | Autorisations
--- | --- 
 Créer et gérer des points de terminaison privés. | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action 
|Attacher un point de terminaison privé à un réseau virtuel ou à un sous-réseau.<br/>Cette autorisation est obligatoire sur le réseau virtuel où le point de terminaison privé sera créé.| Microsoft.Network/virtualNetworks/subnet/join/action <br/> Microsoft.Network/virtualNetworks/join/action
|Lier le point de terminaison privé à un compte de stockage. <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|Créer une interface réseau et la joindre à un groupe de sécurité réseau. | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action (facultatif)
Créer et gérer des zones DNS privées.| Rôle Collaborateur de zone DNS privée <br/> _Ou_ <br/> Microsoft.Network/privateDnsZones/A/* <br/> Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="identify-the-cache-storage-account"></a>Identifier le compte de stockage de cache

 Azure Migrate crée automatiquement un compte de stockage de cache lorsque vous configurez la réplication (à l’aide de l’expérience Portail Azure) pour une machine virtuelle pour la première fois dans un projet Azure Migrate. Le compte de stockage est créé dans le même abonnement et le même groupe de ressources que ceux dans lesquels vous avez créé le projet Azure Migrate.

Pour créer et rechercher le compte de stockage :

1. Utilisez le Portail Azure pour répliquer une ou plusieurs machines virtuelles dans le projet Azure Migrate.
1. Accédez au groupe de ressources du projet Azure Migrate.
1. Localisez le compte de stockage de cache en identifiant le préfixe **lsa** dans son nom.

   ![Capture d’écran montrant une vue des groupes de ressources.](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> Si vous disposez de plusieurs comptes de stockage comportant le préfixe **lsa** dans votre groupe de ressources, vous pouvez les vérifier en accédant aux paramètres de réplication et au menu de configuration cible de l’une des machines virtuelles de réplication du projet.
>
> ![Capture d’écran montrant une vue d’ensemble des paramètres de réplication.](./media/replicate-using-expressroute/storage-account.png)

### <a name="upgrade-the-cache-storage-account-to-general-purpose-v2"></a>Mise à niveau du compte de stockage de cache vers v2 universel

Seul un compte de stockage v2 universel offre la possibilité de créer des points de terminaison privés. Si le compte de stockage de cache n’est pas un compte de stockage v2 universel, mettez-le à niveau.

1. Accédez à votre compte de stockage.
1. Sélectionnez **Configuration**.
1. Sous **Type de compte**, sélectionnez **Mettre à niveau**.
1. Sous **Confirmer la mise à niveau**, entrez le nom de votre compte.
1. Sélectionnez **Mettre à niveau** en bas de la page.

   ![Capture d’écran montrant comment mettre à niveau un compte de stockage.](./media/replicate-using-expressroute/upgrade-storage-account.png) 

### <a name="create-a-private-endpoint-for-the-storage-account"></a>Créer un point de terminaison privé pour le compte de stockage

1. Accédez à votre compte de stockage, sélectionnez **Mise en réseau** dans le menu de gauche, puis sélectionnez l’onglet **Connexions des points de terminaison privés**.
1. Sélectionnez **+ Point de terminaison privé**.

    1. Dans la fenêtre **Créer un point de terminaison privé**, sélectionnez **l’Abonnement** et le **Groupe de ressources**. Donnez un nom à votre point de terminaison privé, puis sélectionnez la région du compte de stockage.
     
       ![Capture d’écran montrant la fenêtre de configuration d’un point de terminaison privé.](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    1. Dans l’onglet **Ressource**, entrez le **Nom de l’abonnement** dans lequel se trouve le compte de stockage. Sélectionnez **Microsoft.Storage/storageAccounts** comme **Type de ressource**. Dans **Ressource**, entrez le nom du compte de stockage de réplication de type v2 universel. Sélectionnez **Blob** comme **Sous-ressource cible**.
     
       ![Capture d’écran montrant les paramètres de point de terminaison privé du compte de stockage.](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    1. Dans l’onglet **Configuration**, sélectionnez le **Réseau virtuel** et le **Sous-réseau** du point de terminaison privé du compte de stockage.

       > [!Note]
       > Le réseau virtuel doit contenir le point de terminaison de la passerelle ExpressRoute ou être connecté au réseau virtuel avec la passerelle ExpressRoute.

       Dans la section **Intégration à un DNS privé**, sélectionnez **Oui** et effectuez une intégration à une zone DNS privée. L’option **Oui** permet d’associer automatiquement la zone DNS au réseau virtuel sélectionné. Elle a également pour effet d’ajouter les enregistrements DNS nécessaires à la résolution DNS des adresses IP et des noms de domaine complets (FQDN, fully qualified domain name) créés pour le point de terminaison privé. Pour plus d’informations, consultez [Zones DNS privées](../dns/private-dns-overview.md).

       ![Capture d’écran montrant les zones DNS privées.](./media/replicate-using-expressroute/private-dns-zone.png)

    1. Vous pouvez également ajouter des **étiquettes** à votre point de terminaison privé.

    1. Une fois que vous avez fini d’entrer les informations nécessaires, sélectionnez l’onglet **Vérifier + créer**. Une fois la validation terminée, sélectionnez **Créer** pour créer le point de terminaison privé.

> [!Note]
> Si l’utilisateur qui crée le point de terminaison privé est également le propriétaire du compte de stockage, le point de terminaison privé est automatiquement approuvé. Dans le cas contraire, ce dernier doit être approuvé par le propriétaire pour pouvoir être utilisé.

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Création de zones DNS privées et ajout manuel d’enregistrements DNS (facultatif)

Si vous n’avez pas sélectionné l’option d’intégration à une zone DNS privée lors de la création du point de terminaison privé, vous devez créer manuellement une zone DNS privée.

> [!Note]
> Si vous avez sélectionné **Oui** pour l’intégration à une zone DNS privée, vous pouvez ignorer cette section.

Pour créer manuellement une zone DNS privée, procédez comme suit :

1. Sélectionnez **Zones DNS privées**.

    ![Capture d’écran montrant la création d’une zone DNS privée.](./media/replicate-using-expressroute/create-private-dns.png)

    1. Sur la page **Zones DNS privées**, sélectionnez **+ Ajouter** pour créer une zone.
    1. Dans la page **Créer une zone DNS privée**, entrez les informations requises. Entrez le nom de la zone DNS privée sous la forme **_privatelink_.blob.core.windows.net**.
    1. Dans l’onglet **Vérifier + créer**, vérifiez et créez la zone DNS.

1. Liez la zone DNS privée à votre réseau virtuel.

    La zone DNS privée que vous avez créée doit être liée au réseau virtuel auquel le point de terminaison privé est attaché.

    1. Accédez à la zone DNS privée créée à l’étape précédente, puis aux liens de réseau virtuel sur le côté gauche de la page. Sélectionnez **Ajouter**.
    1. Renseignez les champs obligatoires. Vous devez renseigner les champs **Subscription** (Abonnement) et **Virtual network** (Réseau virtuel) avec les paramètres correspondants du réseau virtuel auquel votre point de terminaison privé est attaché. Vous pouvez laisser les autres champs tels quels.

1. L’étape suivante consiste à ajouter des enregistrements DNS à la zone DNS. Ajoutez une entrée pour le nom FQDN du compte de stockage dans votre zone DNS privée.

    1. Accédez à votre zone DNS privée, puis à la section **Vue d’ensemble** sur le côté gauche de la page. Sélectionnez **+ Enregistrement** pour commencer à ajouter des enregistrements.
    1. Sur la page **Ajouter un jeu d’enregistrements**, ajoutez une entrée pour le nom FQDN et l’adresse IP privée sous la forme d’un enregistrement de type A.

> [!Important]
> Des paramètres DNS supplémentaires peuvent se révéler nécessaires pour résoudre l’adresse IP privée du point de terminaison privé du compte de stockage à partir de l’environnement source. Pour connaître la configuration DNS nécessaire, consultez [Configuration DNS du point de terminaison privé Azure](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).  

### <a name="verify-network-connectivity-to-the-storage-account"></a>Vérification de la connectivité réseau au compte de stockage

Pour valider la connexion de liaison privée, effectuez une résolution DNS du point de terminaison de la ressource du compte de stockage de cache sur la machine virtuelle locale qui héberge l’appliance Azure Migrate. Vérifiez que vous obtenez une adresse IP privée.

Voici un exemple illustrant la résolution DNS du compte de stockage de cache. 

- Entrez nslookup _nom-compte-stockage_.blob.core.windows.nett. Remplacez <nom-compte-stockage> par le nom du compte de stockage de cache créé par Azure Migrate.  

    Vous recevrez un message similaire à celui ci :  

   ![Exemple de résolution DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- L’adresse IP privée 10.1.0.5 est retournée pour le compte de stockage. Cette adresse doit appartenir au point de terminaison privé du compte de stockage. 

Si la résolution DNS est incorrecte, procédez comme suit :  

- **Conseil :** Vous pouvez mettre à jour manuellement les enregistrements DNS de votre environnement source en modifiant le fichier d’hôtes DNS sur votre appliance locale avec le lien FQDN du compte de stockage (_nom-compte-stockage_.blob.core.windows.net) et l’adresse IP privée associée. Cette option n’est recommandée qu’à des fins de test. 
- Si vous utilisez un DNS personnalisé, vérifiez vos paramètres DNS personnalisés et confirmez que la configuration DNS est correcte. Pour obtenir de l’aide, consultez [Vue d’ensemble du point de terminaison privé : configuration DNS](../private-link/private-endpoint-overview.md#dns-configuration). 
- Si vous utilisez des serveurs DNS fournis par Azure, utilisez ce guide comme référence pour la [résolution avancée des problèmes](./troubleshoot-network-connectivity.md#validate-the-private-dns-zone).   

## <a name="replicate-data-by-using-an-expressroute-circuit-with-microsoft-peering"></a>Réplication des données à l’aide d’un circuit ExpressRoute avec peering Microsoft

Vous pouvez utiliser le peering Microsoft ou un domaine de peering public existant (déconseillé pour les nouvelles connexions ExpressRoute) pour acheminer le trafic de réplication via un circuit ExpressRoute.

![Diagramme montrant la réplication avec le peering Microsoft.](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Même si les données de réplication transitent par le circuit de peering Microsoft, une connectivité Internet est nécessaire à partir du site local pour les autres communications (plan de contrôle) avec Azure Migrate. D’autres URL ne sont pas accessibles via ExpressRoute. L’appliance de réplication ou l’hôte Hyper-V a besoin d’accéder aux URL pour orchestrer le processus de réplication. Passez en revue les exigences en matière d’URL en fonction de votre scénario de migration : [migrations sans agent VMware](./migrate-appliance.md#public-cloud-urls) ou [migrations avec agent](./migrate-replication-appliance.md).

Si vous vous servez d’un proxy sur votre site local et souhaitez utiliser ExpressRoute pour le trafic de réplication, configurez un contournement du proxy pour les URL correspondantes sur l’appliance locale.

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Configurer des règles de contournement proxy sur l’appliance Azure Migrate (pour les migrations sans agent VMware)

1. Établissez une connexion Bureau à distance avec l’appliance Azure Migrate.
1. Ouvrez le fichier *C:/ProgramData/MicrosoftAzure/Config/appliance.json* dans le Bloc-notes.
1. Dans le fichier, remplacez la ligne `"EnableProxyBypassList": "false"` par `"EnableProxyBypassList": "true"`. Enregistrez les modifications, puis redémarrez l’appliance.
1. Après le redémarrage, lorsque vous ouvrez le gestionnaire de configuration de l’appliance, vous pouvez voir l’option de contournement du proxy dans l’interface utilisateur de l’application web. Ajoutez les URL suivantes à la liste de contournement du proxy :

    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com
    - .*.blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Configurer des règles de contournement proxy sur l’appliance de réplication (pour les migrations basées sur un agent)

Pour configurer la liste de contournement du proxy sur le serveur de configuration et les serveurs de traitement, procédez comme suit :

1. Téléchargez [l’outil PsExec](/sysinternals/downloads/psexec) pour accéder au contexte de l’utilisateur système.
1. Ouvrez Internet Explorer dans le contexte de l’utilisateur système en exécutant la ligne de commande suivante : `psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"`.
1. Ajoutez des paramètres de proxy dans Internet Explorer.
1. Dans la liste de contournement, ajoutez les URL *.blob.core.windows.net, *.hypervrecoverymanager.windowsazure.com et *.backup.windowsazure.com. 

Les règles de contournement ci-dessus garantissent que le trafic de réplication peut transiter par ExpressRoute, tandis que la communication de gestion peut accéder à Internet via le proxy.

Vous devez également publier des itinéraires dans le filtre de routage pour les communautés BGP suivantes afin que le trafic de réplication Azure Migrate transite par un circuit ExpressRoute au lieu d’Internet :

- Communauté BGP régionale pour la région Azure source (région du projet Azure Migrate)
- Communauté BGP régionale pour la région Azure cible (région pour la migration)
- Communauté BGP pour Azure Active Directory (12076:5060)

Pour plus d’informations, consultez [Filtres de routage](../expressroute/how-to-routefilter-portal.md) et la liste des [Communautés BGP pour ExpressRoute](../expressroute/expressroute-routing.md#bgp).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Circuits ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
- [Domaines de routage ExpressRoute](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- [Points de terminaison privés](../private-link/private-endpoint-overview.md)