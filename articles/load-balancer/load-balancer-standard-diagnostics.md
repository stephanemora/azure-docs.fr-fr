---
title: Diagnostics avec les métriques, les alertes et l’intégrité des ressources – Azure Standard Load Balancer
description: Utilisez les métriques, les alertes et les informations d’intégrité des ressources disponibles pour diagnostiquer Azure Standard Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: f5fa39e07eba6bdf24d96e72c9229e215ff6730b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772038"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>Diagnostics Azure Standard Load Balancer avec les métriques, les alertes et l’intégrité des ressources

Azure Load Balancer Standard expose les fonctionnalités de diagnostic suivantes :

* **Métriques multidimensionnelles et alertes** : des fonctionnalités de diagnostic multidimensionnel sont proposées par le biais d’[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pour les configurations d’équilibreur standard. Vous pouvez surveiller, gérer et résoudre les problèmes de vos ressources d’équilibreur de charge standard.

* **Intégrité des ressources** : La page associée à l’équilibreur de charge dans le portail Azure et celle associée à l’intégrité des ressources (sous Monitor) affichent la section Intégrité des ressources pour Standard Load Balancer. 

Cet article propose une présentation rapide de ces fonctionnalités et des méthodes pour les utiliser dans Load Balancer Standard.

## <a name = "MultiDimensionalMetrics"></a>Métriques multidimensionnelles

Azure Load Balancer fournit des métriques multidimensionnelles par le biais des métriques Azure dans le portail Azure, et vous permet d’obtenir des informations de diagnostic en temps réel sur vos ressources d’équilibreur de charge. 

Les différentes configurations de Load Balancer Standard fournissent les métriques suivantes :

| Métrique | Type de ressource | Description | Agrégation recommandée |
| --- | --- | --- | --- |
| Disponibilité du chemin d’accès aux données (disponibilité VIP)| Équilibreur de charge interne et public | Load Balancer Standard teste en continu le chemin de données d’une région vers le serveur frontal de l’équilibreur de charge, jusqu’à la pile SDN qui prend en charge votre machine virtuelle. Tant que les instances saines restent, la mesure suit le même chemin que le trafic à charge équilibrée de vos applications. Le chemin de données utilisé par vos clients est également validé. La mesure est invisible pour votre application et n’interfère pas avec les autres opérations.| Average |
| État de la sonde d’intégrité (disponibilité DIP) | Équilibreur de charge interne et public | Load Balancer Standard utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de vos paramètres de configuration. Cette métrique fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool de l’équilibreur de charge. Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité. |  Average |
| Paquets SYN (synchroniser) | Équilibreur de charge interne et public | Load Balancer Standard ne termine pas les connexions Transmission Control Protocol (TCP) et n’interagit pas avec les flux de paquets UDP ou TCP. Les flux et leurs établissements de liaisons sont toujours entre la source et l’instance de machine virtuelle. Pour mieux résoudre les problèmes posés par vos scénarios de protocole TCP, vous pouvez utiliser les compteurs de paquets SYN pour comprendre le nombre de tentatives de connexion TCP effectuées. La métrique indique le nombre de paquets SYN TCP reçus.| Average |
| Connexions SNAT | Équilibreur de charge public |Load Balancer Standard indique le nombre de flux sortants usurpés sur le serveur frontal d’adresse IP public. Les ports de traduction d'adresses réseau source (SNAT) constituent une ressource épuisable. Cette métrique peut donner une idée de l’importance du rôle joué par SNAT dans votre application pour les flux sortants. Les compteurs relatifs aux flux SNAT sortants réussis et mis en échec sont indiqués et peuvent être utilisés pour comprendre l’intégrité de vos flux sortants et résoudre les problèmes associés.| Average |
| Compteurs d’octets |  Équilibreur de charge interne et public | Load Balancer Standard indique les données traitées par serveur frontal.| Average |
| Compteurs de paquets |  Équilibreur de charge interne et public | Load Balancer Standard indique les paquets traités par serveur frontal.| Average |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Afficher vos métriques d’équilibreur de charge dans le portail Azure

Le Portail Azure présente les métriques d’équilibreur de charge via la page Métriques, qui est disponible sur la page de ressource d’équilibreur de charge pour une ressource spécifique, ainsi que sur la page Azure Monitor. 

Pour afficher les métriques de vos ressources de Load Balancer Standard :
1. Accédez à la page Métriques et effectuez l’une des opérations suivantes :
   * Sur la page de ressource d’équilibreur de charge, sélectionnez le type de métrique dans la liste déroulante.
   * Sur la page Azure Monitor, sélectionnez la ressource d’équilibreur de charge.
2. Définissez le type d’agrégation appropriée.
3. Éventuellement, configurez le filtrage et le regroupement requis.

    ![Métriques pour Standard Load Balancer](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *Figure : Métrique de disponibilité du chemin d’accès aux données pour Standard Load Balancer*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>Récupérer les métriques multidimensionnelles par programme via des API

Pour obtenir des instructions relatives à l’API permettant de récupérer les définitions et valeurs de métrique multidimensionnelle, consultez [Procédure pas à pas d’utilisation de l’API REST d’Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api).

### <a name = "DiagnosticScenarios"></a>Scénarios de diagnostic courants et vues recommandées

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>Le chemin d’accès de données est-il opérationnel et disponible pour l’adresse IP virtuelle de mon équilibreur de charge ?

La métrique de disponibilité d’adresse IP virtuelle décrit l’intégrité du chemin d’accès de données dans la région vers l’hôte de calcul où se trouvent vos machines virtuelles. La métrique reflète l’intégrité de l’infrastructure Azure. Vous pouvez utiliser la métrique pour :
- Surveiller la disponibilité externe de votre service
- Aller plus loin et savoir si la plateforme sur laquelle votre service est déployé est intègre ou si votre système d’exploitation invité ou instance d’application est intègre.
- Déterminez si un événement est associé à votre service ou au plan de données sous-jacent. Ne confondez pas cette métrique avec l’état de la sonde d’intégrité (« disponibilité DIP »).

Pour obtenir la disponibilité du chemin d’accès aux données pour vos ressources Load Balancer Standard :
1. Assurez-vous que la ressource d’équilibreur de charge correcte est sélectionnée. 
2. Dans la liste déroulante **Métrique**, sélectionnez **Disponibilité du chemin d’accès aux données**. 
3. Dans la liste déroulante **Agrégation**, sélectionnez **Moy**. 
4. De plus, ajoutez un filtre sur l’adresse IP du serveur frontal ou le port frontal comme dimension avec l’adresse IP de serveur frontal ou le port de serveur frontal requis, puis regroupez-les en fonction de la dimension sélectionnée.

![Détection d’adresse IP virtuelle](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*Figure : Détails de la détection d’adresse IP frontale du Load Balancer*

La métrique est générée par une mesure intrabande active. Un service de détection dans la région provient du trafic pour la mesure. Le service est activé dès que vous créez un déploiement avec un serveur frontal public, et il continue de fonctionner tant que vous ne supprimez pas le serveur frontal. 

Un paquet correspondant au serveur frontal et à la règle de votre déploiement est généré régulièrement. Il traverse la région depuis la source vers l’hôte où une machine virtuelle dans le pool de serveur principal se trouve. L’infrastructure de l’équilibreur de charge effectue les mêmes opérations d’équilibrage de charge et de conversion que pour tout autre trafic. Cette sonde est intrabande sur votre point de terminaison équilibré en charge. Lorsque la sonde arrive sur l’hôte de calcul sur lequel se trouve une machine virtuelle intègre du pool principal, l’hôte de calcul génère une réponse au service de détection. Votre machine virtuelle ne voit pas ce trafic.

La disponibilité d’adresse IP virtuelle échoue pour les raisons suivantes :
- Il ne reste plus de machines virtuelles intègres du pool principal dans votre déploiement. 
- Une panne s’est produite au niveau de l’infrastructure.

À des fins de diagnostics, vous pouvez utiliser la [métrique de disponibilité du chemin d’accès aux données avec l’état de la sonde d’intégrité](#vipavailabilityandhealthprobes).

Utilisez une agrégation **Moyenne** pour la plupart des scénarios.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>Les instances de serveur principal pour mon adresse IP virtuelle répondent-elles aux sondes ?

La métrique d’état de la sonde d’intégrité indique l’intégrité de votre déploiement d’application tel que vous l’avez configuré lors de la configuration de la sonde d’intégrité de votre équilibreur de charge. L’équilibreur de charge utilise l’état de la sonde d’intégrité pour déterminer où envoyer les nouveaux flux. Les sondes d’intégrité proviennent d’une adresse d’infrastructure Azure et sont visibles dans le système d’exploitation invité de la machine virtuelle.

Pour obtenir l’état de la sonde d’intégrité pour vos ressources Load Balancer Standard :
1. Sélectionnez la métrique **État de la sonde d’intégrité** avec le type d’agrégation **Moy**. 
2. Appliquez un filtre sur l’adresse IP frontale ou le port requis (ou les deux).

Les sondes d’intégrité échouent pour les raisons suivantes :
- Vous configurez une sonde d’intégrité pour un port qui n’écoute pas ou ne répond pas ou qui utilise le mauvais protocole. Si votre service utilise des règles de retour direct du serveur (DSR, ou adresse IP flottante), assurez-vous que le service écoute sur l’adresse IP de la configuration IP de la carte d’interface réseau, et pas seulement sur le bouclage configuré avec l’adresse IP de serveur frontal.
- Votre sonde n’est pas autorisée par le groupe de sécurité réseau, le pare-feu du système d’exploitation invité de la machine virtuelle ou les filtres de la couche Application.

Utilisez une agrégation **Moyenne** pour la plupart des scénarios.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>Comment faire pour vérifier les statistiques de ma connexion sortante ? 

La métrique des connexions SNAT indique le volume de connexions ayant réussi et ayant échoué pour les [flux sortants](https://aka.ms/lboutbound).

Un volume de connexions ayant échoué supérieur à zéro indique un épuisement du port SNAT. Vous devez mener un examen précis pour déterminer les causes de ces échecs. L’épuisement du port SNAT se traduit par un problème d’établissement de [flux sortant](https://aka.ms/lboutbound). Consultez l’article sur les connexions sortantes pour comprendre les scénarios, les mécanismes au travail et pour apprendre à atténuer et éviter l’épuisement du port SNAT. 

Pour obtenir des statistiques de connexion SNAT :
1. Sélectionnez le type de métrique **Connexions SNAT** et l’agrégation **Somme**. 
2. Regroupez par **État de la connexion** pour le nombre de connexions SNAT ayant réussi et échoué représenté par les différentes lignes. 

![Connexion SNAT](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*Figure : Nombre de connexions SNAT pour Load Balancer*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>Comment faire pour vérifier les tentatives de connexions entrantes/sortantes pour mon service ?

Une métrique de paquets SYN indique le volume de paquets TCP SYN qui ont été reçus ou qui ont été envoyés (pour les [flux sortants](https://aka.ms/lboutbound)) associés à un serveur frontal spécifique. Vous pouvez utiliser cette métrique pour comprendre les tentatives de connexions TCP vers votre service.

Utilisez une agrégation **Totale** pour la plupart des scénarios.

![Connexion SYN](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*Figure : Nombre de SYN pour Load Balancer*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>Comment faire pour vérifier la consommation de ma bande passante réseau ? 

La métrique de compteurs d’octets et de paquets indique le volume d’octets et de paquets envoyés ou reçus par votre service par serveur frontal.

Utilisez une agrégation **Totale** pour la plupart des scénarios.

Pour obtenir les statistiques de nombre d’octets ou de paquets :
1. Sélectionnez le type de métrique **Bytes Count** (Nombre d’octets) et/ou **Nombre de paquets** avec l’agrégation **Moy**. 
2. Effectuez l'une des opérations suivantes :
   * Appliquez un filtre sur une adresse IP frontale, un port frontal, une adresse IP de serveur principal ou un port de serveur principal spécifique.
   * Récupérez des statistiques globales pour votre ressource d’équilibreur de charge sans aucun filtrage.

![Nombre d’octets](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*Figure : Nombre d’octets pour Load Balancer*

#### <a name = "vipavailabilityandhealthprobes"></a>Comment faire pour diagnostiquer mon déploiement d’équilibreur de charge ?

La combinaison des métriques de disponibilité d’adresse IP virtuelle et de sondes d’intégrité sur un même graphique peut vous permettre de déterminer où rechercher le problème et de le résoudre. Vous pouvez vous assurer qu’Azure fonctionne correctement et utiliser ces informations pour déterminer si la configuration ou l’application est la cause racine.

Vous pouvez utiliser des métriques de sonde d’intégrité pour comprendre comment Azure affiche l’intégrité de votre déploiement en fonction de la configuration que vous avez fournie. Examiner les sondes d’intégrité est toujours une première étape très utile dans l’analyse ou la détermination d’une cause.

Vous pouvez aller plus loin et utiliser des métriques de disponibilité d’adresse IP virtuelle pour savoir comment Azure affiche l’intégrité du plan de données sous-jacent responsable de votre déploiement. Lorsque vous combinez les deux métriques, vous pouvez déterminer où l’erreur peut se situer comme illustré dans cet exemple :

![Combinaison des métriques de disponibilité du chemin d’accès aux données et de l’état de la sonde d’intégrité](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*Figure : Combinaison des métriques de disponibilité du chemin d’accès aux données et de l’état de la sonde d’intégrité*

Ce graphique affiche les informations suivantes :
- L’infrastructure qui héberge vos machines virtuelles n’était pas disponible et à 0 pour cent au début du graphique. Plus tard, l’infrastructure était intègre, les machines virtuelles étaient accessibles et plusieurs machines virtuelles ont été placées dans le serveur principal. Ces informations sont représentées par le trait bleu pour la disponibilité du chemin d’accès aux données (disponibilité VIP), qui est de 100 %. 
- L’état de la sonde d’intégrité (disponibilité DIP), indiqué par le trait violet, est à 0 % au début du graphique. La zone dans le cercle vert indique où l’état de la sonde d’intégrité (disponibilité DIP) est devenu intègre, et à quel point le déploiement du client a été en mesure d’accepter de nouveaux flux.

Le graphique permet aux clients de dépanner eux-mêmes le déploiement sans devoir deviner ou demander de l’aide sur d’autres problèmes. Le service n’était pas disponible, car les sondes d’intégrité ont échoué en raison d’une configuration incorrecte ou de l’échec d’une application.

## <a name = "ResourceHealth"></a>État d’intégrité des ressources

L’état d’intégrité des ressources de niveau Standard de Load Balancer est indiqué dans la page **Intégrité des ressources** existante sous **Monitor > État du service**.

Pour afficher l’intégrité de vos ressources Load Balancer Standard public :
1. Sélectionnez **Monitor** > **État du service**.

   ![Port Monitor](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *Figure : Lien vers État du service sur Azure Monitor*

2. Sélectionnez **Intégrité des ressources** et vérifiez que **l’ID d’abonnement** et le **Type de ressource = Load Balancer** sont sélectionnés.

   ![État d’intégrité des ressources](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *Figure : Sélectionner une ressource pour la vue d’intégrité*

3. Dans la liste, cliquez sur la ressource Load Balancer pour afficher son état d’intégrité historique.

    ![État d’intégrité de Load Balancer](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *Figure : Vue d’intégrité de la ressource Load Balancer*
 
Le tableau suivant répertorie les divers états d’intégrité de ressource et leurs descriptions : 

| État d’intégrité des ressources | Description |
| --- | --- |
| Disponible | Votre ressource d’équilibreur de charge standard est intègre et disponible. |
| Non disponible | Votre ressource d’équilibreur de charge standard n’est pas intègre. Diagnostiquez l’intégrité en sélectionnant **Azure Monitor** > **Métriques**.<br>(L’état *Non disponible* peut également indiquer que la ressource n’est pas connectée avec votre équilibreur de charge standard.) |
| Unknown | L’état d’intégrité des ressources de votre ressource d’équilibreur de charge standard n’a pas encore été mis à jour.<br>(L’état *Inconnu* peut également indiquer que la ressource n’est pas connectée avec votre équilibreur de charge standard.)  |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[équilibreur de charge standard](load-balancer-standard-overview.md).
- En savoir plus sur la [connectivité sortante de votre équilibreur de charge](https://aka.ms/lboutbound).
- Découvrez [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).
- Découvrez l’[API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) et [comment récupérer des métriques par le biais de l’API REST](/rest/api/monitor/metrics/list).
