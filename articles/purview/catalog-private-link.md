---
title: Utiliser des points de terminaison privés pour un accès sécurisé à Purview
description: Cet article décrit comment vous pouvez configurer un point de terminaison privé pour votre compte Purview
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 5101e71f6caf23e48adc59c9afde402427aa9995
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112080414"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>Utiliser des points de terminaison privés pour votre compte Purview

Vous pouvez utiliser des points de terminaison privés pour vos comptes Purview afin de permettre aux clients et utilisateurs d’un réseau virtuel (VNet) d’accéder en toute sécurité au catalogue via une liaison privée. Le point de terminaison privé utilise une adresse IP de l’espace d’adressage du réseau virtuel pour votre compte Purview. Le trafic réseau entre les clients sur le réseau virtuel et le stockage Purview traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, éliminant ainsi l’exposition sur l’Internet public.

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Architecture d’Azure Purview et Azure Private Link":::



Consultez les [questions fréquentes (FAQ) relatives aux liaisons privées Azure Purview](./catalog-private-link-faqs.md).

## <a name="create-purview-account-with-a-private-endpoint"></a>Créer un compte Purview avec un point de terminaison privé

1. Accédez au [portail Azure](https://portal.azure.com), puis à votre compte Purview.

1. Renseignez les informations de base, puis, sous l’onglet **Mise en réseau**, définissez la méthode de connectivité sur Point de terminaison privé. Configurez vos points de terminaison privés d’ingestion en fournissant des détails sur **l’abonnement, le réseau virtuel et le sous-réseau** que vous souhaitez appairer avec votre point de terminaison privé.

   > [!NOTE]
   > Ne créez un point de terminaison privé d’ingestion que si vous envisagez d’activer un isolement réseau dans le cadre de scénarios d’analyse de bout en bout de vos sources Azure et locales. Actuellement, nous ne prenons pas en charge les points de terminaison privés d’ingestion fonctionnant avec des sources AWS.

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="Créer un point de terminaison privé dans le portail Azure":::

1. Vous pouvez également configurer une **zone DNS privée** pour chaque point de terminaison privé d’ingestion.

1. Sélectionnez **Ajouter** afin d’ajouter un point de terminaison privé pour votre compte Purview.

1. Dans la page Créer un point de terminaison privé, définissez la sous-ressource Purview sur **compte**, choisissez vos réseau et sous-réseau virtuels, puis sélectionnez la zone DNS privée dans laquelle le DNS sera inscrit (vous pouvez également utiliser vos propres serveurs DNS ou créer des enregistrements DNS à l’aide de fichiers hôtes sur vos machines virtuelles).

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="Sélections pour la création de points de terminaison privés":::

1. Sélectionnez **OK**.

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page Vérifier + créer où Azure valide votre configuration.

1. Quand le message Validation réussie s’affiche, sélectionnez **Créer**.

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="Validation réussie pour la création de compte":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>Créer un point de terminaison privé pour le portail web Purview

1. Accédez au compte Purview que vous venez de créer, puis, dans la section Paramètres, sélectionnez les connexions de point de terminaison privée.

1. Sélectionnez **+Point de terminaison privé** pour créer un point de terminaison privé.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Créer un point de terminaison privé du portail":::

1. Entrez les informations de base.

1. Sous l’onglet Ressource, sélectionnez le type de ressource **Microsoft.Purview/account**.

1. Sélectionnez comme Ressource le compte Purview nouvellement créé, puis sélectionnez la sous-ressource cible **portail**.
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="Détails du point de terminaison privé du portail":::

1. Sous l’onglet Configuration, sélectionnez le réseau virtuel et la zone DNS privée. Accédez à la page de résumé, puis cliquez sur **Créer** pour créer le point de terminaison privé du portail.

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>Exigences relatives à la zone DNS privée pour les points de terminaison privés
Quand vous créez un point de terminaison privé, l’enregistrement de ressources CNAME DNS pour Purview est mis à jour avec un alias dans un sous-domaine avec le préfixe `privatelink`. Par défaut, nous créons également une [zone DNS privée](../dns/private-dns-overview.md) correspondant au sous-domaine `privatelink`, avec les enregistrements de ressource DNS A pour les points de terminaison privés.
 
Lorsque vous résolvez l’URL du point de terminaison Purview à l’extérieur du réseau virtuel avec le point de terminaison privé, elle correspond au point de terminaison public d’Azure Purview. En cas de résolution à partir du réseau virtuel hébergeant le point de terminaison privé, l’URL du point de terminaison Purview correspond à l’adresse IP du point de terminaison privé.

Par exemple, si le nom du compte Purview est « PurviewA », en cas de résolution à partir de l’extérieur du réseau virtuel hébergeant le point de terminaison privé, l’URL sera la suivante :

| Nom | Type | Valeur |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | Un | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |
 
Les enregistrements de ressources DNS correspondant à PurviewA, lorsqu’ils sont résolus dans le réseau virtuel hébergeant le point de terminaison privé, sont les suivants :
 
| Nom | Type | Valeur |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | Un | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |
 
 > [!important]
 > Si vous n’utilisez pas de redirecteurs DNS et que vous gérez des enregistrements A directement dans vos serveurs DNS locaux pour résoudre les points de terminaison par le biais de leurs adresses IP privées, vous devrez peut-être créer des enregistrements A supplémentaires dans vos serveurs DNS :

| Nom | Type | Valeur |
| ---------- | -------- | --------------- |
| `PurviewA.scan.Purview.azure.com` | Un | \<private endpoint IP address\> |
| `PurviewA.catalog.Purview.azure.com` | A | \<private endpoint IP address\> |

<br> 

_Exemple de résolution de noms DNS Azure Purview à partir de l’extérieur du réseau virtuel ou lorsque le point de terminaison privé Azure n’est pas configuré :_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="Résolution de noms Purview en dehors de CorpNet":::

_Exemple de résolution de noms DNS Azure Purview à l’intérieur du réseau virtuel :_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="Résolution de noms Purview à partir de CorpNet":::

Il est important de configurer correctement vos paramètres DNS pour résoudre l’adresse IP du point de terminaison privé en nom de domaine complet (FQDN) de la chaîne de connexion.

Si vous utilisez un serveur DNS personnalisé sur votre réseau, les clients doivent pouvoir résoudre le nom de domaine complet du point de terminaison Purview en l’adresse IP du point de terminaison privé. Vous devez configurer votre serveur DNS de manière à déléguer votre sous-domaine de liaison privée à la zone DNS privée du réseau virtuel ou configurer les enregistrements A pour « PurviewA.privatelink.purview.azure.com » avec l’adresse IP du point de terminaison privé.

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Résolution de noms Purview":::

Pour plus d’informations, consultez [Configuration DNS des points de terminaison privés Azure](../private-link/private-endpoint-dns.md).

## <a name="enabling-access-to-azure-active-directory"></a>Activation de l’accès à Azure Active Directory

> [!NOTE]
> Si votre machine virtuelle, passerelle VPN ou passerelle de peering de réseau virtuel disposent d’un accès Internet public, elles peuvent accéder au portail et au compte Purview activés avec des points de terminaison privés, et vous n’êtes pas obligé de suivre le reste des instructions ci-dessous. Si votre réseau privé a des règles de groupe de sécurité réseau définies pour refuser tout trafic Internet public, vous devez ajouter des règles pour activer l’accès à AAD. Pour ce faire, suivez les instructions ci-dessous.

Les instructions ci-dessous ont trait à l’accès à Purview en toute sécurité à partir d’une machine virtuelle Azure. Vous devez suivre des étapes similaires si vous utilisez un VPN ou d’autres passerelles de peering de réseaux virtuels.

1. Accédez à votre machine virtuelle dans le portail Azure, puis, dans la section Paramètres, sélectionnez l’onglet Mise en réseau. Sélectionnez ensuite des règles de port de sortie, puis cliquez sur Ajouter une règle de port de sortie.

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="Ajout d’une règle de trafic sortant":::

2. Dans le panneau Ajouter une règle de port de sortie, sélectionnez Étiquette de service en tant que *Destination*, **AzureActiveDirectory** en tant qu’Étiquette de service de destination, * en tant que Plages de ports de destination, autoriser en tant qu’Action, puis **La priorité doit être supérieure à la règle qui a refusé tout trafic Internet**. Créez la règle.

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="Ajout de détails de règle de trafic sortant":::

3. Suivez les mêmes étapes pour créer une autre règle afin d’autoriser l’étiquette de service « **AzureResourceManager** ». Si vous avez besoin d’accéder au portail Azure, vous pouvez également ajouter une règle pour l’étiquette de service « *AzurePortal* ».

4. Connectez-vous à la machine virtuelle, ouvrez le navigateur, accédez à la console du navigateur (Ctrl + Maj + J), puis basculez vers l’onglet du réseau pour surveiller les demandes réseau. Entrez web.purview.azure.com dans la zone URL, puis essayez de vous connecter à l’aide de vos informations d’identification AAD. Il est probable que la connexion échoue et, sous l’onglet du réseau sur la console, vous voyiez AAD tenter d’accéder à aadcdn.msauth.net mais être bloqué.

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="Détails d’échec de connexion":::

5. Dans ce cas, ouvrez une invite de commandes sur la machine virtuelle, effectuez un test ping de cette URL (aadcdn.msauth.net), récupérez son adresse IP, puis ajoutez une règle de port de sortie pour l’adresse IP dans les règles de sécurité réseau de la machine virtuelle. Définissez la Destination sur Adresse IP et définissez les Adresses IP de destination sur l’adresse IP d’aadcdn. En raison de l’équilibreur de charge et du gestionnaire de trafic, l’adresse IP du CDN AAD pourrait être dynamique. Une fois que vous avez obtenu son adresse IP, il est préférable de l’ajouter dans le fichier hôte de la machine virtuelle pour forcer le navigateur à visiter cette adresse IP afin d’obtenir le CDN AAD.

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="Test ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="Règle de CDN AAD":::

6. Une fois la nouvelle règle créée, revenez à la machine virtuelle et réessayez de vous connecter à l’aide de vos informations d’identification Azure Active Directory. Si la connexion réussit, le portail Purview est prêt à être utilisé. Toutefois, dans certains cas, AAD opère une redirection vers d’autres domaines pour se connecter en fonction du type de compte du client. Par exemple, pour un compte live.com, AAD redirige vers live.com pour la connexion, puis ces demandes sont à nouveau bloquées. Pour des comptes d’employés Microsoft, AAD accède à msft.sts.microsoft.com pour les informations de connexion. Vérifiez les demandes de mise en réseau sous l’onglet de mise en réseau du navigateur pour afficher les demandes du domaine qui sont bloquées, recommencez l’étape précédente pour obtenir son adresse IP, puis ajoutez des règles de port de sortie dans le groupe de sécurité réseau afin d’autoriser les demandes pour cette adresse IP (si possible, ajoutez l’URL et l’adresse IP au fichier hôte de la machine virtuelle pour corriger la résolution DNS). Si vous connaissez les plages exactes d’adresses IP du domaine de connexion, vous pouvez également les ajouter directement dans les règles de mise en réseau.

7. Maintenant, votre connexion à Azure Active Directory doit réussir. Le portail Purview se chargera correctement, mais la liste de tous les comptes Purview ne fonctionnera pas, car il ne peut accéder qu’à un compte Purview spécifique. Entrez `web.purview.azure.com/resource/{PurviewAccountName}` pour accéder directement au compte Purview pour lequel vous avez correctement configuré un point de terminaison privé.
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>Points de terminaison privés d’ingestion et sources d’analyse dans des réseaux privés et virtuels, et derrière des points de terminaison privés

Si vous souhaitez garantir l’isolement réseau de vos métadonnées circulant de la source en cours d’analyse au mappage de données Purview, procédez comme suit :

1. Activez un **point de terminaison privé d’ingestion** en procédant de la manière décrite dans [cette section](#creating-an-ingestion-private-endpoint).

1. Analyser la source à l’aide d’un **IR auto-hébergé**.
 
    1. Tous les types de sources locales, tels que SQL Server, Oracle, SAP et autres, ne sont actuellement pris en charge que via des analyses basées sur un IR auto-hébergé. L’IR auto-hébergé doit s’exécuter au sein de votre réseau privé, et être appairé avec votre réseau virtuel dans Azure. Votre réseau virtuel Azure doit ensuite être activé sur votre point de terminaison privé d’ingestion en suivant les étapes [ci-dessous](#creating-an-ingestion-private-endpoint). 

    2. Pour tous les types de sources **Azure**, telles que le service Stockage Blob Azure, Azure SQL Database et d’autres, vous devez choisir explicitement d’exécuter l’analyse à l’aide d’un IR auto-hébergé pour garantir l’isolement réseau. Suivez les étapes dans [Création et gestion d’un runtime d’intégration auto-hébergé](manage-integration-runtimes.md) pour configurer un IR auto-hébergé. Configurez ensuite votre analyse sur la source Azure en sélectionnant cet IR auto-hébergé dans la liste déroulante **Se connecter via le runtime d’intégration** pour garantir l’isolement réseau. 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="Exécution d’une analyse Azure à l’aide d’un IR auto-hébergé":::

> [!NOTE]
> Lorsque vous utilisez un point de terminaison privé pour l’ingestion, vous pouvez utiliser Azure Integration Runtime pour l’analyse uniquement pour les sources de données suivantes :
> - Stockage Blob Azure 
> - Azure Data Lake Storage Gen2
>  
> Pour les autres sources de données, un runtime d’intégration auto-hébergé est requis.
> Actuellement, nous ne prenons pas en charge la méthode d’informations d’identification MSI lorsque vous analysez vos sources Azure à l’aide d’un IR auto-hébergé. Vous devez utiliser l’une des autres méthodes d’informations d’identification prises en charge pour cette source Azure.

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>Activer un point de terminaison privé sur des comptes Purview existants

Il existe deux façons d’ajouter des points de terminaison privés Purview après avoir créé votre compte Purview :

- Utilisation du portail Azure (compte Purview)
- Utilisation du Centre Private Link

### <a name="using-the-azure-portal-purview-account"></a>Utilisation du portail Azure (compte Purview)

1. Accédez au compte Purview à partir du portail Azure, sélectionnez les connexions de point de terminaison privés dans la section **Mise en réseau** de **Paramètres**.

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="Créer un point de terminaison privé de compte":::

1. Sélectionnez **+Point de terminaison privé** pour créer un point de terminaison privé.

1. Entrez les informations de base.

1. Sous l’onglet Ressource, sélectionnez le **type de ressource** **Microsoft.Purview/accounts**.

1. Sélectionnez le compte Purview comme **Ressource**, puis **compte** comme sous-ressource cible.

1. Sous l’onglet Configuration, sélectionnez le **réseau virtuel** et la **zone DNS privée**. Accédez à la page de résumé, puis cliquez sur **Créer** pour créer le point de terminaison privé du portail.

> [!NOTE]
> Vous devrez suivre les mêmes étapes que ci-dessus pour la sous-ressource cible sélectionnée en tant que **Portail** .

#### <a name="creating-an-ingestion-private-endpoint"></a>Création d’un point de terminaison privé d’ingestion

1. Accédez au compte Purview à partir du portail Azure, sélectionnez les connexions de point de terminaison privés dans la section **Mise en réseau** de **Paramètres**.

3. Accédez à l’onglet **Connexions de point de terminaison privé d’ingestion**, puis cliquez sur **+ Nouveau** pour créer un point de terminaison privé d’ingestion.

4. Entrez les informations de base et les détails du réseau virtuel.
 
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="Entrer les détails du point de terminaison privé":::

5. Cliquez sur **Créer** pour terminer la configuration.

> [!NOTE]
> Vous ne pouvez créer des points de terminaison privés d’ingestion que via le portail Azure Purview décrit ci-dessus. Vous ne pouvez pas le faire à partir du Centre Private Link.

### <a name="using-the-private-link-center"></a>Utilisation du Centre Private Link

1. Accédez au [portail Azure](https://portal.azure.com).

2. Dans la barre de recherche en haut de la page, recherchez « liaison privée » et accédez au panneau Liaison privée en cliquant sur la première option.

3. Sélectionnez **+ Ajouter** et renseignez les détails de base.

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="Créer un PE à partir du Centre Private Link":::

4. Sélectionnez comme Ressource le compte Purview nouvellement créé, puis sélectionnez la sous-ressource cible **compte**.

5. Sous l’onglet Configuration, sélectionnez le réseau virtuel et la zone DNS privée. Accédez à la page de résumé, puis cliquez sur **Créer** pour créer le point de terminaison privé du compte.

> [!NOTE]
> Vous devrez suivre les mêmes étapes que ci-dessus pour la sous-ressource cible sélectionnée en tant que **Portail** .

## <a name="firewalls-to-restrict-public-access"></a>Pare-feu pour restreindre l’accès public

Pour couper complètement l’accès au compte Purview à partir de l’Internet public, procédez comme suit. Ce paramétrage s’applique tant aux connexions de point de terminaison privé qu’aux connexions de point de terminaison privé d’ingestion.

1. Accédez au compte Purview à partir du portail Azure, sélectionnez les connexions de point de terminaison privés dans la section **Mise en réseau** de **Paramètres**.

3. Accédez à l’onglet du pare-feu et vérifiez que le bouton bascule est défini sur **Refuser**.

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="Paramètres de pare-feu de point de terminaison privé":::

## <a name="next-steps"></a>Étapes suivantes

- [Naviguer dans le catalogue de données Azure Purview](how-to-browse-catalog.md)

- [Recherche dans le catalogue de données Azure Purview](how-to-search-catalog.md)
