---
title: Résoudre les problèmes de connectivité Azure Private Link
description: Guide pas à pas pour diagnostiquer la connectivité de liaison privée
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191055"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Résoudre les problèmes de connectivité au service Private Link

Ce guide fournit des instructions pas à pas pour valider et diagnostiquer la connectivité pour votre installation du service Private Link. 

Azure Private Link vous permet d’accéder aux services PaaS Azure (par exemple Stockage Azure, Azure Cosmos DB et SQL Database) ainsi qu’aux services de partenaires ou de clients hébergés par Azure sur un point de terminaison privé sur votre réseau virtuel. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez également créer votre propre service Private Link sur votre réseau virtuel et le distribuer en privé à vos clients. 

Vous pouvez activer votre service en cours d’exécution derrière Azure Standard Load Balancer pour l’accès à Private Link. Les consommateurs de votre service peuvent créer un point de terminaison privé à l’intérieur de leur réseau virtuel, et le mapper à ce service pour y accéder en privé.

Voici les scénarios de connectivité disponibles avec le service Private Link :
- Réseau virtuel de la même région 
- Réseaux virtuels avec appairage régional
- Réseaux virtuels avec appairage mondial
- Circuits Express Route ou client local via VPN

## <a name="deployment-troubleshooting"></a>Résolution des problèmes liés au déploiement

Passez en revue les informations sur la [désactivation des stratégies réseau sur le service Private Link](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) afin d’obtenir des informations sur la résolution de problèmes lors desquels vous ne pouvez pas sélectionner l’adresse IP source à partir du sous-réseau de votre choix pour votre service Private Link.

Vérifiez que le paramètre **privateLinkServiceNetworkPolicies** est désactivé pour le sous-réseau à partir duquel vous sélectionnez l’adresse IP source.

## <a name="diagnosing-connectivity-problems"></a>Diagnostic des problèmes de connectivité

Si vous rencontrez des problèmes de connectivité avec votre configuration de liaison privée, passez en revue les étapes listées ci-dessous pour vous assurer que toutes les configurations habituelles sont telles que prévu.

1. Passez en revue la configuration du service Private Link en parcourant la ressource. 

    a) Accédez au **Centre de liaisons privées**.

      ![Centre de liaisons privées](./media/private-link-tsg/private-link-center.png)

    b) Sélectionnez **Services de liaison privée** dans le volet de navigation gauche.

      ![Services de liaison privée](./media/private-link-tsg/private-link-service.png)

    c) Filtrez et sélectionnez le service de liaison privée que vous souhaitez diagnostiquer.

    d) Passez en revue les connexions de point de terminaison privé.
     - Vérifiez que l’état de la connexion du point de terminaison privé à partir duquel vous recherchez la connectivité est **Approuvée**. 
     - S’il est **En attente**, sélectionnez-le et approuvez-le. 

       ![Connexions des points de terminaison privés](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - Accédez au point de terminaison privé à partir duquel vous vous connectez en cliquant sur son nom. Vérifiez que l’état de la connexion est **Approuvée**.

       ![Vue d’ensemble de connexion Private Endpoint](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Une fois les deux côtés approuvés, réessayez de vous connecter.

    e) Vérifiez **Alias** sous l’onglet Vue d’ensemble et **ID de ressource** sous l’onglet Propriétés. 
     - Vérifiez que **l’alias et l’ID de ressource** correspondent à **l’alias et l’ID de ressource** que vous utilisez pour créer un point de terminaison privé à ce service. 

       ![Vérifiez l’alias](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Vérifiez l’ID de ressource](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) Passez en revue les informations sur la visibilité (section Vue d’ensemble).
     - Vérifiez que votre abonnement se trouve dans l’étendue de **Visibilité**.

       ![Vérifiez la visibilité](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) Passez en revue les informations de Load Balancer (Vue d’ensemble)
     - Vous pouvez accéder à l’équilibreur de charge en cliquant sur le lien Load Balancer.

       ![Vérifiez Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Vérifiez que les paramètres de Load Balancer sont configurés conformément à vos attentes.
       - Passez en revue la configuration IP front-end.
       - Passez en revue les pools de back-ends.
       - Passez en revue les règles d’équilibrage de charge.

       ![Vérifiez les propriétés de Load Balancer.](./media/private-link-tsg/pls-ilb-properties.png)

     - Vérifiez que Load Balancer fonctionne avec les paramètres ci-dessus.
       - Sélectionnez une machine virtuelle sur un sous-réseau autre que celui où le pool de back-ends Load Balancer est disponible.
       - Essayez d’accéder au front-end Load Balancer à partir de la machine virtuelle ci-dessus.
       - Si la connexion est établie au pool de back-ends conformément aux règles d’équilibrage de charge, votre équilibreur de charge est opérationnel.
       - Vous pouvez également passer en revue la métrique Load Balancer par le biais d’Azure Monitor pour voir si les données passent par l’équilibreur de charge.

2. Utilisez [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) pour examiner les données transmises.

    a) Sur une ressource de service Private Link, sélectionnez **Métriques**.
     - Sélectionnez **Octets d’entrée** ou **Octets de sortie**.
     - Vérifiez que les données sont transmises lors de la tentative de connexion au service Private Link. Un délai d’environ 10 minutes est attendu.

       ![Vérifiez les métriques du service Private Link](./media/private-link-tsg/pls-metrics.png)

3. Si votre problème n’est toujours pas résolu et que le problème de connectivité persiste, contactez l’[équipe de support Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). 

## <a name="next-steps"></a>Étapes suivantes

 * [Créer un service Private Link (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [Guide de résolution des problèmes Private Endpoint](troubleshoot-private-endpoint-connectivity.md).
