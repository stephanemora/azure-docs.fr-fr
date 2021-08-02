---
title: Se connecter à un réseau virtuel à l’aide de Gestion des API Azure
description: Découvrez comment configurer une connexion à un réseau virtuel dans Gestion des API Azure et accéder à des services web par son intermédiaire.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 06/08/2021
ms.author: apimpm
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 9de42ef1aa7471f489a02af6e1931c0df0252b7f
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746336"
---
# <a name="connect-to-a-virtual-network-using-azure-api-management"></a>Se connecter à un réseau virtuel à l’aide de Gestion des API Azure
Avec les réseaux virtuels Azure, vous pouvez placer vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Vous pouvez ensuite connecter ces réseaux à vos réseaux locaux à l’aide de différentes technologies VPN. Pour en savoir plus sur les réseaux virtuels Azure, commencez par consulter les informations fournies dans [Vue d’ensemble du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

Gestion des API Azure peut être déployée à l’intérieur du réseau virtuel pour qu’elle puisse accéder aux services principaux au sein du réseau. Vous pouvez configurer le portail des développeurs et la passerelle API de manière à ce qu’ils soient accessibles depuis Internet ou uniquement au sein du réseau virtuel. 

Cet article décrit les options de connectivité du réseau virtuel, les paramètres, les limitations et les procédures de résolution des problèmes pour votre instance de Gestion des API. Pour les configurations spécifiques au mode interne, où le portail des développeurs et la passerelle d’API sont accessibles uniquement au sein du réseau virtuel, consultez [Se connecter à un réseau virtuel interne à l’aide de Gestion des API Azure](./api-management-using-with-internal-vnet.md).

> [!NOTE]
> L’URL du document d’importation d’API doit être hébergée sur une adresse Internet publiquement accessible.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prérequis

+ **Un abonnement Azure actif.** [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Une instance APIM.** Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Activer la connexion au réseau virtuel

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Activer la connectivité aux réseaux virtuels à l’aide du portail Azure

1. Accédez au [portail Azure](https://portal.azure.com) pour rechercher votre instance Gestion des API. Recherchez et sélectionnez **Services Gestion des API**.

1. Choisissez votre instance Gestion des API.

1. Sélectionnez **Réseau virtuel**.
1. Configurez l’instance de Gestion des API à déployer à l’intérieur d’un réseau virtuel.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Sélectionnez un réseau virtuel dans le portail Azure.":::

1. Sélectionnez le type d’accès souhaité :

    * **Désactivé** : type par défaut. Gestion des API n’est pas déployée dans un réseau virtuel.

    * **Externe** : la passerelle Gestion des API et le portail des développeurs sont accessibles à partir de l’Internet public via un équilibreur de charge externe. La passerelle peut accéder aux ressources au sein du réseau virtuel.

        ![Peering public][api-management-vnet-public]

    * **Interne** : la passerelle Gestion des API et le portail des développeurs sont accessibles uniquement au sein du réseau virtuel via un équilibreur de charge interne. La passerelle peut accéder aux ressources au sein du réseau virtuel.

        ![Peering privé][api-management-vnet-private]

1. Si vous avez sélectionné **Externe** ou **Interne**, vous voyez une liste de tous les emplacements (régions) où votre service Gestion des API est provisionné. 
1. Choisissez un **Emplacement**.
1. Choisissez **Réseau virtuel**, **Sous-réseau** et **Adresse IP publique**. 
    * La liste des réseaux virtuels contient les réseaux virtuels Resource Manager, disponibles dans vos abonnements Azure, qui sont installés dans la région que vous configurez.

        :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Paramètres du réseau virtuel sur le portail.":::

        > [!IMPORTANT]
        > * **Si vous utilisez la version 2020-12-01 ou une version antérieure pour déployer une instance de Gestion des API Azure dans un réseau virtuel Resource Manager :** le service doit se trouver dans un sous-réseau dédié qui ne contient que des instances de Gestion des API Azure. Toute tentative de déploiement d’une instance de Gestion des API Azure sur un sous-réseau du réseau virtuel Resource Manager qui contient d’autres ressources entraîne l’échec du déploiement.
        >
        > * **Si vous utilisez la version d’API 2021-01-01-preview ou une version ultérieure pour déployer une instance de Gestion des API Azure dans un réseau virtuel :** seul un réseau virtuel Resource Manager est pris en charge, mais le sous-réseau utilisé peut contenir d’autres ressources. Vous n’êtes pas obligé d’utiliser un sous-réseau dédié aux instances Gestion des API.

1. Sélectionnez **Appliquer**. La page **Réseau virtuel** de votre instance de Gestion des API est mise à jour avec vos nouveaux choix de réseau virtuel et de sous-réseau.

1. Poursuivez la configuration des paramètres de réseau virtuel pour les autres emplacements de votre instance de Gestion des API.

7. Dans la barre de navigation supérieure, sélectionnez **Enregistrer**, puis **Appliquer la configuration réseau**.

    La mise à jour de l’instance Gestion des API peut prendre de 15 à 45 minutes.

> [!NOTE]
> Avec les clients utilisant l’API version 2020-12-01 et les versions antérieures, l’adresse IP virtuelle de l’instance de Gestion des API est modifiée :
> * Le réseau virtuel est activé ou désactivé. 
> * Gestion des API est déplacée du réseau virtuel **extérieur** vers le réseau virtuel **interne** ou vice versa.

> [!IMPORTANT]
> * **Si vous utilisez l’API version 2018-01-01 et les versions antérieures :**    
> Le réseau virtuel est verrouillé pendant une durée maximale de six heures si vous supprimez Gestion des API d’un réseau virtuel ou si vous modifiez le réseau virtuel. Pendant ces six heures, vous ne pouvez pas supprimer le réseau virtuel ni y déployer de nouvelle ressource. 
>
> * **Si vous utilisez l’API version 2019-01-01 et les versions antérieures :**  
> Le réseau virtuel est disponible dès la suppression du service Gestion des API associé.

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Déployer Gestion des API dans un réseau virtuel externe

Vous pouvez également activer une connectivité de réseau virtuel à l’aide des méthodes suivantes.

### <a name="api-version-2021-01-01-preview"></a>API version 2021-01-01-preview

* [Modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip) Azure Resource Manager

     [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>API version 2020-12-01

* [Modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-create-with-external-vnet) Azure Resource Manager

     [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fapi-management-create-with-external-vnet%2Fazuredeploy.json)

* Cmdlets Azure PowerShell : [créer](/powershell/module/az.apimanagement/new-azapimanagement) ou [mettre à jour](/powershell/module/az.apimanagement/update-azapimanagementregion) une instance de Gestion des API dans un réseau virtuel

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Se connecter à un service web hébergé sur un réseau virtuel
Une fois que vous avez connecté votre service Gestion des API au réseau virtuel, vous pouvez accéder aux services principaux au sein de celui-ci comme vous le feriez pour des services publics. Lorsque vous créez ou modifiez une API, tapez l’adresse IP locale ou le nom d’hôte (si un serveur DNS est configuré pour le réseau virtuel) de votre service web dans le champ **URL du service web**.

![Ajouter des API à partir du VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Problèmes courants liés à la configuration réseau
Problèmes courants de configuration incorrecte qui peuvent se produire lors du déploiement du service Gestion des API dans un réseau virtuel :

* **Configuration du serveur DNS personnalisée :**  
    le service de la gestion des API dépend de plusieurs services Azure. Si la gestion des API est hébergée dans un réseau virtuel comportant un serveur DNS personnalisé, il doit résoudre les noms d’hôte de ces services Azure.  
    * Pour obtenir de l’aide sur la configuration DNS personnalisée, consultez [Résolution de noms des ressources dans les réseaux virtuels Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).  
    * Pour obtenir des informations de référence, consultez la [table des ports](#required-ports) et la configuration réseau requise.

    > [!IMPORTANT]
    > Si vous envisagez d’utiliser des serveurs DNS personnalisés pour le réseau virtuel, vous devez les configurer **avant** d’y déployer un service Gestion des API. Sinon, vous devez mettre à jour le service Gestion des API chaque fois que vous changez les serveurs DNS en exécutant l’[opération Appliquer une configuration réseau](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates).

* **Ports nécessaires pour la Gestion des API :**  
    Vous pouvez contrôler le trafic entrant et sortant dans le sous-réseau où la Gestion des API est déployée à l’aide de [groupes de sécurité réseau][groupes de sécurité réseau]. Si les ports suivants ne sont pas disponibles, Gestion des API risque de ne pas fonctionner correctement et d’être inaccessible. Les ports bloqués sont un autre problème de configuration courant lors de l’utilisation de Gestion des API avec un réseau virtuel.

<a name="required-ports"> </a> Quand une instance de service Gestion des API est hébergée dans un réseau virtuel, les ports du tableau suivant sont utilisés.

| Port(s) source / de destination | Sens          | Protocole de transfert |   [Balises de service](../virtual-network/network-security-groups-overview.md#service-tags) <br> Source / Destination   | Objectif (\*)                                                 | Type de réseau virtuel |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Trafic entrant            | TCP                | INTERNET / VIRTUAL_NETWORK            | Communication client avec Gestion des API                      | Externe             |
| * / 3443                     | Trafic entrant            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Point de terminaison de gestion pour le Portail Azure et PowerShell         | Externe et interne  |
| * / 443                  | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / Storage             | **Dépendance sur le Stockage Azure**                             | Externe et interne  |
| * / 443                  | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Dépendance vis-à-vis d’[Azure Active Directory](api-management-howto-aad.md) et Azure Key Vault                  | Externe et interne  |
| * / 1433                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / SQL                 | **Accès aux points de terminaison de SQL Azure**                           | Externe et interne  |
| * / 443                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                 | **Accès à Azure Key Vault**                           | Externe et interne  |
| * / 5671, 5672, 443          | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / EventHub            | Dépendance pour le [journal pour la stratégie Event Hub](api-management-howto-log-event-hubs.md) et l’agent de surveillance | Externe et interne  |
| * / 445                      | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / Storage             | Dépendance du partage de fichiers Azure pour [GIT](api-management-configuration-repository-git.md)                      | Externe et interne  |
| * / 443, 12000                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Extension Intégrité et surveillance         | Externe et interne  |
| * / 1886, 443                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publier les [journaux et métriques de diagnostic](api-management-howto-use-azure-monitor.md), [Resource Health](../service-health/resource-health-overview.md) et [Application Insights](api-management-howto-app-insights.md)                   | Externe et interne  |
| * / 25, 587, 25028                       | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / INTERNET            | Se connecter au relais SMTP pour envoyer des e-mails                    | Externe et interne  |
| * / 6381 - 6383              | Trafic entrant et sortant | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Accéder au service Redis pour les stratégies de [cache](api-management-caching-policies.md) entre machines         | Externe et interne  |
| * / 4290              | Trafic entrant et sortant | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Compteurs de synchronisation pour les stratégies de [limite de débit](api-management-access-restriction-policies.md#LimitCallRateByKey) entre machines         | Externe et interne  |
| * / *                        | Trafic entrant            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Équilibrage de charge de l’infrastructure Azure                          | Externe et interne  |

>[!IMPORTANT]
> Les deux éléments de la colonne *Objectif* sont obligatoires pour le déploiement correct du service Gestion des API. Toutefois, le blocage des autres ports entraîne une **dégradation** de la capacité à utiliser et **superviser le service en cours d’exécution et fournir le contrat SLA validé**.

+ **Fonctionnalités TLS :**  
  Pour activer la génération et la validation de la chaîne de certificats TLS/SSL, le service Gestion des API a besoin d’une connectivité réseau sortante vers `ocsp.msocsp.com`, `mscrl.microsoft.com` et `crl.microsoft.com`. Cette dépendance n’est pas obligatoire si l’un des certificats que vous chargez dans Gestion des API contient la totalité de la chaîne permettant d’accéder à la racine de l’autorité de certification.

+ **Accès DNS :**  
  L’accès sortant sur `port 53` est obligatoire pour la communication avec des serveurs DNS. S'il existe un serveur DNS personnalisé à l'autre extrémité d'une passerelle VPN, le serveur DNS doit être accessible depuis le sous-réseau hébergeant la gestion de l’API.

+ **Métriques et supervision de l’intégrité :**  
  La connectivité réseau sortante aux points de terminaison de surveillance Azure, qui sont résolus sous les domaines suivants, est représentée sous l’étiquette de service AzureMonitor pour une utilisation avec des groupes de sécurité réseau.

    | Environnement Azure | Points de terminaison                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure (public)      | <ul><li>gcs.prod.monitoring.core.windows.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com</li><li>shoebox2.prod.microsoftmetrics.com</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>prod3.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  
+ **Balises de service régional** : les règles NSG autorisant une connectivité sortante aux balises de service Storage, SQL et Event Hubs peuvent utiliser les versions régionales de ces balises correspondant à la région contenant l’instance Gestion des API (par exemple, Storage.WestUS pour une instance Gestion des API dans la région USA Ouest). Dans les déploiements dans plusieurs régions, le groupe de sécurité réseau de chaque région doit autoriser le trafic vers les balises de service pour cette région ainsi que pour la région primaire.

    > [!IMPORTANT]
    > Pour activer la publication du [portail des développeurs](api-management-howto-developer-portal.md) pour une instance de Gestion des API dans un réseau virtuel, autorisez la connectivité sortante vers le Stockage Blob dans la région USA Ouest. Par exemple, utilisez la balise de service **Storage.WestUS** dans une règle NSG. La connectivité au Stockage Blob dans la région USA Ouest est actuellement obligatoire pour publier le portail des développeurs pour toutes les instances de Gestion des API.

+ **Relais SMTP :**  
  connectivité réseau sortante pour le relais SMTP, qui se résout sur l’hôte `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` et `ies.global.microsoft.com`

+ **CAPTCHA du portail des développeurs :**  
  Connectivité réseau sortante pour CAPTCHA du portail de développeur, qui se résout sous les hôtes `client.hip.live.com` et `partner.hip.live.com`.

+ **Diagnostics du Portail Azure :**  
  Lors de l’utilisation de l’extension API Management depuis l’intérieur d’un réseau virtuel, un accès sortant vers `dc.services.visualstudio.com` sur `port 443` est obligatoire pour activer le flux des journaux de diagnostic à partir du Portail Azure. Cet accès contribue à la résolution des problèmes que vous pouvez rencontrer lors de l’utilisation d’extension.

+ **Azure Load Balancer :**  
  Vous n’êtes pas obligé d’autoriser les demandes entrantes de l’étiquette de service `AZURE_LOAD_BALANCER` pour la référence SKU `Developer`, car une seule unité de calcul est déployée derrière elle. Toutefois, le trafic entrant à partir de [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) devient critique lors de la mise à l’échelle vers une référence SKU supérieure comme `Premium`, car la défaillance de la sonde d’intégrité de Load Balancer fait échouer un déploiement.

+ **Application Insights :**  
  Si vous avez activé la surveillance d’[Azure Application Insights](api-management-howto-app-insights.md) sur Gestion des API, autorisez la connectivité sortante vers le [point de terminaison de télémétrie](../azure-monitor/app/ip-addresses.md#outgoing-ports) à partir du réseau virtuel.

+ **Tunneling forcé du trafic vers le pare-feu local à l’aide d’ExpressRoute ou de l’appliance virtuelle réseau :**  
  Généralement, vous configurez et définissez votre propre itinéraire par défaut (0.0.0.0/0), ce qui force tout le trafic du sous-réseau délégué de Gestion des API à traverser un pare-feu local ou une appliance virtuelle réseau. Ce flux de trafic interrompt la connectivité avec Gestion des API Azure, car le trafic sortant est bloqué en local ou fait l’objet d’une opération NAT sur un jeu d’adresses non reconnaissable qui ne fonctionne plus avec différents points de terminaison Azure. Vous pouvez résoudre ce problème à l’aide de deux méthodes : 

  * Activez les [points de terminaison de service][ServiceEndpoints] sur le sous-réseau où le service Gestion des API est déployé pour :
      * Azure SQL
      * Stockage Azure
      * Azure EventHub
      * Azure ServiceBus et
      * Azure KeyVault. 
  
    En activant des points de terminaison directement du sous-réseau délégué Gestion des API vers ces services, vous pouvez utiliser le réseau principal de Microsoft Azure pour un routage optimal du trafic de service. Si vous utilisez des points de terminaison de service avec le service Gestion des API tunnelisé de force, le trafic des services Azure susmentionnés n’est pas tunnelisé de force. L’autre trafic de dépendance du service Gestion des API est tunnelisé de force et ne peut pas être perdu. En cas de perte, le service Gestion des API ne fonctionnerait pas correctement.

  * Tout le trafic du plan de contrôle entre Internet et le point de terminaison de gestion de votre service Gestion des API est acheminé à travers un ensemble d’adresses IP entrantes hébergé par Gestion des API. Lorsque le trafic est tunnelisé de force, les réponses ne correspondent pas symétriquement à ces adresses IP sources entrantes. Pour surmonter la limitation, définissez la destination des itinéraires définis par l’utilisateur ([UDR][UDRs]) suivants sur « Internet » pour rediriger le trafic vers Azure. Vous trouverez la documentation relative à l’ensemble des adresses IP entrantes pour le trafic du plan de contrôle dans [Adresses IP du plan de contrôle](#control-plane-ips).

  * Pour les autres dépendances du service Gestion des API tunnelisées de force, résolvez le nom d’hôte et atteignez le point de terminaison. Il s’agit notamment des paramètres suivants :
      - Métriques et supervision de l’intégrité
      - Diagnostics du Portail Azure
      - Relais SMTP
      - CAPTCHA du portail des développeurs

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Dépannage
* **Échec du déploiement initial du service Gestion des API dans un sous-réseau :** 
  * Déployer une machine virtuelle dans le même sous-réseau. 
  * Utilisez le Bureau à distance pour vous connecter à la machine virtuelle et vérifiez la connectivité à chacune des ressources suivantes de votre abonnement Azure :
    * Stockage Blob Azure
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Après avoir validé la connectivité, supprimez toutes les ressources dans le sous-réseau avant de déployer Gestion des API dans le sous-réseau.

* **Vérifier l’état de la connectivité réseau :**  
  * Après avoir déployé Gestion des API dans le sous-réseau, utilisez le portail pour vérifier la connectivité de votre instance aux dépendances, telles que Stockage Azure. 
  * Dans le menu de gauche du portail, sous **Déploiement et infrastructure**, sélectionnez **État de la connectivité réseau**.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Vérifier l’état de la connectivité réseau dans le portail":::

  | Filtrer | Description |
  | ----- | ----- |
  | **Obligatoire** | Sélectionnez cette option pour vérifier la connectivité aux services Azure requis pour Gestion des API. Un échec indique que l’instance ne peut pas effectuer des opérations de base pour gérer les API |
  | **Facultatif** | Sélectionnez cette option pour vérifier la connectivité aux services facultatifs. Tout échec indique uniquement que la fonctionnalité spécifique ne fonctionnera pas (par exemple, SMTP). Un échec peut entraîner une dégradation dans l’utilisation et la surveillance de l’instance de Gestion des API et empêcher d’offrir le contrat SLA validé. |

  Pour résoudre les problèmes de connectivité, consultez [Problèmes courants de configuration du réseau](#network-configuration-issues) et corrigez les paramètres réseau requis.

* **Mises à jour incrémentielles :**  
  Lorsque vous changez votre réseau, consultez [API NetworkStatus](/rest/api/apimanagement/2019-12-01/networkstatus) pour vérifier que le service Gestion des API n’a pas perdu l’accès aux ressources critiques. L’état de connectivité doit être mis à jour toutes les 15 minutes.

* **Liens de navigation de ressources :**  
  Pendant le déploiement sur un sous-réseau de réseau virtuel Resource Manager doté de l’API version 2020-12-01 et versions antérieures, Gestion des API réserve le sous-réseau en créant un lien de navigation de ressource. Si le sous-réseau contient déjà une ressource d’un autre fournisseur, le déploiement **échoue**. De même, lorsque vous supprimez un service Gestion des API ou le déplacez vers un sous-réseau différent, le lien de navigation des ressources est supprimé.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Exigence de taille du sous-réseau
Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. La première et la dernière adresses IP des sous-réseaux sont réservées à des fins de conformité du protocole. Trois adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez la section [L’utilisation des adresses IP au sein de ces sous-réseaux est-elle soumise à des restrictions ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

Outre les adresses IP utilisées par l’infrastructure de réseau virtuel Azure, chaque instance de Gestion des API du sous-réseau utilise :
* deux adresses IP par unité de référence SKU Premium ou 
* une adresse IP pour la référence SKU du développeur. 

Chaque instance réserve une adresse IP supplémentaire pour l’équilibreur de charge externe. Lors du déploiement dans un [réseau virtuel interne](./api-management-using-with-internal-vnet.md), l’instance requiert une adresse IP supplémentaire pour l’équilibreur de charge interne.

D’après le calcul ci-dessus, la taille minimale du sous-réseau, dans lequel Gestion des API peut être déployée, est de /29, ce qui donne trois adresses IP utilisables. Chaque unité d’échelle supplémentaire de Gestion des API requiert deux adresses IP supplémentaires.

## <a name="routing"></a><a name="routing"> </a> Routage
+ Une adresse IP publique à charge équilibrée (adresse IP virtuelle) est réservée pour fournir l’accès à l’ensemble des points de terminaison et ressources de service extérieurs au réseau virtuel.
  + Les adresses IP publiques à charge équilibrée se trouvent dans le panneau **Vue d’ensemble/Bases** sur le portail Azure.
+ Une adresse IP d’une plage d’adresses IP de sous-réseau (DIP) permet d’accéder aux ressources au sein du réseau virtuel.

## <a name="limitations"></a><a name="limitations"> </a>Limitations
* Pour l’API version 2020-12-01 et les versions antérieures, un sous-réseau contenant des instances de Gestion des API ne peut pas contenir d’autres types de ressources Azure.
* Le sous-réseau et le service Gestion des API doivent figurer dans le même abonnement.
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas être déplacé entre des abonnements.
* Pour les déploiements de Gestion des API dans plusieurs régions configurés en mode de réseau virtuel interne, les utilisateurs possèdent le routage et sont chargés de gérer leur équilibrage de charge entre les différentes régions.
* En raison de la limitation de la plateforme, la connectivité entre une ressource d’un réseau virtuel homologué à l’échelle mondiale dans une autre région et le service Gestion des API en mode interne ne fonctionnera pas. Pour plus d’informations, consultez la section [Les ressources situées dans un réseau virtuel ne peuvent pas communiquer avec l’adresse IP d’un équilibreur de charge interne Azure dans le réseau virtuel homologué](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Adresses IP du plan de contrôle

Les adresses IP sont divisées par **environnement Azure**. Lorsque vous autorisez des requêtes entrantes, les adresses IP marquées comme **globales** doivent être autorisées, ainsi que l’adresse IP spécifique de la **région**.

| **Environnement Azure**|   **Région**|  **Adresse IP**|
|-----------------|-------------------------|---------------|
| Azure (public)| USA Centre Sud (Global)| 104.214.19.224|
| Azure (public)| USA Centre Nord (Global)| 52.162.110.80|
| Azure (public)| Centre-USA Ouest| 52.253.135.58|
| Azure (public)| Centre de la Corée| 40.82.157.167|
| Azure (public)| Ouest du Royaume-Uni| 51.137.136.0|
| Azure (public)| OuJapon Est| 40.81.185.8|
| Azure (public)| Centre-Nord des États-Unis| 40.81.47.216|
| Azure (public)| Sud du Royaume-Uni| 51.145.56.125|
| Azure (public)| Inde Ouest| 40.81.89.24|
| Azure (public)| USA Est| 52.224.186.99|
| Azure (public)| Europe Ouest| 51.145.179.78|
| Azure (public)| Japon Est| 52.140.238.179|
| Azure (public)| France Centre| 40.66.60.111|
| Azure (public)| Est du Canada| 52.139.80.117|
| Azure (public)| Émirats arabes unis Nord| 20.46.144.85|
| Azure (public)| Brésil Sud| 191.233.24.179|
| Azure (public)| Brésil Sud-Est| 191.232.18.181|
| Azure (public)| Asie Sud-Est| 40.90.185.46|
| Azure (public)| Afrique du Sud Nord| 102.133.130.197|
| Azure (public)| Centre du Canada| 52.139.20.34|
| Azure (public)| Corée du Sud| 40.80.232.185|
| Azure (public)| Inde centrale| 13.71.49.1|
| Azure (public)| USA Ouest| 13.64.39.16|
| Azure (public)| Sud-Australie Est| 20.40.160.107|
| Azure (public)| Centre de l’Australie| 20.37.52.67|
| Azure (public)| Inde Sud| 20.44.33.246|
| Azure (public)| USA Centre| 13.86.102.66|
| Azure (public)| Australie Est| 20.40.125.155|
| Azure (public)| USA Ouest 2| 51.143.127.203|
| Azure (public)| USA Ouest 3| 20.150.167.160|
| Azure (public)| USA Est 2 (EUAP)| 52.253.229.253|
| Azure (public)| EUAP USA Centre| 52.253.159.160|
| Azure (public)| États-Unis - partie centrale méridionale| 20.188.77.119|
| Azure (public)| USA Est 2| 20.44.72.3|
| Azure (public)| Europe Nord| 52.142.95.35|
| Azure (public)| Asie Est| 52.139.152.27|
| Azure (public)| France Sud| 20.39.80.2|
| Azure (public)| Suisse Ouest| 51.107.96.8|
| Azure (public)| Centre de l’Australie 2| 20.39.99.81|
| Azure (public)| Émirats arabes unis Centre| 20.37.81.41|
| Azure (public)| Suisse Nord| 51.107.0.91|
| Azure (public)| Afrique du Sud Ouest| 102.133.0.79|
| Azure (public)| Allemagne Centre-Ouest| 51.116.96.0|
| Azure (public)| Allemagne Nord| 51.116.0.0|
| Azure (public)| Norvège Est| 51.120.2.185|
| Azure (public)| Norvège Ouest| 51.120.130.134|
| Azure China 21Vianet| Chine Nord (Global)| 139.217.51.16|
| Azure China 21Vianet| Chine Est (Global)| 139.217.171.176|
| Azure China 21Vianet| Chine du Nord| 40.125.137.220|
| Azure China 21Vianet| Chine orientale| 40.126.120.30|
| Azure China 21Vianet| Chine Nord 2| 40.73.41.178|
| Azure China 21Vianet| Chine orientale 2| 40.73.104.4|
| Azure Government| USGov Virginie (Global)| 52.127.42.160|
| Azure Government| USGov Texas (Global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| Gouvernement des États-Unis - Arizona| 52.244.32.39|
| Azure Government| Gouvernement des États-Unis - Texas| 52.243.154.118|
| Azure Government| US DoD - Centre| 52.182.32.132|
| Azure Government| USDoD Est| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Contenu associé
* [Connexion d’un réseau virtuel au serveur principal à l’aide de la passerelle VPN](../vpn-gateway/design.md#s2smulti)
* [Connexion d’un réseau virtuel utilisant des modèles de déploiement différents](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure](api-management-howto-api-inspector.md)
* [Questions fréquentes (FAQ) sur les réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Balises de service](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
