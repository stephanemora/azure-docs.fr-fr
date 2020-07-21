---
title: Nouveautés d’Azure Application Gateway
description: Découvrez les nouveautés d’Azure Application Gateway, notamment les dernières notes de publication, les problèmes connus, les corrections de bogues, les fonctionnalités dépréciées et les changements à venir.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: ebb3e6ba777646d39e3732215aa6c8fdd12ca40a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186688"
---
# <a name="whats-new-in-azure-application-gateway"></a>Nouveautés d’Azure Application Gateway

Azure Application Gateway est mis à jour en continu. Pour vous informer des développements les plus récents, cet article détaille les thèmes suivants :

- Versions les plus récentes
- Problèmes connus
- Résolution des bogues
- Fonctionnalités dépréciées

## <a name="new-features"></a>Nouvelles fonctionnalités

|Fonctionnalité  |Description  |Date de l’ajout  |
|---------|---------|---------|
| Port personnalisé pour les sondes d’intégrité | La référence SKU Application Gateway v2 offre désormais la possibilité de spécifier un port personnalisé dans la configuration de la sonde d’intégrité. Pour plus d’informations, consultez la [vue d’ensemble des sondes d’intégrité](application-gateway-probe-overview.md). | Juillet 2020
| Module complémentaire AKS Application Gateway Ingress Controller (AGIC) (Préversion) |Le contrôleur d’entrée Application Gateway peut désormais être déployé comme module complémentaire AKS natif, en une seule ligne via Azure CLI. En tant que module complémentaire AKS, AGIC peut se transformer en service entièrement géré, tout en continuant de s’exécuter dans le cluster AKS du client. Pour plus d’informations, consultez [Différences avec le module complémentaire AGIC](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). |Juin 2020 |
| Routes définies par l’utilisateur (UDR) sur v2 (préversion) |Les routes définies par l’utilisateur sont désormais pris en charge dans certains scénarios sur les références SKU Application Gateway v2. Pour plus d’informations, consultez [Présentation de la configuration d’Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Mars 2020 |
|Modifications du cookie d’affinité |Quand l’affinité basée sur les cookies est activée, Application Gateway injecte un autre cookie identique appelé *ApplicationGatewayAffinityCORS* en plus du cookie ApplicationGatewayAffinity existant. *ApplicationGatewayAffinityCORS* a deux attributs supplémentaires qui lui sont ajoutés (*SameSite=None; Secure*) afin que les sessions persistantes soient conservées même pour les requêtes cross-origin. Pour plus d’informations, consultez [Affinité basée sur les cookies dans Application Gateway](configuration-overview.md#cookie-based-affinity). |Février 2020 |
|Améliorations de la sonde |Grâce aux améliorations de la sonde personnalisées dans la référence SKU Application Gateway v2, nous avons simplifié la [configuration de la sonde](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), facilité les [tests d’intégrité principale à la demande](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) et ajouté [des informations de diagnostic](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) pour vous aider à résoudre des problèmes d’intégrité principale.  |2 octobre 2019 |
|Métriques supplémentaires |Nous avons ajouté les nouvelles métriques suivantes pour vous aider à superviser votre référence SKU Application Gateway v2 : [Métriques liées au minutage](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), État de la réponse du back-end, Octets reçus, Octets envoyés, Protocole TLS du client et Unités de calcul actuelles. Consultez [Métriques prises en charge par le SKU Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Août 2019 |
|Règles personnalisées WAF |Application Gateway WAF_v2 prend désormais en charge la création de règles personnalisées. Consultez [Règles personnalisées Application Gateway](custom-waf-rules-overview.md). |Juin 2019 |
|Mise à disposition générale des fonctions de mise à l’échelle automatique, de redondance de zone et de prise en charge des adresses IP virtuelles statiques |Mise à disposition générale de la référence SKU version 2, qui améliore les performances et prend en charge la mise à l’échelle automatique, la redondance de zone et la prise en charge des adresses IP virtuelles statiques, ainsi que Key Vault et la réécriture des en-têtes. Consultez la [documentation relative à la mise à l’échelle automatique de Application Gateway](application-gateway-autoscaling-zone-redundant.md). |Avril 2019 |
|Intégration dans Key Vault |Application Gateway prend désormais en charge l’intégration dans Key Vault (dans la préversion publique) des certificats de serveur associés à des écouteurs activés via HTTPS. Consultez [Arrêt SSL avec certificats Key Vault](key-vault-certs.md). |Avril 2019 |
|Opérations de réécriture et CRUD sur les en-têtes     |Vous pouvez désormais réécrire les en-têtes HTTP. Voir le [tutoriel : Créer une passerelle d’application et réécrire des en-têtes HTTP](tutorial-http-header-rewrite-powershell.md) pour plus d’informations.|Décembre 2018|
|Configuration de WAF et liste d’exclusions     |Nous avons ajouté des options pour vous aider à configurer votre WAF et à réduire les faux positifs. Pour plus d’informations, consultez [Limites de la taille des demandes adressées au pare-feu d’application web et listes d’exclusions](application-gateway-waf-configuration.md).|Décembre 2018|
|Mise à l’échelle automatique, redondance de zone et prise en charge des adresses IP virtuelles statiques      |La référence SKU v2 apporte de nombreuses améliorations, dont la mise à l’échelle automatique et l’accroissement des performances. Consultez [Nouveautés d’Azure Application Gateway](overview.md) pour plus d’informations.|Septembre 2018|
|Vidage des connexions     |Le vidage des connexions vous permet de supprimer correctement des membres de vos pools de back-ends. Pour plus d’informations, consultez [Vidage des connexions](features.md#connection-draining).|Septembre 2018|
|Pages d’erreur personnalisées     |Avec les pages d’erreurs personnalisées, vous pouvez créer une page d’erreur dans le format du reste de vos sites web. Pour y parvenir, consultez [Créer des pages d’erreur personnalisées Application Gateway](custom-error.md).|Septembre 2018|
|Améliorations des métriques     |Vous pouvez obtenir une meilleure vue de l’état de votre passerelle Application Gateway avec des métriques améliorées. Pour activer les métriques sur votre passerelle Application Gateway, consultez [Intégrité du serveur principal, journaux de diagnostic et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).|Juin 2018|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Application Gateway, consultez [Présentation d’Azure Application Gateway](overview.md).
