---
title: Utilisation de la gestion des API Azure avec des réseaux virtuels
description: Découvrez comment configurer une connexion à un réseau virtuel dans Gestion des API Azure et accéder à des services web par son intermédiaire.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: 532c1051522410c496fb3809c06c7e3a74340adb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006044"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Utilisation de la gestion des API Azure avec des réseaux virtuels
Les réseaux virtuels Azure vous permettent de placer vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Ces réseaux peuvent ensuite être connectés à vos réseaux locaux à l’aide de différentes technologies VPN. Pour en savoir plus sur les réseaux virtuels Azure, commencez par consulter la page [Présentation du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

La gestion des API Azure peut être déployée à l’intérieur du réseau virtuel (VNET), pour qu’il puisse accéder aux services principaux au sein du réseau. Le portail des développeurs et la passerelle API peuvent être configurés pour être accessibles depuis Internet ou uniquement au sein du réseau virtuel.

> [!NOTE]
> La gestion des API Azure prend en charge les réseaux virtuels classiques et Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).

## <a name="enable-vpn"></a>Activer la connexion au réseau virtuel

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Activer la connectivité aux réseaux virtuels à l’aide du portail Azure

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre instance APIM.
2. Sélectionnez **Réseau virtuel**.
3. Configurez l’instance du service Gestion des API à déployer à l’intérieur d’un réseau virtuel.

    ![Menu Réseau virtuel du service Gestion des API][api-management-using-vnet-menu]
4. Sélectionnez le type d’accès souhaité :

   * **Externe** : la passerelle Gestion des API et le portail des développeurs sont accessibles à partir de l’Internet public via un équilibreur de charge externe. La passerelle peut accéder aux ressources au sein du réseau virtuel.

     ![Homologation publique][api-management-vnet-public]

   * **Interne** : la passerelle Gestion des API et le portail des développeurs sont accessibles uniquement sur le réseau virtuel via un équilibreur de charge interne. La passerelle peut accéder aux ressources au sein du réseau virtuel.

     ![Homologation privée][api-management-vnet-private]

     Vous voyez maintenant une liste de toutes les régions où votre service Gestion des API est créé. Sélectionnez un réseau VNET et un sous-réseau pour chaque région. La liste contient les réseaux virtuels classiques et Resource Manager, disponibles dans vos abonnements Azure, qui sont installés dans la région que vous configurez.

     > [!NOTE]
     > Le **point de terminaison de service** du diagramme ci-dessus inclut la passerelle/le proxy, le portail Azure, le portail des développeurs, GIT et le point de terminaison de gestion directe.
     > Le **point de terminaison de gestion** du diagramme ci-dessus est le point de terminaison hébergé sur le service pour gérer la configuration via le portail Azure et Powershell.
     > Veuillez également noter que, bien que le diagramme contient les adresses IP de ses différents points de terminaison, le service Gestion des API répond **uniquement** à ses noms d’hôtes configurés.

     > [!IMPORTANT]
     > Lorsque vous déployez une instance de la gestion des API Azure sur un réseau virtuel Resource Manager, le service doit se trouver dans un sous-réseau dédié qui ne contient aucune autre ressource à l’exception des instances de la gestion des API Azure. Si vous essayez de déployer une instance de gestion des API Azure sur un sous-réseau virtuel Resource Manager qui contient d’autres ressources, le déploiement échouera.
     >

     ![Sélectionner le VPN][api-management-setup-vpn-select]

5. Cliquez sur **enregistrer** dans la barre de navigation supérieure.
6. Cliquez sur **appliquer la configuration réseau** dans la barre de navigation supérieure.

> [!NOTE]
> L’adresse IP virtuelle de l’instance de gestion des API change à chaque activation ou désactivation du réseau virtuel.
> L’adresse IP virtuelle est également modifiée si la gestion des API passe **d’externe** à **interne** ou vice versa
>

> [!IMPORTANT]
> Si vous supprimez le service Gestion des API à partir d’un réseau virtuel (VNET) ou que vous modifiez celui sur lequel il est déployé, le réseau virtuel précédemment utilisé peut rester verrouillé jusqu’à deux heures. Pendant ce temps, vous ne pourrez pas supprimer le réseau virtuel ou y déployer une nouvelle ressource.

## <a name="enable-vnet-powershell"></a>Activation de la connexion au réseau virtuel à l’aide d’applets de commande PowerShell
Vous pouvez également activer la connectivité de réseau virtuel à l’aide d’applets de commande PowerShell.

