---
title: Mise à l’échelle et la passerelle d’Application redondant dans Azure
description: Cet article présente le Standard_v2 d’Application Azure et WAF_v2 référence (SKU), qui inclut les fonctionnalités de mise à l’échelle et redondant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 86693e829ab08c3cb7befc6f0047472e8faa61fa
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947207"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>Mise à l’échelle et la passerelle d’Application redondant 

Passerelle d’application et de pare-feu d’applications Web (WAF) sont également disponibles sous un Standard_v2 et WAF_v2 référence (SKU) qui offre des améliorations des performances et ajoute la prise en charge des nouvelles fonctionnalités critiques telles que la mise à l’échelle, de redondance de zone et de prise en charge des adresses IP virtuelles statiques. Des fonctionnalités existantes sous Standard et de la référence SKU WAF continuent à être pris en charge dans la nouvelle référence SKU v2, avec quelques exceptions répertoriées dans la section de comparaison. 

La nouvelle référence (SKU) v2 inclut les améliorations suivantes :

- **Mise à l’échelle automatique** : les déploiements d’Application Gateway ou du WAF sous la référence SKU de la mise à l’échelle automatique peuvent augmenter ou diminuer en fonction de l’évolution des modèles de charge du trafic. La mise à l’échelle automatique vous évite aussi d’avoir à choisir une taille de déploiement ou un nombre d’instances au moment du provisionnement. Cette référence (SKU) offre l’élasticité true. Dans Standard_v2 et WAF_v2 référence (SKU), la passerelle d’Application peut fonctionner dans capacité fixe (à l’échelle automatique désactivée) et en mode de mise à l’échelle est activée. Le mode de capacité fixe est utile pour les scénarios avec des charges de travail cohérentes et prévisibles. Mode de mise à l’échelle est utile dans les applications qui consultez variance dans le trafic d’application.
- **Redondance de zone** : Une passerelle d’Application ou d’un déploiement de WAF peut s’étendre sur plusieurs Zones de disponibilité, évite d’avoir à provisionner des instances de passerelle d’Application distincts dans chaque zone avec un gestionnaire de trafic. Vous pouvez choisir une ou plusieurs zones où les instances d’Application Gateway sont déployées, assurant ainsi une résilience en cas d’échec de la zone. Le pool principal pour les applications peut être distribué de la même façon entre les différentes zones de disponibilité.
- **Adresse IP virtuelle statique** : Application gateway v2 référence (SKU) prend en charge l’adresse IP virtuelle statique tapez exclusivement. Cela garantit que l’adresse IP virtuelle associé avec la passerelle d’application ne change pas pendant le cycle de vie du déploiement, même après un redémarrage.
- **Réécriture de l’en-tête**: Application Gateway vous permet de vous ajouter, supprimer ou mettre à jour des en-têtes de demande et de réponse HTTP avec la référence (SKU) v2. Pour plus d’informations, consultez [en-têtes HTTP réécrire avec Application Gateway](rewrite-http-headers.md)
- **Key Vault Integration (version préliminaire)**: Application Gateway v2 prend en charge l’intégration avec Key Vault (en préversion publique) pour les certificats de serveur qui sont attachés aux écouteurs d’activation du HTTPS. Pour plus d’informations, consultez [une terminaison SSL avec certificats Key Vault](key-vault-certs.md).
- **Azure Kubernetes Service entrée contrôleur (version préliminaire)**: Le contrôleur d’entrée de passerelle d’Application v2 permet la passerelle d’Application Azure à utiliser en tant que l’entrée pour un Azure Kubernetes Service (AKS) appelé Cluster AKS. Pour plus d’informations, consultez le [page de documentation](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Amélioration des performances** : Le v2 référence (SKU) offre jusqu'à 5 SSL mieux X décharger des performances par rapport à la référence SKU Standard/WAF.
- **Temps de déploiement et de mise à jour** la référence (SKU) v2 fournit le temps de déploiement et de mise à jour par rapport à la référence (SKU) Standard/WAF. Cela inclut également des modifications de configuration de pare-feu d’applications Web.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Régions prises en charge

Le Standard_v2 et WAF_v2 référence (SKU) est disponible dans les régions suivantes : USA Centre Nord, USA Centre Sud, USA Ouest, USA Ouest 2, USA Est, USA Est 2, USA Centre, Europe Nord, Europe Ouest, Asie Sud-Est, France Centre, Royaume-Uni Ouest, Japon Est, Japon Ouest. Prise en charge pour d’autres régions est à venir.

## <a name="pricing"></a>Tarifs

Avec la référence (SKU) v2, le modèle de tarification est piloté par la consommation et n’est plus attaché au nombre d’instances ou tailles. La tarification de référence (SKU) v2 a deux composants :

- **Prix fixe** -il s’agit toutes les heures (ou entamée) prix pour approvisionner un Standard_v2 ou une passerelle de WAF_v2.
- **Prix unitaire de la capacité** -il s’agit des coûts basé sur la consommation qui sont facturé en plus du coût fixe. Frais d’unités de capacité est également calculé toutes les heures ou partielle toutes les heures. Il existe trois dimensions à une unité de capacité : unité, des connexions persistantes et le débit de calcul. Unité de calcul est une mesure de capacité de processeur consommée. Facteurs affectant l’unité de calcul sont des connexions TLS/s, calculs de réécriture d’URL et le traitement des règles WAF. Une connexion permanente est une mesure de connexions TCP établies pour la passerelle d’application dans un intervalle de facturation donné. Le débit est moyenne mégabits par seconde traitées par le système dans un intervalle de facturation donné.

Chaque unité de capacité comprend au maximum : 1 unité, ou des connexions persistantes 2500 ou 2.22 débit de Mbits/s de calcul.

Guide de l’unité de calcul :

- **Standard_v2** -chaque unité de calcul est capable d’environ 50 connexions par seconde avec un certificat TLS de la clé RSA 2048 bits.
- **WAF_v2** : chaque unité de calcul est capable d’environ 10 requêtes simultanées par seconde pour 70-30 % combinaison du trafic avec 70 % des demandes inférieure à 2 Ko GET/POST et restant supérieur. Performances de WAF n’est pas actuellement affectée par la taille de la réponse.

> [!NOTE]
> Chaque instance peut prend actuellement en charge environ 10 unités de capacité.
> Le nombre de demandes de qu'une unité de calcul peut gérer dépend de différents critères comme taille de clé de certificat TLS, algorithme d’échange de clés, réécritures d’en-tête et en cas de la taille de la demande entrante WAF. Nous vous recommandons de qu'effectuer des tests d’application pour déterminer le taux de demandes par unité de calcul. Des unités de calcul et de l’unité de capacité sera disponibles en tant qu’une mesure avant la facturation démarre.

**La tarification dans la région est des États-Unis**:

|              Nom de la référence (SKU)                             | Prix fixe ($/ h)  | Prix unitaire de la capacité ($/ CU-h)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Le [page de tarification](https://azure.microsoft.com/en-us/pricing/details/application-gateway/) sera mis à jour pour refléter les prix sur le 14 mai 2019. La facturation est programmée pour démarrer sur le 1 juin 2019.

**Exemple 1**

Un Standard_v2 de passerelle d’Application est configuré sans mise à l’échelle en mode de mise à l’échelle manuels avec une capacité fixe de cinq instances.

Prix fixe = 744(hours) * 0,20 $ = $148.8 <br>
Unités de capacité = unité de capacité (heures) 10 744 par instance * cinq instances * $0.008 par heure d’unité de capacité = $297.6

Prix total = $148.8 + $297.6 = $446.4

**Exemple 2**

Un standard_v2 de passerelle d’Application est configurée pour un mois et pendant ce temps, il reçoit 25 nouvelles SSL connexions par seconde, moyenne 8.88 Mbits/s transfert de données. En supposant que les connexions ne durent, que votre prix serait :

Prix fixe = 744(hours) * 0,20 $ = $148.8

Prix unitaire de capacité = 744(hours) * Max (unité de calcul de 25/50 pour les connexions par seconde, unité de capacité 8.88/2.22 pour le débit) * $0.008 = 744 * 4 * 0.008 = $23.81

Prix total = $148. 23.81 8 + = $172.61

**Exemple 3**

Un WAF_v2 de passerelle d’Application est configurée pour un mois et pendant ce temps, il reçoit les 25 nouvelles SSL connexions par seconde, moyenne 8.88 Mbits/s transfert de données et est de 80 demandes par seconde. En supposant que les connexions sont de courtes durée de vie, et que le calcul d’unité de calcul pour l’application prend en charge de RPS 10 par unité de calcul, vos prix serait :

Prix fixe = 744(hours) * 0,36 $ = $267.84

Prix unitaire de capacité = 744(hours) * Max (calcul unité Max(25/50 for connections/sec, 80/10 WAF RPS) 8.88/2.22 unité de capacité de débit) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Prix total = $267.84 + $85.71 = $353.55

Le [page de tarification](https://azure.microsoft.com/en-us/pricing/details/application-gateway/) sera mis à jour pour refléter les prix sur le 14 mai 2019. La facturation est programmée pour démarrer sur le 1 juin 2019.

## <a name="scaling-application-gateway-and-waf-v2"></a>Mise à l’échelle de passerelle d’Application et WAF v2

Passerelle d’application et de pare-feu d’applications Web peuvent être configurés à l’échelle dans deux modes :

- **Mise à l’échelle** : la mise à l’échelle activée la passerelle d’Application et les références (SKU) de WAF v2 sera mise à l’échelle vers le haut ou vers le bas en fonction des besoins de trafic d’application. Ce mode offre une meilleure élasticité à votre application et vous évite de devoir deviner le nombre de taille ou d’instances de passerelle application. Ce mode permet également à réduire les coûts en vous évitant pour exécuter des passerelles à sa capacité maximale configurée pour la charge du trafic maximal prévu. Les clients doivent spécifier un nombre d’instances minimum et maximum si vous le souhaitez. Capacité minimale garantit que les Application Gateway et WAF v2 ne prennent pas inférieur au nombre minimal d’instances spécifié même en l’absence de trafic. Vous êtes facturé pour cette capacité minimale même en l’absence de tout le trafic. Vous pouvez également éventuellement spécifier le nombre maximal d’instances qui permet de s’assurer que la passerelle d’Application n’évolue pas au-delà du nombre spécifié d’instances. Vous continuerez à être facturé pour la quantité de trafic traité par la passerelle. Le nombre d’instances peut varier de 0 à 125. Valeur par défaut pour le nombre maximal d’instances est 20 si non spécifié.
- **Manuel** -vous pouvez aussi choisir le mode manuel, où la passerelle ne sera pas mise à l’échelle. Dans ce mode, si plus de trafic est envoyé à quelle Application Gateway ou le WAF est capable de gérer, cela peut entraîner une perte de trafic. Nombre d’instances en spécifiant le mode manuel est obligatoire. Nombre d’instances peut varier de 1 à 125 instances.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparaison des fonctionnalités de la référence (SKU) v1 et v2 référence (SKU)

Le tableau suivant compare les fonctionnalités disponibles avec chaque référence (SKU).

|                                                   | référence (SKU) v1   | référence (SKU) v2   |
| ------------------------------------------------- | -------- | -------- |
| Mise à l’échelle automatique                                       |          | &#x2713; |
| Redondance de zone                                   |          | &#x2713; |
| Adresse IP virtuelle statique                                        |          | &#x2713; |
| Azure contrôleur d’entrée de Kubernetes Service (AKS) |          | &#x2713; |
| Intégration du coffre de clés Azure                       |          | &#x2713; |
| Réécrire les en-têtes HTTP (S)                           |          | &#x2713; |
| Routage basé sur des URL                                 | &#x2713; | &#x2713; |
| Hébergement de plusieurs sites                             | &#x2713; | &#x2713; |
| Redirection du trafic                               | &#x2713; | &#x2713; |
| Pare-feu d’applications web (WAF)                    | &#x2713; | &#x2713; |
| Terminaison SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Chiffrement SSL de bout en bout                         | &#x2713; | &#x2713; |
| Affinité de session                                  | &#x2713; | &#x2713; |
| Pages d’erreur personnalisées                                | &#x2713; | &#x2713; |
| Prise en charge de WebSocket                                 | &#x2713; | &#x2713; |
| Assistance HTTP/2                                    | &#x2713; | &#x2713; |
| Vidage des connexions                               | &#x2713; | &#x2713; |

> [!NOTE]
> La mise à l’échelle et la passerelle d’application redondant référence (SKU) prend désormais en charge [sondes d’intégrité par défaut](application-gateway-probe-overview.md#default-health-probe) pour surveiller l’intégrité de toutes les ressources dans son pool back-end et mettez en surbrillance ces membres principaux qui sont considérés comme automatiquement défectueux. La sonde de contrôle d’intégrité par défaut finiront par être automatiquement configurés pour ces serveurs principaux pour lesquels vous n’avez pas configuré de n’importe quelle configuration de sonde personnalisée. Pour plus d’informations, consultez [sondes d’intégrité dans application gateway](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Différences avec la référence (SKU) v1

|Différence|Détails|
|--|--|
|Certificat d’authentification|Non pris en charge.<br>Pour plus d’informations, consultez [Présentation du chiffrement SSL de bout en bout sur la passerelle Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinaison de Standard_v2 et Standard Application Gateway sur le même sous-réseau|Non pris en charge|
|Route définie par l’utilisateur sur le sous-réseau d’Application Gateway|Non pris en charge|
|Groupe de sécurité réseau pour plage de ports entrants| - 65 200 à 65 535 pour référence (SKU) Standard_v2<br>- 65 503 à 65 534 pour référence (SKU) Standard<br>Pour plus d’informations, visitez le [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Journaux d’activité de performances dans les diagnostics Azure|Non pris en charge.<br>Les métriques Azure doivent être utilisées.|
|Facturation|Facturation planifiée pour démarrer sur le 1 juin 2019.|
|Mode FIPS|Ils ne sont pas pris en charge.|
|Mode ILB uniquement|Non pris en charge actuellement. Les modes public et ILB sont pris en charge ensemble.|
|Intégration de Network Watcher|Non pris en charge.|

## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide : Trafic web direct avec Azure Application Gateway - portail Azure](quick-create-portal.md)
- [Créer une passerelle d’application redondante dans une zone, avec mise à l’échelle automatique et avec une adresse IP virtuelle réservée à l’aide d’Azure PowerShell](tutorial-autoscale-ps.md)
- Découvrez [Application Gateway](overview.md).
- Découvrez le [Pare-feu Azure](../firewall/overview.md).