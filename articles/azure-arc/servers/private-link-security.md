---
title: Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Arc
description: Apprenez à utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Arc.
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 53bd9310c193d4fad1d550fbf33446754c30ecd6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631511"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-arc"></a>Utiliser Azure Private Link pour connecter en toute sécurité des réseaux à Azure Arc

[Azure Private Link](../../private-link/private-link-overview.md) vous permet de lier en toute sécurité les services PaaS Azure à votre réseau virtuel à l’aide de points de terminaison privés. Pour de nombreux services, il vous suffit de configurer un point de terminaison par ressource. Vous pouvez ainsi connecter vos serveurs locaux ou multiclouds à Azure Arc et envoyer tout le trafic via Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) ou une [connexion VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) de site à site au lieu d'utiliser des réseaux publics.

À partir des serveurs Azure Arc, vous pouvez utiliser un modèle Étendue de liaison privée pour permettre à plusieurs serveurs ou machines de communiquer avec leurs ressources Azure Arc via un même point de terminaison privé.

Cet article explique quand utiliser une Étendue de liaison privée Azure Arc (préversion) et comment la configurer.

> [!NOTE]
> La fonctionnalité Étendue de liaison privée Azure Arc (préversion) est disponible dans toutes les régions cloud commerciales, mais pas dans le cloud US Government.

## <a name="advantages"></a>Avantages

Grâce à Azure Private Link, vous pouvez :

- vous connecter en privé à Azure Arc sans ouvrir d'accès au réseau public ;
- Vérifiez que les données de la machine ou du serveur Azure Arc ne sont accessibles que via des réseaux privés autorisés. Cela s'applique également aux données provenant des [extensions de machines virtuelles](manage-vm-extensions.md) installées sur la machine ou le serveur et qui fournissent un support de gestion et de surveillance post-déploiement ;
- empêcher l'exfiltration de données de vos réseaux privés en définissant des serveurs Azure Arc spécifiques et d'autres ressources de services Azure, comme Azure Monitor, qui se connectent via votre point de terminaison privé ;
- connecter en toute sécurité votre réseau privé local à Azure Arc à l'aide d'ExpressRoute et de Private Link ;
- Conservez tout le trafic au sein du réseau principal Microsoft Azure.