* **Création d’un service de gestion des API au sein d’un réseau virtuel** : Utilisez l’applet de commande [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) pour créer un service de gestion des API Azure à l’intérieur d’un réseau virtuel.

* **Déploiement d’un service de gestion des API existant au sein d’un réseau virtuel** : Utilisez l’applet de commande [AzApiManagementRegion de mise à jour](/powershell/module/az.apimanagement/update-azapimanagementregion) pour déplacer un service de gestion des API Azure existant à l’intérieur d’un réseau virtuel.

## <a name="connect-vnet"></a>Se connecter à un service web hébergé sur un réseau virtuel
Une fois que votre service Gestion des API est connecté au réseau virtuel, l’accès aux services principaux de ce réseau est similaire à l’accès aux services publics. Tapez simplement l’adresse IP locale ou le nom d’hôte (si un serveur DNS est configuré pour le réseau virtuel) de votre service web dans le champ **URL du service web** lorsque vous créez ou modifiez une API.

![Ajouter des API à partir du VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"></a>Problèmes courants liés à la configuration du réseau
Voici une liste des problèmes courants de configuration incorrecte qui peuvent se produire lors du déploiement du service de gestion des API dans un réseau virtuel.

* **Configuration du serveur DNS personnalisée** : le service de la gestion des API dépend de plusieurs services Azure. Si la gestion des API est hébergée dans un réseau virtuel comportant un serveur DNS personnalisé, il doit résoudre les noms d’hôte de ces services Azure. Veuillez suivre [ce](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) guide sur la configuration de serveurs DNS personnalisée. Consultez le tableau des ports ci-dessous et les autres exigences en matière de réseau pour référence.

> [!IMPORTANT]
> Si vous envisagez d’utiliser des serveurs DNS personnalisés pour le réseau virtuel, vous devez le configurer **avant** d’y déployer un service Gestion des API. Sinon, vous devez mettre à jour le service Gestion des API chaque fois que vous changez les serveurs DNS en exécutant l’[opération Appliquer une configuration réseau](https://docs.microsoft.com/rest/api/apimanagement/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Ports requis pour la gestion des API** : le trafic entrant et sortant dans un sous-réseau dans lequel est déployée la gestion des API peut être contrôlé à l’aide du [groupe de sécurité réseau][Network Security Group]. Si ces ports ne sont pas disponibles, la gestion des API risque de ne pas fonctionner correctement et d’être inaccessible. Le blocage d’un ou plusieurs de ces ports constitue un autre problème de configuration courant lorsque vous utilisez la gestion des API dans un réseau virtuel.

<a name="required-ports"> </a> Lorsqu’une instance de service de gestion des API est hébergée dans un réseau virtuel, les ports dans le tableau suivant sont utilisées.

| Port(s) source / de destination | Direction          | Protocole de transfert |   [Balises de service](../virtual-network/security-overview.md#service-tags) <br> Source / Destination   | Objectif ( * )                                                 | Type de réseau virtuel |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Trafic entrant            | TCP                | INTERNET / VIRTUAL_NETWORK            | Communication client avec Gestion des API                      | Externe             |
| * / 3443                     | Trafic entrant            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Point de terminaison de gestion pour le portail Azure et Powershell         | Externe et interne  |
| * / 80, 443                  | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / Storage             | **Dépendance sur le Stockage Azure**                             | Externe et interne  |
| * / 80, 443                  | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Azure Active Directory (le cas échéant)                   | Externe et interne  |
| * / 1433                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / SQL                 | **Accès aux points de terminaison de SQL Azure**                           | Externe et interne  |
| * / 5672                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / EventHub            | Dépendance du journal pour la stratégie Event Hub et l’agent de surveillance | Externe et interne  |
| * / 445                      | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / Storage             | Dépendance sur le partage de fichiers Azure pour GIT                      | Externe et interne  |
| * / 1886                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / INTERNET            | Nécessaire pour publier l’état d’intégrité sur Resource Health          | Externe et interne  |
| * / 443                     | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publier les journaux de diagnostic et les métriques                        | Externe et interne  |
| * / 25                       | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / INTERNET            | Se connecter au relais SMTP pour envoyer des e-mails                    | Externe et interne  |
| * / 587                      | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / INTERNET            | Se connecter au relais SMTP pour envoyer des e-mails                    | Externe et interne  |
| * / 25028                    | Règle de trafic sortant           | TCP                | VIRTUAL_NETWORK / INTERNET            | Se connecter au relais SMTP pour envoyer des e-mails                    | Externe et interne  |
| * / 6381 - 6383              | Trafic entrant et sortant | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Accès aux instances du Cache Azure pour Redis entre instances de rôle          | Externe et interne  |
| * / *                        | Trafic entrant            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Équilibrage de charge de l’infrastructure Azure                          | Externe et interne  |

>[!IMPORTANT]
> Les ports pour lesquels *l’objectif* est indiqué en **gras** sont nécessaires au déploiement du service Gestion des API. Toutefois, le blocage des autres ports entraîne une dégradation de la capacité à utiliser et à surveiller le service en cours d’exécution.

+ **Fonctionnalité SSL** : pour activer la génération et la validation de la chaîne de certification SSL, le service de gestion des API nécessite une connectivité réseau sortante vers ocsp.msocsp.com, mscrl.microsoft.com et crl.microsoft.com. Cette dépendance n’est pas requise si l’un des certificats que vous chargez sur la gestion de API contient la totalité de la chaîne permettant d’accéder à la racine de l’AC.

+ **Accès DNS** : L’accès sortant sur le port 53 est nécessaire pour la communication avec des serveurs DNS. S'il existe un serveur DNS personnalisé à l'autre extrémité d'une passerelle VPN, le serveur DNS doit être accessible depuis le sous-réseau hébergeant la gestion de l’API.

+ **Métriques et supervision de l’intégrité** : connectivité réseau sortante aux points de terminaison de la supervision Azure, qui se résolvent sous les domaines suivants :

    | Environnement Azure | Points de terminaison                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure (public)      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com où `East US 2` est eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Relais SMTP** : Connectivité réseau sortante pour le relais SMTP, qui se résout sur l’hôte `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` et `ies.global.microsoft.com`

+ **CAPTCHA du portail des développeurs** : Connectivité réseau sortante pour CAPTCHA du portail de développeur, qui se résout sur l’hôte `client.hip.live.com`.

+ **Diagnostics du portail Azure** : pour activer le flux des journaux de diagnostic à partir du portail Azure lors de l’utilisation de l’extension de la gestion des API depuis l’intérieur d’un réseau virtuel, un accès sortant vers `dc.services.visualstudio.com` sur le port 443 est requis. Cela permet de résoudre les problèmes que vous pouvez rencontrer lors de l’utilisation d’extension.

+ **Forcer le mode Tunneling de trafic vers le pare-feu local à l’aide d’Appliance virtuelle réseau ou Express Route**: Une configuration client courante est de définir leur propre itinéraire par défaut (0.0.0.0/0), ce qui force tout le trafic à partir de l’API de gestion déléguée sous-réseau au flux via un pare-feu en local ou vers une appliance virtuelle réseau. Ce flux de trafic interrompt la connectivité avec la gestion des API Azure, car le trafic sortant peut être bloqué sur site, ou faire l’objet d’une opération NAT sur un jeu d’adresses non reconnaissable qui ne fonctionne plus avec différents points de terminaison Azure. La solution vous oblige à faire deux choses :

  * Activer les points de terminaison de service sur le sous-réseau dans lequel le service de gestion des API est déployé. [Points de terminaison de service] [ ServiceEndpoints] doivent être activés pour Sql Azure, stockage Azure, Azure EventHub et Azure ServiceBus. L’activation de points de terminaison directement à partir de la gestion des API sous-réseau déléguée à ces services leur permet d’utiliser le réseau principal Microsoft Azure en fournissant un routage optimal pour le trafic de service. Si vous utilisez des points de terminaison de Service avec une gestion des Api en tunnel forcé, les services Azure ci-dessus, le trafic n’est pas forcé par tunnel. Les autres API de gestion du trafic de dépendance de service est forcé par tunnel et ne peut être perdue ou le service de gestion des API ne fonctionnerait pas correctement.
    
  * Tout le contrôle plan le trafic à partir d’Internet vers le point de terminaison de gestion de votre service de gestion des API sont acheminées via un ensemble spécifique d’adresses IP d’entrant hébergé par la gestion des API. Lorsque le trafic est tunnélisé de force les réponses symétriquement ne copiera pas à ces adresses IP sources entrantes. Pour surmonter la limitation, nous devons ajouter les itinéraires suivants définis par l’utilisateur ([UDR][UDRs]) pour diriger le trafic vers Azure en définissant la destination de ces itinéraires d’hôte pour « Internet ». L’ensemble des adresses IP de trafic entrant pour le trafic du plan de contrôle est comme suit :
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * Pour les autres dépendances de service de gestion des API qui sont tunnélisés de force, il doit être un moyen de résoudre le nom d’hôte et d’atteindre le point de terminaison. Citons notamment
      - Contrôle d’intégrité et métriques
      - Portail Azure Diagnostics
      - Relais SMTP
      - Portail des développeurs CAPTCHA

## <a name="troubleshooting"> </a>Résolution des problèmes
* **Installation initiale** : si le déploiement initial du service Gestion des API sur un sous-réseau échoue, nous vous recommandons de commencer par déployer une machine virtuelle sur ce sous-réseau. Ensuite, utilisez le Bureau à distance pour vous connecter à la machine virtuelle et confirmez qu’il existe une connectivité à chacune des ressources de votre abonnement Azure mentionnées ci-dessous.
    * Stockage Blob Azure
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > Après avoir validé la connectivité, veillez à supprimer toutes les ressources déployées sur le sous-réseau avant d’y déployer le service Gestion des API.

* **Mises à jour incrémentielles** : Pour apporter des modifications à votre réseau, reportez-vous à [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/networkstatus), pour vérifier que le service de gestion des API n’a pas perdu l’accès à toutes les ressources critiques, dont il dépend. L’état de connectivité doit être mis à jour toutes les 15 minutes.

* **Liens de navigation de ressources** : pendant le déploiement sur le sous-réseau de réseau virtuel de type Gestionnaire des ressources, le service Gestion des API réserve le sous-réseau, en créant un lien de navigation de ressource. Si le sous-réseau contient déjà une ressource d’un autre fournisseur, le déploiement **échoue**. De même, quand vous déplacez un service Gestion des API vers un autre sous-réseau ou que vous le supprimez, nous supprimons ce lien de navigation de ressource.

## <a name="subnet-size"> </a> Exigence de taille du sous-réseau
Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et 3 adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Outre les adresses IP utilisées par l’infrastructure de réseau virtuel Azure, chaque instance Gestion des API du sous-réseau utilise deux adresses IP par unité de référence SKU Premium ou une adresse IP pour la référence SKU Développeur. Chaque instance réserve une adresse IP supplémentaire pour l’équilibreur de charge externe. Lors du déploiement dans un réseau virtuel interne, une adresse IP supplémentaire est requise pour l’équilibreur de charge interne.

Le calcul ci-dessus, la taille minimale du sous-réseau, dans lequel la gestion des API peut être déployée est/29, donnant offre trois adresses IP.

## <a name="routing"> </a> Routage
+ Une adresse IP publique à charge équilibrée (adresse IP virtuelle) est réservée pour fournir l’accès à tous les points de terminaison de service.
+ Une adresse IP d’une plage d’adresses IP de sous-réseau (adresse IP dynamique) est utilisée pour accéder aux ressources sur le réseau virtuel, tandis qu’une adresse IP publique (adresse IP virtuelle) est utilisée pour accéder aux ressources à l’extérieur du réseau virtuel.
+ L’adresse IP publique à charge équilibrée se trouve dans le panneau Vue d’ensemble/Bases sur le portail Azure.

## <a name="limitations"> </a>Limitations
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas contenir d’autres types de ressource Azure.
* Le sous-réseau et le service Gestion des API doivent figurer dans le même abonnement.
* Un sous-réseau contenant des instances du service Gestion des API ne peut pas être déplacé entre des abonnements.
* Pour les déploiements du service Gestion des API dans plusieurs régions configurés en mode de réseau virtuel interne, les utilisateurs sont chargés de gérer leur équilibrage de charge entre les différentes régions, car ils possèdent le routage.
* La connectivité entre une ressource d’un réseau virtuel homologué à l’échelle mondiale dans une autre région et le service Gestion des API en mode interne ne fonctionnera pas en raison de la limitation de la plateforme. Pour plus d’informations, consultez la section [Les ressources situées dans un réseau virtuel ne peuvent pas communiquer avec l’adresse IP d’un équilibreur de charge interne Azure dans le réseau virtuel homologué](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).


## <a name="related-content"></a>Contenu connexe
* [Connexion d’un réseau virtuel au serveur principal à l’aide de la passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Connexion d’un réseau virtuel utilisant des modèles de déploiement différents](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure](api-management-howto-api-inspector.md)
* [FAQ sur le réseau virtuel](../virtual-network/virtual-networks-faq.md)
* [Balises de service](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
