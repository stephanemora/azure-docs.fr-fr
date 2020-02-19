---
title: Stratégies de point de terminaison de service de réseau virtuel Azure | Microsoft Docs
description: Découvrez comment filtrer le trafic de réseau virtuel vers les ressources de service Azure à l’aide de stratégies de point de terminaison de service
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sumi
ms.openlocfilehash: 1aa4328a6d5367ef356ce33807289a873c93d90f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056697"
---
# <a name="virtual-network-service-endpoint-policies-preview"></a>Stratégies de point de terminaison de service de réseau virtuel (préversion)

Les stratégies de points de terminaison de service de réseau virtuel permettent de filtrer le trafic de réseau virtuel vers les services Azure, en autorisant uniquement des ressources de service Azure spécifiques, sur les points de terminaison de service. Les stratégies de point de terminaison fournissent un contrôle d’accès granulaire pour le trafic de réseau virtuel vers les services Azure.

Cette fonctionnalité est disponible en __évaluation__ dans toutes les régions Azure publiques pour Stockage Azure.

Pour obtenir des notifications actualisées concernant la préversion, consultez la page [Mises à jour du réseau virtuel Azure](https://azure.microsoft.com/updates/?product=virtual-network).

> [!NOTE]  
> Dans la préversion, les stratégies de point de terminaison de service de réseau virtuel peuvent afficher un niveau de disponibilité et une fiabilité différents de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="key-benefits"></a>Principaux avantages

Les stratégies de point de terminaison de service de réseau virtuel offrent les avantages suivants :

- __Sécurité renforcée pour votre trafic de réseau virtuel vers les services Azure__

  Les [balises de service Azure pour les groupes de sécurité réseau](https://aka.ms/servicetags) vous permettent de restreindre le trafic sortant du réseau virtuel vers des services Azure spécifiques. Cependant, elles autorisent le trafic vers toutes les ressources de ce service Azure. 
  
  Avec les stratégies de point de terminaison, vous pouvez désormais restreindre l’accès sortant du réseau virtuel à des ressources Azure spécifiques. Cela apporte un contrôle de sécurité beaucoup plus granulaire pour la protection des données accessibles dans votre réseau virtuel. 

- __Stratégies évolutives et hautement disponibles pour filtrer le trafic de service Azure__

   Les stratégies de point de terminaison fournissent une solution évolutive horizontalement et hautement disponible pour filtrer le trafic de service Azure à partir de réseaux virtuels, sur les points de terminaison de service. Aucun temps système supplémentaire n’est nécessaire afin de maintenir les appliances réseau centrales pour ce trafic sur vos réseaux virtuels.

## <a name="configuration"></a>Configuration

- Vous pouvez configurer les stratégies de point de terminaison pour limiter le trafic de réseau virtuel à des ressources de service Azure spécifiques. Pour la préversion, nous prenons en charge les stratégies de point de terminaison associées à Stockage Azure. 
- La stratégie de point de terminaison de service est configurée sur un sous-réseau dans un réseau virtuel. Les points de terminaison de service doivent être activés sur le sous-réseau afin que la stratégie soit appliquée, pour tous les services Azure répertoriés dans la stratégie.
- La stratégie de point de terminaison vous permet d’ajouter à la liste verte des ressources de service Azure spécifiques, en utilisant le format resourceID. Vous pouvez restreindre l’accès à toutes les ressources dans un abonnement ou un groupe de ressources. Vous pouvez également restreindre l’accès à des ressources spécifiques. 
- Par défaut, si aucune stratégie n’est attachée à un sous-réseau avec des points de terminaison, vous pouvez accéder à toutes les ressources du service. Une fois qu’une stratégie est configurée sur ce sous-réseau, seules les ressources spécifiées dans la stratégie sont accessibles à partir d’instances de calcul dans ce sous-réseau. L’accès à toutes les autres ressources pour le service spécifique sera refusé. 
- Vous pouvez filtrer le trafic vers les ressources de service Azure dans les régions où le point de terminaison de service est configuré. L’accès aux ressources de service dans d’autres régions sera autorisé par défaut. 

  > [!NOTE]  
  > Pour verrouiller entièrement l’accès sortant du réseau virtuel vers les ressources Azure dans les régions de point de terminaison de service, vous avez également besoin de règles de groupe de sécurité réseau configurées pour autoriser le trafic uniquement vers les régions de point de terminaison de service, au moyen de [balises de service](security-overview.md#service-tags).

- Vous pouvez appliquer plusieurs stratégies à un sous-réseau. Lorsque plusieurs stratégies sont associées au sous-réseau, pour le même service, le trafic de réseau virtuel vers les ressources spécifiées dans toutes ces stratégies est autorisé. L’accès à toutes les autres ressources de service, qui ne sont pas spécifiées dans ces stratégies, est refusé. 

  > [!NOTE]  
  > La stratégie autorise uniquement l’accès aux ressources de service répertoriées à partir d’un réseau virtuel. Tout autre trafic vers le service est refusé automatiquement lorsque vous ajoutez des ressources spécifiques à la stratégie. Assurez-vous que toutes les dépendances de ressource de service pour vos applications peuvent être identifiées et répertoriées dans la stratégie.

  > [!WARNING]  
  > Les services Azure déployés dans votre réseau virtuel, comme Azure HDInsight, accèdent aux autres services Azure, comme Stockage Azure, pour les besoins de l’infrastructure. La restriction de stratégie de point de terminaison à des ressources spécifiques peut bloquer l’accès à ces ressources d’infrastructure pour les services déployés dans votre réseau virtuel. Pour des services spécifiques, reportez-vous à [Limites](#limitations). Dans la préversion, les stratégies de point de terminaison de service ne sont pas prises en charge pour les services Azure gérés qui sont déployés dans votre réseau virtuel.

- Pour Stockage Azure : 
  -  Vous pouvez restreindre l’accès en répertoriant la valeur *resourceId* Azure Resource Manager du compte de stockage. Cela concerne le trafic vers les objets blob, les tables, les files d’attente, les fichiers et Azure Data Lake Storage Gen2.

     Par exemple, les comptes de stockage Azure peuvent apparaître dans la définition de stratégie de point de terminaison comme indiqué ci-dessous :
    
     Pour autoriser un compte de stockage spécifique :         
     `subscriptions/subscriptionId/resourceGroups/resourceGroup/providers/Microsoft.Storage/storageAccounts/storageAccountName`
      
     Pour autoriser tous les comptes d’un abonnement et d’un groupe de ressources : `/subscriptions/subscriptionId/resourceGroups/resourceGroup`
     
     Pour autoriser tous les comptes d’un abonnement : `/subscriptions/subscriptionId`
    
- Seuls les comptes de stockage utilisant le modèle de ressource Azure peuvent être spécifiés dans la stratégie de point de terminaison.  

  > [!NOTE]  
  > L’accès aux comptes de stockage classiques est bloqué avec les stratégies de point de terminaison.

- L’emplacement principal du compte répertorié doit se trouver dans les régions de la géopaire du point de terminaison de service pour le sous-réseau. 

  > [!NOTE]  
  > Les stratégies permettent de spécifier les ressources de service d’autres régions. L’accès du réseau virtuel aux services Azure est filtré uniquement pour les régions de la géopaire. Si les groupes de sécurité réseau ne sont pas limités aux régions de la géopaire pour Stockage Azure, le réseau virtuel peut accéder à tous les comptes de stockage en dehors des régions de la géopaire.

- L’accès secondaire RA-GRS est automatiquement autorisé si le compte principal est répertorié. 
- Les comptes de stockage peuvent se trouver dans le même abonnement, dans un autre abonnement ou dans le locataire Azure Active Directory en tant que réseau virtuel. 

## <a name="limitations"></a>Limites

- Vous pouvez uniquement déployer des stratégies de point de terminaison de service sur des réseaux virtuels déployés à l’aide du modèle de déploiement Azure Resource Manager.
- Les réseaux virtuels doivent se trouver dans la même région que la stratégie de point de terminaison de service.
- Vous pouvez uniquement appliquer la stratégie de point de terminaison de service sur un sous-réseau si les points de terminaison de service sont configurés pour les services Azure répertoriés dans la stratégie.
- Vous ne pouvez pas utiliser les stratégies de points de terminaison de service pour le trafic allant de votre réseau local aux services Azure.
- Les stratégies de point de terminaison ne doivent pas être appliquées aux sous-réseaux avec des services Azure gérés, avec une dépendance sur les services Azure pour les besoins de l’infrastructure. 

  > [!WARNING]  
  > Les services Azure déployés dans votre réseau virtuel, comme Azure HDInsight, accèdent aux autres services Azure, comme Stockage Azure, pour les besoins de l’infrastructure. La restriction de stratégie de point de terminaison à des ressources spécifiques peut bloquer l’accès à ces ressources d’infrastructure pour les services Azure déployés dans votre réseau virtuel.
  
  - Certains services Azure peuvent être déployés dans des sous-réseaux avec d’autres instances de calcul. Vérifiez que les stratégies de point de terminaison ne sont pas appliquées au sous-réseau, si les services gérés répertoriés ci-dessous sont déployés dans le sous-réseau.
   
    - Azure HDInsight
    - Azure Batch (Azure Resource Manager)
    - Azure Active Directory Domain Services (Azure Resource Manager)
    - Azure Application Gateway (Azure Resource Manager)
    - Passerelle VPN Azure (Azure Resource Manager)
    - Pare-feu Azure

  - Certains services Azure sont déployés dans des sous-réseaux dédiés. Les stratégies de point de terminaison sont bloquées sur tous ces services, répertoriés ci-dessous, dans la préversion. 

     - Environnement Azure App Service
     - Azure Rediscache
     - Gestion des API Azure
     - Azure SQL Managed Instance
     - Azure Active Directory Domain Services
     - Azure Application Gateway (classique)
     - Passerelle VPN Azure (classique)

- Stockage Azure : Les comptes de stockage classiques ne sont pas pris en charge dans les stratégies de point de terminaison. Par défaut, les stratégies refusent l’accès à tous les comptes de stockage classiques. Si votre application doit accéder à Azure Resource Manager et à des comptes de stockage classiques, les stratégies de point de terminaison ne doivent pas être utilisées pour ce trafic. 

## <a name="nsgs-with-service-endpoint-policies"></a>NSG avec stratégies de points de terminaison de service
- Par défaut, les NSG autorisent le trafic Internet sortant, y compris le trafic de votre réseau virtuel vers les services Azure.
- Si vous souhaitez refuser tout trafic Internet sortant et autoriser le trafic uniquement vers des ressources de services Azure spécifiques : 

  Étape 1 : Configurez les NSG pour autoriser le trafic sortant uniquement vers les services Azure dans les régions de point de terminaison, à l’aide de *balises de service Azure*. Pour plus d’informations, consultez [Balises de Service Azure pour les groupes de sécurité réseau](https://aka.ms/servicetags)
      
  Les règles de groupe de sécurité réseau qui restreignent l’accès aux seules régions de point de terminaison peuvent ressembler à ce qui suit :

  ```
  Allow AzureStorage.WestUS2,
  Allow AzureStorage.WestCentralUS,
  Deny all
  ```

  Étape 2 : Appliquez la stratégie de point de terminaison de service avec un accès uniquement aux ressources du service Azure spécifique.

  > [!WARNING]  
  > Si le groupe de sécurité réseau n’est pas configuré pour limiter l’accès du service Azure d’un réseau virtuel aux régions de point de terminaison, vous pouvez accéder aux ressources de service dans d’autres régions, même si la stratégie de point de terminaison de service est appliquée.

## <a name="scenarios"></a>Scénarios

- **Réseaux virtuels appairés, connectés ou multiples** : Pour filtrer le trafic dans des réseaux virtuels appairés, les stratégies de point de terminaison doivent leur être appliquées individuellement.
- **Filtrage du trafic Internet avec des appliances réseau ou le Pare-feu Azure** : Filtrez le trafic du service Azure avec des stratégies, sur les points de terminaison et filtrez le reste du trafic Internet ou Azure par le biais d’appliances ou du Pare-feu Azure. 
- **Filtrage du trafic sur les services Azure déployés dans des réseaux virtuels** : Dans la préversion, les stratégies de point de terminaison de service ne sont pas prises en charge pour les services Azure managés qui sont déployés dans votre réseau virtuel. 
 Pour les services spécifiques, consultez [Limites.](#limitations)
- **Filtrage du trafic local vers les services Azure** : Les stratégies de point de terminaison de service s’appliquent uniquement au trafic en provenance des sous-réseaux associés aux stratégies. Pour autoriser l’accès à des ressources de service Azure spécifiques en local, le trafic doit être filtré à l’aide d’appliances de réseau virtuel ou de pare-feu.

## <a name="logging-and-troubleshooting"></a>Journalisation et résolution des problèmes
Aucun enregistrement centralisé n’est disponible pour les stratégies de point de terminaison de service. Pour les journaux de diagnostic de service, consultez [Service endpoints logging](virtual-network-service-endpoints-overview.md#logging-and-troubleshooting) (Enregistrement des points de terminaison de service).

### <a name="troubleshooting-scenarios"></a>Scénarios de résolution des problèmes
- Accès autorisé aux comptes de stockage non répertoriés dans les stratégies de point de terminaison
  - Les groupes de sécurité réseau peuvent autoriser l’accès à Internet ou aux comptes Stockage Azure dans d’autres régions.
  - Les groupes de sécurité réseau doivent être configurés pour refuser tout trafic Internet sortant et autoriser uniquement le trafic vers des régions spécifiques de Stockage Azure. Pour plus d’informations, consultez Groupes de sécurité réseau.
- L’accès est refusé aux comptes répertoriés dans les stratégies de point de terminaison
  - Le filtrage des groupes de sécurité réseau ou du pare-feu peut bloquer l’accès
  - Si la suppression/nouvelle application de la stratégie entraîne la perte de la connectivité :
    - Vérifiez que le service Azure est configuré pour autoriser l’accès à partir du réseau virtuel, sur les points de terminaison, ou que la stratégie par défaut pour la ressource est définie sur *Allow All* (Tout autoriser).
      > [!NOTE]      
      > Les ressources de service n’ont pas besoin d’être sécurisées sur des réseaux virtuels pour accéder aux stratégies de point de terminaison. Toutefois, dans le cadre de meilleures pratiques de sécurité, nous recommandons de sécuriser les ressources de service sur vos réseaux approuvés, comme vos réseaux virtuels Azure, par le biais de points de terminaison de service et en local, par le biais d’un pare-feu IP.
  
    - Vérifiez que les diagnostics de service affichent le trafic sur les points de terminaison.
    - Vérifiez si les journaux de flux de groupe de sécurité réseau affichent l’accès et si les journaux d’activité de stockage affichent l’accès, comme prévu, sur les points de terminaison de service.
    - Contactez le support Azure.
- L’accès est refusé aux comptes non répertoriés dans les stratégies de point de terminaison de service
  - Le filtrage des groupes de sécurité réseau ou du pare-feu peut bloquer l’accès. Vérifiez que la balise de service *Stockage Azure* est autorisée pour les régions de point de terminaison. Pour les restrictions de stratégie, consultez [Limites](#limitations).
  Par exemple, l’accès aux comptes de stockage classiques est refusé si une stratégie est appliquée.
  - Vérifiez que le service Azure est configuré pour autoriser l’accès à partir du réseau virtuel, sur les points de terminaison, ou que la stratégie par défaut pour la ressource est définie sur *Allow All* (Tout autoriser).

## <a name="provisioning"></a>Approvisionnement

Les stratégies de points de terminaison de service peuvent être configurées sur les sous-réseaux par un utilisateur avec accès en écriture à un réseau virtuel. En savoir plus sur les [rôles intégrés](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure et l’assignation d’autorisations spécifiques aux [rôles personnalisés](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Les réseaux virtuels et les ressources du service Azure peuvent se trouver dans des abonnements identiques ou différents, ou dans des locataires Azure Active Directory. 

## <a name="pricing-and-limits"></a>Tarification et limites

L’utilisation de stratégies de point de terminaison de service n’engendre pas de frais supplémentaires. Le modèle de tarification actuel pour les services Azure (comme Stockage Azure) reste le même que celui utilisé actuellement, sur les points de terminaison de service.

Les limites suivantes sont appliquées sur les stratégies de point de terminaison de service : 

 |Ressource | Limite par défaut |
 |---------|---------------|
 |ServiceEndpointPoliciesPerSubscription |500 |
 |ServiceEndpintPoliciesPerSubnet|100 |
 |ServiceResourcesPerServiceEndpointPolicyDefinition|200 |

## <a name="next-steps"></a>Étapes suivantes

- Découvrez [comment configurer des stratégies de point de terminaison de service de réseau virtuel](virtual-network-service-endpoint-policies-portal.md)
- En savoir plus sur les [points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md)

