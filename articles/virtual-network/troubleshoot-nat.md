---
title: Résoudre les problèmes de connectivité NAT du réseau virtuel Azure
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Résoudre les problèmes liés au service NAT du réseau virtuel.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: c629b3425cd095a6ac9d305b5cd6de58ed9d572a
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674326"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Résoudre les problèmes de connectivité NAT du réseau virtuel Azure

Cet article permet aux administrateurs de diagnostiquer et de résoudre les problèmes de connectivité lors de l’utilisation du service NAT du réseau virtuel.

>[!NOTE] 
>Le service NAT de Réseau virtuel est disponible en préversion publique pour l’instant. Actuellement, il n’est disponible que dans un ensemble limité de [régions](nat-overview.md#region-availability). Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problèmes

* [Épuisement des ressources SNAT](#snat-exhaustion)
* [Échec de la commande ping ICMP](#icmp-ping-is-failing)
* [Échecs de connectivité](#connectivity-failures)
* [Coexistence IPv6](#ipv6-coexistence)

Pour résoudre ces problèmes, effectuez les étapes de la section suivante.

## <a name="resolution"></a>Résolution

### <a name="snat-exhaustion"></a>Épuisement des ressources SNAT

Une seule [ressource de passerelle NAT](nat-gateway-resource.md) prend en charge entre 64 000 et 1 million de flux simultanés.  Chaque adresse IP fournit 64 000 ports SNAT à l’inventaire disponible. Vous pouvez utiliser jusqu’à 16 adresses IP par ressource de passerelle NAT.  Le mécanisme SNAT est décrit [ici](nat-gateway-resource.md#source-network-address-translation) de manière plus détaillée.

Souvent, la cause racine de l’épuisement des ressources SNAT est un anti-modèle du mode d’établissement et de gestion de la connectivité sortante.  Lisez attentivement cette section.

#### <a name="steps"></a>Étapes

1. Examinez comment votre application crée une connectivité sortante (par exemple, la révision de code ou la capture de paquets). 
2. Déterminez si cette activité est un comportement attendu ou si l’application ne fonctionne pas correctement.  Utilisez des [métriques](nat-metrics.md) dans Azure Monitor pour justifier vos découvertes. Utilisez la catégorie « Échec » pour la métrique Connexions SNAT.
3. Évaluez si les modèles appropriés sont suivis.
4. Évaluez si l’épuisement des ports SNAT doit être atténué avec des adresses IP supplémentaires affectées à la ressource de passerelle NAT.

#### <a name="design-patterns"></a>Modèles de conception

Tirez toujours parti de la réutilisation des connexions et du regroupement de connexions chaque fois que c’est possible.  Ces modèles offrent une solution radicale pour éviter les problèmes d’épuisement des ressources et assurent un comportement prévisible, fiable et scalable. Des primitives pour ces modèles sont disponibles dans beaucoup de frameworks et de bibliothèques de développement.

_**Solution :**_ Utiliser les modèles appropriés

- Envisagez d’utiliser les [modèles d’interrogation asynchrone](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) pour les opérations de longue durée afin de libérer des ressources de connexion pour d’autres opérations.
- Les flux de longue durée (par exemple, les connexions TCP réutilisées) doivent utiliser des conservations de connexion TCP active ou des conservations de connexion active de la couche Application pour éviter l’expiration des systèmes intermédiaires.
- Les [modèles de nouvelle tentative](https://docs.microsoft.com/azure/architecture/patterns/retry) sans perte de données doivent être utilisés pour éviter les nouvelles tentatives agressives/rafales en cas de défaillance temporaire ou de reprise d’activité après défaillance.
La création d’une connexion TCP pour chaque opération HTTP (également appelée « connexions atomiques ») est un anti-modèle.  Les connexions atomiques empêchent votre application d’être correctement mise à l’échelle et gaspillent des ressources.  Canalisez toujours plusieurs opérations dans la même connexion.  Votre application tire parti de la vitesse des transactions et des coûts des ressources.  Quand votre application utilise le chiffrement de la couche de transport (par exemple, TLS), un coût élevé est associé au traitement des nouvelles connexions.  Passez en revue les [Modèles de conception Azure Cloud](https://docs.microsoft.com/azure/architecture/patterns/) pour obtenir des modèles de bonnes pratiques supplémentaires.

#### <a name="possible-mitigations"></a>Atténuations possibles

_**Solution :**_ Mettez à l’échelle la connectivité sortante comme suit :

| Scénario | Preuve |Limitation des risques |
|---|---|---|
| Vous rencontrez des problèmes de contention pour les ports SNAT et l’épuisement des ports SNAT au cours des périodes d’utilisation intensive. | La catégorie « Échec » pour la [métrique](nat-metrics.md) Connexions SNAT dans Azure Monitor présente des défaillances temporaires ou persistantes dans le temps et un volume de connexions élevé.  | Déterminez si vous pouvez ajouter des ressources d’adresses IP publiques ou des ressources de préfixes d’adresses IP publiques supplémentaires. Cet ajout permet jusqu’à 16 adresses IP au total pour votre passerelle NAT. Cet ajout fournit davantage d’inventaire pour les ports SNAT disponibles (64 000 par adresse IP) et vous permet d’affiner la misse à l’échelle de votre scénario.|
| Vous avez déjà donné 16 adresses IP et êtes toujours confronté à un épuisement des ports SNAT. | Échec de la tentative d’ajout d’une adresse IP. Le nombre total d’adresses IP provenant de ressources d’adresses IP publiques ou de ressources de préfixes d’adresses IP publiques est supérieur à 16. | Distribuez votre environnement d’application sur plusieurs sous-réseaux et fournissez une ressource de passerelle NAT pour chaque sous-réseau.  Réévaluez le ou les modèles de conception à optimiser en vous basant sur les [instructions](#design-patterns) précédentes. |

>[!NOTE]
>Il est important de comprendre la raison de l’épuisement des ressources SNAT. Veillez à utiliser les modèles appropriés pour des scénarios évolutifs et fiables.  L’ajout de ports SNAT supplémentaires à un scénario sans comprendre la cause de la demande doit être envisagé en dernier recours. Si vous ne comprenez pas pourquoi votre scénario applique une charge sur l’inventaire des ports SNAT, le fait d’ajouter des ports SNAT supplémentaires à l’inventaire en ajoutant plus d’adresses IP ne fera que retarder le même échec dû à l’épuisement quand votre application est mise à l’échelle.  Vous masquez peut-être d’autres manques d’efficacité et anti-modèles.

### <a name="icmp-ping-is-failing"></a>Échec de la commande ping ICMP

Le service [NAT du réseau virtuel](nat-overview.md) prend en charge les protocoles TCP et UDP IPv4. ICMP n’est pas pris en charge et est censé échouer.  

_**Solution :**_ Utilisez plutôt des tests de connexion TCP (par exemple « ping TCP ») et des tests de la couche Application spécifiques à UDP pour valider la connectivité de bout en bout.

Le tableau suivant peut être utilisé comme point de départ pour savoir quels outils utiliser pour démarrer des tests.

| Système d’exploitation | Test de connexion TCP générique | Test de la couche Application TCP | UDP |
|---|---|---|---|
| Linux | nc (test de connexion générique) | curl (test de la couche Application TCP) | spécifique à l’application |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) de PowerShell | spécifique à l’application |

### <a name="connectivity-failures"></a>Échecs de connectivité

Les échecs de connectivité avec le service [NAT de réseau virtuel](nat-overview.md) peuvent être dus à différents problèmes :

* [Épuisement des ressources SNAT](#snat-exhaustion) de la passerelle NAT (temporaire ou persistant)
* Défaillances temporaires au niveau de l’infrastructure Azure 
* Défaillances temporaires au niveau du chemin entre Azure et la destination Internet publique 
* Défaillances temporaires ou persistantes au niveau de la destination Internet publique

Utilisez des outils tels que ceux mentionnés ci-après pour la validation de la connectivité. [Le ping ICMP n’est pas prise en charge](#icmp-ping-is-failing).

| Système d’exploitation | Test de connexion TCP générique | Test de la couche Application TCP | UDP |
|---|---|---|---|
| Linux | nc (test de connexion générique) | curl (test de la couche Application TCP) | spécifique à l’application |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) de PowerShell | spécifique à l’application |

#### <a name="snat-exhaustion"></a>Épuisement des ressources SNAT

Consultez la section de cet article relative à l’[épuisement des ressources SNAT](#snat-exhaustion).

#### <a name="azure-infrastructure"></a>Infrastructure Azure

Même si Azure supervise et exploite son infrastructure avec une grande rigueur, des défaillances temporaires peuvent se produire. En effet, rien ne garantit des transmissions sans perte.  Utilisez des modèles de conception qui autorisent les retransmissions SYN pour les applications TCP. Utilisez des délais de connexion suffisamment longs pour que la retransmission SYN TCP puisse réduire les impacts temporaires causés par un paquet SYN perdu.

_**Solution :**_

* Vérifiez l’[épuisement des ressources SNAT](#snat-exhaustion).
* Le paramètre de configuration d’une pile TCP qui contrôle le comportement de la retransmission SYN est le [délai d’attente de retransmission](https://tools.ietf.org/html/rfc793) (RTO, Retransmission Time-Out). La valeur RTO peut être ajustée. En général, elle est d’au moins 1 seconde par défaut avec une temporisation exponentielle.  Si le délai de connexion de votre application est trop court (par exemple, 1 seconde), vous pouvez constater des expirations de connexion sporadiques.  Augmentez le délai de connexion de l’application.
* Si vous observez des délais plus longs ou inattendus avec les comportements de l’application par défaut, ouvrez un cas de support pour obtenir une assistance supplémentaire en vue de la résolution du problème.

Nous vous déconseillons de réduire le délai de connexion TCP de façon artificielle ou d’ajuster le paramètre RTO.

#### <a name="public-internet-transit"></a>Transit Internet public

Plus le chemin jusqu’à la destination est long et plus il existe de systèmes intermédiaires, plus la probabilité de défaillances temporaires est élevée. Nous savons que la fréquence des défaillances temporaires sur l’[infrastructure Azure](#azure-infrastructure) peut augmenter. 

Suivez les instructions fournies dans la section précédente [Infrastructure Azure](#azure-infrastructure).

#### <a name="internet-endpoint"></a>Point de terminaison Internet

Les sections précédentes s’appliquent en plus des considérations relatives au point de terminaison Internet avec lequel votre communication est établie. D’autres facteurs peuvent impacter la réussite de la connectivité :

* Gestion du trafic côté destination, y compris
- Limitation du taux d’API imposée côté destination
- Atténuations DDoS volumétriques ou régulation de flux de la couche de transport
* Pare-feu ou autres composants côté destination 

En général, des captures de paquets au niveau de la source et de la destination (le cas échéant) sont nécessaires pour déterminer ce qui se produit.

_**Solution :**_

* Vérifiez l’[épuisement des ressources SNAT](#snat-exhaustion). 
* Vérifiez la connectivité à un point de terminaison dans la même région et dans une région différente à des fins de comparaison.  
* Si vous effectuez des tests de taux de transactions ou de volume élevé, vérifiez si la réduction du taux réduit l’occurrence des défaillances.
* Si la modification du taux a un impact sur le taux de défaillances, vérifiez si les limites de taux d’API ont été atteintes et vérifiez l’impact éventuel d’autres contraintes côté destination.
* Si votre recherche n’aboutit pas, ouvrez un cas de support pour obtenir une assistance supplémentaire en vue de la résolution du problème.

#### <a name="tcp-resets-received"></a>Réinitialisations TCP reçues

Si vous constatez que des réinitialisations TCP (paquets RST TCP) sont reçues sur la machine virtuelle source, elles peuvent être générées par la passerelle NAT côté privé pour les flux qui ne sont pas reconnus comme étant en cours.  Ceci peut être dû, par exemple, à l’expiration du délai d’inactivité de la connexion TCP.  Vous pouvez définir le délai d’inactivité entre 4 minutes et 120 minutes.

Les réinitialisations TCP ne sont pas générées du côté public des ressources de passerelle NAT. Si vous recevez des réinitialisations TCP côté destination, elles sont générées par la pile de la machine virtuelle source et non par la ressource de passerelle NAT.

_**Solution :**_

* Passez en revue les recommandations sur les [modèles de conception](#design-patterns).  
* Si nécessaire, ouvrez un cas de support pour obtenir une assistance supplémentaire en vue de la résolution du problème.

### <a name="ipv6-coexistence"></a>Coexistence IPv6

Le service [NAT de réseau virtuel](nat-overview.md) prend en charge les protocoles TCP et UDP IPv4. Le déploiement sur un [sous-réseau avec préfixe IPv6 n’est pas pris en charge](nat-overview.md#limitations).

_**Solution :**_ Déployer une passerelle NAT sur un sous-réseau sans préfixe IPv6.

Si vous êtes intéressé par d’autres fonctionnalités, vous pouvez l’indiquer sur le site [UserVoice](https://aka.ms/natuservoice) (service NAT de réseau virtuel).

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en davantage sur le service [Nat de Réseau virtuel](nat-overview.md).
* Découvrir la [ressource de passerelle NAT](nat-gateway-resource.md)
* Découvrez les [métriques et les alertes pour les ressources de passerelle NAT](nat-metrics.md).
* [Utilisez UserVoice pour nous faire part des prochains développements dont vous aimeriez bénéficier concernant le service NAT de réseau virtuel](https://aka.ms/natuservoice).