Pour plus d’informations, consultez [Principaux avantages d’Azure Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Fonctionnement

L'Étendue de liaison privée (préversion) connecte des points de terminaison privés (et les réseaux virtuels qui les hébergent) à une ressource Azure, dans ce cas des serveurs Azure Arc. Lorsque vous activez l'une des extensions de machine virtuelle prises en charge par les serveurs Azure Arc, comme Azure Automation - Update Management ou Azure Monitor, ces ressources connectent d'autres ressources Azure. Par exemple :

- Espace de travail Log Analytics, requis pour Azure Automation - Update Management, Azure Automation - Suivi des modifications et inventaire, Azure Monitor - Insights sur les machines virtuelles et la collecte de journaux Azure Monitor avec l'agent Log Analytics
- Compte Azure Automation, requis pour Update Management et Suivi des modifications et inventaire
- Azure Key Vault
- Stockage Blob Azure, requis pour l'extension de script personnalisé

:::image type="content" source="./media/private-link-security/private-link-topology.png" alt-text="Schéma de la topologie de base des ressources" border="true":::

La connectivité aux autres ressources Azure à partir d'un serveur Azure Arc mentionné précédemment nécessite la configuration de Private Link pour chaque service. Pour plus d'informations, consultez les rubriques suivantes afin de configurer Private Link pour [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) ou [Stockage Blob Azure](../../private-link/tutorial-private-endpoint-storage-portal.md).

> [!IMPORTANT]
> Azure Private Link est maintenant en disponibilité générale. Le point de terminaison privé et le service Private Link (service derrière l’équilibreur de charge standard) sont tous les deux en disponibilité générale. Différentes versions d’Azure PaaS seront intégrées à Azure Private Link à différentes échéances. Consultez [Disponibilité de Private Link](../../private-link/availability.md) pour obtenir l’état précis d’Azure PaaS sur Private Link. Pour en savoir plus sur les limitations connues, consultez [Point de terminaison privé](../../private-link/private-endpoint-overview.md#limitations) et [Service Liaison privée](../../private-link/private-link-service-overview.md#limitations).

* Le point de terminaison privé de votre réseau virtuel lui permet d'atteindre les points de terminaison des serveurs Azure Arc par le biais d'adresses IP privées à partir du pool de votre réseau, au lieu d'utiliser les adresses IP publiques de ces points de terminaison. Cela vous permet de continuer à utiliser vos ressources de serveurs Azure Arc sans ouvrir votre réseau virtuel à un trafic sortant non requis.

* Le trafic entre le point de terminaison privé et vos ressources passe par le réseau principal Microsoft Azure et n'est pas acheminé vers des réseaux publics.

* Vous pouvez configurer chacun de vos composants pour qu'ils autorisent ou refusent l'ingestion et les requêtes en provenance de réseaux publics. Vous bénéficiez ainsi d’une protection au niveau des ressources, ce qui vous permet de contrôler le trafic vers des ressources spécifiques.

## <a name="restrictions-and-limitations"></a>Limitations et restrictions

L'objet de l’Étendue de liaison privée des serveurs Azure Arc présente un certain nombre de limites à prendre en compte lors de la planification de votre configuration Private Link.

- Vous ne pouvez associer qu'une seule Étendue de liaison privée Azure Arc à un réseau virtuel.

- Une machine ou un serveur Azure Arc ne peut se connecter qu'à une seule Étendue de liaison privée de serveurs Azure Arc.

- Toutes les machines locales doivent utiliser le même point de terminaison privé en résolvant les bonnes informations de point de terminaison privé (nom d'enregistrement FQDN et adresse IP privée) à l'aide du même redirecteur DNS. Pour plus d'informations, consultez [Configuration DNS des points de terminaison privés Azure](../../private-link/private-endpoint-dns.md).

- Le serveur Azure Arc et l'Étendue de liaison privée Azure Arc doivent se trouver dans la même région Azure. Le point de terminaison privé et le réseau virtuel doivent également se trouver dans la même région Azure, mais cette région peut être différente de celle de votre étendue de lien privée Azure arc et du serveur Azure Arc.

- Dans le cadre de la préversion, le trafic vers les étiquettes de service Azure Active Directory et Azure Resource Manager doit être autorisé via votre pare-feu de réseau local.

- D'autres services Azure, comme Azure Monitor, ont besoin de leurs propres points de terminaison privés sur votre réseau virtuel.

- La fonctionnalité Étendue de liaison privée des serveurs Azure Arc n'est actuellement pas disponible dans les régions Azure US Government.

## <a name="planning-your-private-link-setup"></a>Planification de votre configuration Private Link

Pour connecter votre serveur à Azure Arc via une liaison privée, vous devez configurer votre réseau comme suit :

1. Établissez une connexion entre votre réseau local et un réseau virtuel Azure à l'aide d'un [VPN de site à site](../../vpn-gateway/tutorial-site-to-site-portal.md) ou d'un [circuit ExpressRoute](../../expressroute/expressroute-howto-linkvnet-arm.md).

1. Déployez une Étendue de liaison privée Azure Arc (préversion) contrôlant les machines ou serveurs qui peuvent communiquer avec Azure Arc sur des points de terminaison privés, et associez-la à votre réseau virtuel Azure à l'aide d'un point de terminaison privé.

1. Mettez à jour la configuration DNS sur votre réseau local afin de résoudre les adresses des points de terminaison privés.

1. Configurez votre pare-feu local pour autoriser l'accès à Azure Active Directory et Azure Resource Manager. Il s'agit d'une étape temporaire qui ne sera pas nécessaire lorsque les points de terminaison privés de ces services seront disponibles en préversion.

1. Associez les machines ou serveurs inscrits auprès des serveurs Azure Arc à l'étendue de liaison privée.

1. Si vous le souhaitez, déployez des points de terminaison privés pour d'autres services Azure gérant votre machine ou votre serveur, comme :

    - Azure Monitor
    - Azure Automation
    - Stockage Blob Azure
    - Azure Key Vault

Cet article part du principe que vous avez déjà configuré votre circuit ExpressRoute ou votre connexion VPN de site à site.

## <a name="network-configuration"></a>Configuration réseau

Les serveurs Azure Arc s'intègrent à différents services Azure pour mettre en place la gestion et la gouvernance cloud sur vos machines ou serveurs hybrides. La plupart de ces services offrent déjà des points de terminaison privés, mais vous devez configurer votre pare-feu et vos règles d'acheminement de manière à autoriser l'accès à Azure Active Directory et Azure Resource Manager sur Internet jusqu'à ce que ces services offrent des points de terminaison privés.

Pour ce faire, deux méthodes sont disponibles :

- Si votre réseau est configuré pour acheminer l'ensemble du trafic Internet via le VPN Azure ou le circuit ExpressRoute, vous pouvez configurer le groupe de sécurité réseau (NSG) associé à votre sous-réseau dans Azure de manière à autoriser l'accès TCP 443 (HTTPS) sortant à Azure AD et Azure à l'aide d'[étiquettes de service](../../virtual-network/service-tags-overview.md). Les règles NSG doivent se présenter comme suit :

    |Paramètre |Règle Azure AD | Règle Azure |
    |--------|--------------|-----------------------------|
    |Source |Réseau virtuel |Réseau virtuel |
    |Source port ranges |* |* |
    |Destination |Étiquette du service |Étiquette du service |
    |Identification de destination |AzureActiveDirectory |AzureResourceManager |
    |Plages de ports de destination |443 |443 |
    |Protocol |TCP |TCP |
    |Action |Autoriser |Autoriser |
    |Priority |150 (doit être inférieure à toutes les règles qui bloquent l'accès à Internet) |151 (doit être inférieure à toutes les règles qui bloquent l'accès à Internet) |
    |Name |AllowAADOutboundAccess |AllowAzOutboundAccess |

- Configurez le pare-feu de votre réseau local de manière à autoriser l'accès TCP 443 (HTTPS) sortant à Azure AD et Azure à l'aide des fichiers d'étiquettes de service téléchargeables. Le fichier JSON contient toutes les plages d'adresses IP publiques utilisées par Azure AD et Azure, et est mis à jour tous les mois pour tenir compte des éventuels changements. L'étiquette de service d'Azure AD est `AzureActiveDirectory` et celle d'Azure est `AzureResourceManager`. Contactez votre administrateur réseau et votre fournisseur de pare-feu réseau pour savoir comment configurer vos règles de pare-feu.

Consultez le diagramme visuel de la section [Fonctionnement](#how-it-works) pour en savoir plus sur les flux de trafic réseau.

## <a name="create-a-private-link-scope"></a>Créer une Étendue de liaison privée

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sur le portail Azure, accédez à **Créer une ressource** et recherchez **Étendue de liaison privée Azure Arc**. Vous pouvez également utiliser le lien suivant pour ouvrir la page [Étendue de liaison privée Azure Arc](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2FprivateLinkScopes) sur le portail.

    :::image type="content" source="./media/private-link-security/find-scope.png" alt-text="Rechercher une Étendue de liaison privée" border="true":::

1. Sélectionnez **Create** (Créer).

1. Choisissez un abonnement et un groupe de ressources. Dans le cadre de la préversion, votre réseau virtuel et vos serveurs Azure Arc doivent être associés au même abonnement que l'Étendue de liaison privée Azure Arc.

1. Donnez un nom à l'Étendue de liaison privée Azure Arc. Il est préférable d'utiliser un nom explicite et clair.

   Si vous le souhaitez, vous pouvez exiger que chaque machine ou serveur Azure Arc associé à cette Étendue de liaison privée Azure Arc (préversion) envoie des données au service via le point de terminaison privé. Si vous sélectionnez **Activer l'accès au réseau public**, les machines ou serveurs associés à cette Étendue de liaison privée Azure Arc (préversion) peuvent communiquer avec le service sur des réseaux privés ou publics. Vous pouvez modifier ce paramètre après avoir créé l'étendue si vous changez d'avis.

1. Sélectionnez **Vérifier + créer**.

    :::image type="content" source="./media/private-link-security/create-private-link-scope.png" alt-text="Créer une Étendue de liaison privée" border="true":::

1. Laissez le processus de validation se terminer, puis sélectionnez **Créer**.

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

Une fois votre Étendue de liaison privée Azure Arc (préversion) créée, vous devez la connecter à un ou plusieurs réseaux virtuels à l'aide d'un point de terminaison privé. Le point de terminaison privé expose l'accès aux services Azure Arc sur une adresse IP privée dans l'espace d'adressage de votre réseau virtuel.

1. Dans votre ressource d’étendue, sélectionnez **Connexions de point de terminaison privé** dans le menu gauche de la ressource. Sélectionnez **Ajouter** pour lancer le processus de création du point de terminaison. Vous pouvez également approuver ici des connexions qui ont été démarrées dans le centre Azure Private Link en les sélectionnant puis en choisissant **Approuver**.

    :::image type="content" source="./media/private-link-security/create-private-endpoint.png" alt-text="Créer un point de terminaison privé" border="true":::

1. Choisissez l’abonnement, le groupe de ressources et le nom du point de terminaison, ainsi que la région dans laquelle il doit résider. La région doit être la même que celle du réseau virtuel auquel vous vous connectez.

1. Sélectionnez **Suivant : Ressource**.

1. Sur la page **Ressource**,

   a. Sélectionnez l'**Abonnement** auquel est associée votre ressource Étendue de liaison privée Azure Arc.

   b. Dans le champ **Type de ressource**, choisissez **Microsoft.HybridCompute/privateLinkScopes**.

   c. Dans la liste déroulante **Ressource**, choisissez l'étendue de liaison privée que vous avez créée précédemment.

   d. Sélectionnez **Suivant : Configuration >** .

    :::image type="content" source="./media/private-link-security/create-private-endpoint-configuration.png" alt-text="Finaliser la création du point de terminaison privé" border="true":::

1. Sur la page **Configuration**,

   a. Choisissez le **réseau virtuel** et le **sous-réseau** que vous souhaitez connecter à vos ressources Azure Monitor. 

   b. Choisissez **Oui** pour **Intégrer à une zone DNS privée**, et laissez-le créer automatiquement une nouvelle zone DNS privée. Les zones DNS réelles peuvent différer de ce que montre la capture d’écran ci-dessous.

     > [!NOTE]
     > Si vous choisissez **Non** et préférez gérer les enregistrements DNS manuellement, commencez par configurer votre liaison privée, avec ce point de terminaison privé et la configuration de l'étendue privée. Ensuite, configurez votre DNS conformément aux instructions de la rubrique [Configuration DNS des points de terminaison privés Azure](../../private-link/private-endpoint-dns.md). Veillez à ne pas créer d’enregistrements vides en préparation de votre configuration de liaison privée. Les enregistrements DNS que vous créez peuvent remplacer les paramètres existants et avoir un impact sur votre connectivité avec les serveurs Azure Arc.

   c.    Sélectionnez **Revoir + créer**.

   d.    Laissez le processus de validation se terminer.

   e.    Sélectionnez **Create** (Créer).

## <a name="configure-on-premises-dns-forwarding"></a>Configurer la redirection DNS locale

Vos machines ou serveurs locaux doivent être en mesure de résoudre les enregistrements DNS des liaisons privées en adresses IP de points de terminaison privés. La configuration de ces éléments varie selon que vous utilisez des zones DNS privées Azure pour gérer les enregistrements DNS ou votre propre serveur DNS local. Elle dépend également du nombre de serveurs que vous configurez.

### <a name="dns-configuration-using-azure-integrated-private-dns-zones"></a>Configuration DNS à l'aide de zones DNS privées intégrées à Azure

Si vous configurez des zones DNS privées pour les serveurs Azure Arc et la configuration des invités lors de la création du point de terminaison privé, vos machines ou serveurs locaux doivent être en mesure de transmettre les requêtes DNS aux serveurs Azure DNS intégrés afin de résoudre correctement les adresses des points de terminaison privés. Vous avez besoin dans Azure d'un redirecteur DNS (soit une machine virtuelle spécialement conçue, soit une instance de Pare-feu Azure avec proxy DNS activé), après quoi vous pouvez configurer votre serveur DNS local pour qu'il transmette les requêtes à Azure afin de résoudre les adresses IP des points de terminaison privés.

La documentation relative aux points de terminaison privés fournit des conseils sur la configuration des [charges de travail locales à l'aide d'un redirecteur DNS](../../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

### <a name="manual-dns-server-configuration"></a>Configuration manuelle du serveur DNS

Si vous avez choisi de ne pas utiliser les zones DNS privées Azure lors de la création du point de terminaison privé, vous devrez créer les enregistrements DNS requis sur votre serveur DNS local.

1. Accédez au portail Azure.

1. Accédez au point de terminaison privé associé à votre réseau virtuel et à votre étendue de liaison privée.

1. Dans le volet de gauche, sélectionnez **Configuration DNS** pour afficher la liste des enregistrements DNS et des adresses IP correspondantes à configurer sur votre serveur DNS. Les noms de domaine complets (FQDN) et les adresses IP changeront en fonction de la région que vous avez sélectionnée pour votre point de terminaison privé et des adresses IP disponibles dans votre sous-réseau.

    :::image type="content" source="./media/private-link-security/dns-configuration.png" alt-text="Détails de la configuration DNS" border="true":::

1. Suivez les instructions de votre fournisseur de serveur DNS pour ajouter les zones DNS et les enregistrements A correspondant au tableau du portail. Veillez à sélectionner un serveur DNS dont l'étendue est adaptée à votre réseau. Chaque machine ou serveur qui utilise ce serveur DNS résout désormais les adresses IP des points de terminaison privés et doit être associé à l'Étendue de liaison privée Azure Arc (préversion), faute de quoi la connexion sera refusée.

### <a name="single-server-scenarios"></a>Scénarios à serveur unique

Si vous prévoyez d'utiliser des liaisons privées pour un nombre limité de machines ou serveurs, il n'est probablement pas nécessaire de mettre à jour la configuration DNS de l'ensemble de votre réseau. Dans ce cas, vous pouvez ajouter les noms d'hôte et les adresses IP des points de terminaison privés au fichier **Hosts** de votre système d'exploitation. Selon la configuration du système d'exploitation, le fichier Hosts peut être la méthode principale ou alternative pour résoudre le nom d'hôte en adresse IP.

#### <a name="windows"></a>Windows

1. À l'aide d'un compte doté de privilèges d'administrateur, ouvrez **C:\Windows\System32\drivers\etc\hosts**.

1. Ajoutez les adresses IP et les noms d'hôte des points de terminaison privés comme indiqué dans le tableau de l'étape 3 sous [Configuration manuelle du serveur DNS](#manual-dns-server-configuration). Le fichier hosts requiert d'abord l'adresse IP suivie d'un espace, puis du nom d'hôte.

1. Enregistrez le fichier contenant vos modifications. Il se peut que vous deviez d'abord enregistrer le fichier dans un autre répertoire, puis le copier à l'emplacement d'origine.

#### <a name="linux"></a>Linux

1. À l'aide d'un compte doté du privilège **sudoers**, exécutez `sudo nano /etc/hosts` pour ouvrir le fichier hosts.

1. Ajoutez les adresses IP et les noms d'hôte des points de terminaison privés comme indiqué dans le tableau de l'étape 3 sous [Configuration manuelle du serveur DNS](#manual-dns-server-configuration). Le fichier hosts exige d'abord l'adresse IP suivie d'un espace, puis du nom d'hôte.

1. Enregistrez le fichier contenant vos modifications.

## <a name="connect-to-an-azure-arc-enabled-servers"></a>Se connecter à des serveurs Azure Arc

> [!NOTE]
> La version minimale prise en charge de l’agent de la machine Azure Arc avec point de terminaison privé est la version 1.4. Le script de déploiement des serveurs Azure Arc généré sur le portail télécharge la dernière version.

### <a name="configure-a-new-azure-arc-enabled-server-to-use-private-link"></a>Configurer un nouveau serveur Azure Arc pour utiliser une liaison privée

Lorsque vous connectez une machine ou un serveur à des serveurs Azure Arc pour la première fois, vous avez la possibilité de le connecter à une Étendue de liaison privée. Procédez comme suit : 

1. À partir de votre navigateur, accédez au [portail Azure](https://portal.azure.com).

1. Accédez à **Serveurs -Azure Arc**.

1. Dans la page **Serveurs - Azure Arc**, sélectionnez **Ajouter** en haut à gauche.

1. Sur la page **Ajouter des serveurs à Azure Arc**, sélectionnez **Ajouter un seul serveur** ou **Ajouter plusieurs serveurs** en fonction de votre scénario de déploiement, puis sélectionnez **Générer un script**.

1. Dans la page **Générer un script**, sélectionnez l’abonnement et le groupe de ressources où vous souhaitez que la machine soit gérée dans Azure. Sélectionnez une localisation Azure destinée au stockage des métadonnées de la machine. Cet emplacement peut être identique ou différent de l’emplacement du groupe de ressources.

1. Dans la page **Prérequis**, passez en revue les informations, puis sélectionnez **Suivant : Détails des ressources**.

1. Dans la page **Détails des ressources**, spécifiez les informations suivantes :

    1. Dans la liste déroulante **Groupe de ressources**, sélectionnez le groupe de ressources à partir duquel sera gérée la machine.
    1. Dans la liste déroulante **Région**, sélectionnez la région Azure dans laquelle vous souhaitez stocker les métadonnées de la machine ou du serveur.
    1. Dans la liste déroulante **Système d’exploitation**, sélectionnez le système d’exploitation sur lequel le script est configuré pour s’exécuter.
    1. Sous **Connectivité réseau**, sélectionnez **Point de terminaison privé (préversion)** et, dans la liste déroulante, sélectionnez l'Étendue de liaison privée Azure Arc créé au point 1.

       :::image type="content" source="./media/private-link-security/arc-enabled-servers-create-script.png" alt-text="Sélection de l'option de connectivité Point de terminaison privé" border="true":::

    1. Sélectionnez **Suivant : Balises**.

1. Si vous avez sélectionné **Ajouter plusieurs serveurs**, sur la page **Authentification**, sélectionnez le principal de service créé pour les serveurs Azure arc dans la liste déroulante. Si vous n'avez pas créé de principal de service pour les serveurs Azure Arc, consultez d'abord la [procédure de création d'un principal de service](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) pour vous familiariser avec les autorisations requises et les étapes à suivre pour en créer un. Sélectionnez **Suivant : Étiquettes** pour continuer.

1. Dans la page **Balises**, passez en revue les suggestions de **balises d’emplacement physique** par défaut et entrez une valeur, ou spécifiez une ou plusieurs **Balises personnalisées** en fonction de vos standards.

1. Sélectionnez **Suivant : Télécharger et exécuter le script**.

1. Dans la page **Télécharger et exécuter le script**, passez en revue les informations de résumé, puis sélectionnez **Télécharger**. Si vous avez encore besoin d’apporter des modifications, sélectionnez **Précédent**.

Après avoir téléchargé le script, vous devez l'exécuter sur votre machine ou serveur en utilisant un compte doté de privilèges (administrateur ou racine). En fonction de la configuration de votre réseau, vous devrez peut-être télécharger l'agent à partir d'un ordinateur ayant accès à Internet et le transférer sur votre machine ou serveur, puis modifier le script en y entrant le chemin d'accès à l'agent. 

L'agent Windows peut être téléchargé à partir de [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent) et l'agent Linux à partir de [https://packages.microsoft.com](https://packages.microsoft.com). Recherchez la dernière version d'**azcmagent** située dans le répertoire de distribution de votre système d'exploitation et installée avec votre gestionnaire de package local. 

Le script renverra des messages d'état qui vous indiqueront si l'intégration a abouti au terme de celle-ci.

> [!NOTE]
> Si vous déployez Connected Machine Agent sur un serveur Linux, vous serez peut-être confronté à une attente de cinq minutes pendant la vérification de la connectivité du réseau, suivie d'une erreur indiquant `you do not have access to login.windows.net`, même si votre pare-feu est correctement configuré. Il s'agit d'un problème connu qui sera corrigé dans une version ultérieure de l'agent. L'intégration devrait malgré tout aboutir si votre pare-feu est correctement configuré.

### <a name="configure-an-existing-azure-arc-enabled-server"></a>Configurer un serveur Azure Arc existant

Pour permettre aux serveurs Azure Arc qui ont été configurés avant l’étendue de votre liaison privée, vous pouvez autoriser ces derniers à commencer à utiliser l'Étendue de liaison privée des serveurs Azure Arc en réalisant les étapes suivantes.

1. Sur le portail Azure, accédez à votre ressource Étendue de liaison privée Azure Arc.

1. Dans le volet gauche, sélectionnez **Ressources Azure Arc**, puis **+ Ajouter**.

1. Sélectionnez les serveurs dans la liste que vous souhaitez associer à l'Étendue de liaison privée, puis choisissez **Sélectionner** pour enregistrer vos modifications.

    > [!NOTE]
    > Seuls les serveurs Azure Arc associés au même abonnement et à la même région que votre Étendue de liaison privée sont affichés.

    :::image type="content" source="./media/private-link-security/select-servers-private-link-scope.png" alt-text="Sélection des ressources Azure Arc" border="true":::

Quinze minutes peuvent s'écouler avant que l'Étendue de liaison privée n'accepte les connexions des serveurs récemment associés.

## <a name="troubleshooting"></a>Résolution des problèmes

1. Vérifiez vos serveurs DNS locaux pour vous assurer qu'ils sont transférés vers Azure DNS ou qu'ils sont configurés avec les enregistrements A appropriés dans votre zone de liaison privée. Ces commandes de recherche doivent renvoyer des adresses IP privées dans votre réseau virtuel Azure. Si adresses IP publiques sont résolues, vérifiez la configuration DNS de votre machine ou serveur et de votre réseau.

    nslookup gbl.his.arc.azure.com  nslookup agentserviceapi.guestconfiguration.azure.com

1. En cas de problème lors de l'intégration d'une machine ou d'un serveur, vérifiez que vous avez ajouté les étiquettes de service Azure Active Directory et Azure Resource Manager au pare-feu de votre réseau local. L'agent doit communiquer avec ces services via Internet jusqu'à ce que des points de terminaison privés soient disponibles pour ceux-ci.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les points de terminaison privés, consultez [Qu’est-ce qu’Azure Private Endpoint ?](../../private-link/private-endpoint-overview.md).

* Si vous rencontrez des problèmes liés à la configuration de la connectivité de vos points de terminaison privés Azure, consultez [Résoudre les problèmes de connectivité des points de terminaison privés Azure](../../private-link/troubleshoot-private-endpoint-connectivity.md).

* Consultez les rubriques suivantes afin de configurer Private Link pour [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) ou [Stockage Blob Azure](../../private-link/tutorial-private-endpoint-storage-portal.md).
