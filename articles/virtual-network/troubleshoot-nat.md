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
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302982"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Résoudre les problèmes de connectivité NAT du réseau virtuel Azure

Cet article permet aux administrateurs de diagnostiquer et de résoudre les problèmes de connectivité lors de l’utilisation du service NAT du réseau virtuel.

>[!NOTE] 
>Le service NAT de Réseau virtuel est disponible en préversion publique pour l’instant. Actuellement, il n’est disponible que dans un ensemble limité de [régions](nat-overview.md#region-availability). Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problèmes

- [Épuisement des ressources SNAT](#snat-exhaustion).
- [Échec de la commande ping ICMP](#icmp-ping-is-failing).

Pour résoudre ces problèmes, effectuez les étapes de la section suivante.

## <a name="resolution"></a>Résolution

### <a name="snat-exhaustion"></a>Épuisement des ressources SNAT

Une seule [ressource de passerelle NAT](nat-gateway-resource.md) prend en charge entre 64 000 et 1 million de flux simultanés.  Chaque adresse IP fournit 64 000 ports SNAT à l’inventaire disponible. Vous pouvez utiliser jusqu’à 16 adresses IP par ressource de passerelle NAT.  Le mécanisme SNAT est décrit [ici](nat-gateway-resource.md#source-network-address-translation) de manière plus détaillée.

#### <a name="steps"></a>Étapes :

1. Examinez comment votre application crée une connectivité sortante (par exemple, la révision de code ou la capture de paquets). 
2. Déterminez si cette activité est un comportement attendu ou si l’application ne fonctionne pas correctement.  Utilisez des [métriques](nat-metrics.md) dans Azure Monitor pour justifier vos découvertes.
3. Évaluez si les modèles appropriés sont suivis.
4. Évaluez si l’épuisement des ports SNAT doit être atténué avec des adresses IP supplémentaires affectées à la ressource de passerelle NAT.

#### <a name="design-pattern"></a>Modèle de conception :

Tirez toujours parti de la réutilisation des connexions et du regroupement de connexions chaque fois que c’est possible.  Ce modèle évite totalement les problèmes d’épuisement des ressources et entraîne un comportement prévisible. Des primitives pour ces modèles sont disponibles dans beaucoup de frameworks et de bibliothèques de développement.
- Envisagez d’utiliser les [modèles d’interrogation asynchrone](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) pour les opérations de longue durée afin de libérer des ressources de connexion pour d’autres opérations.
- Les flux de longue durée (par exemple, les connexions TCP réutilisées) doivent utiliser des conservations de connexion TCP active ou des conservations de connexion active de la couche Application pour éviter l’expiration des systèmes intermédiaires.
- Les [modèles de nouvelle tentative](https://docs.microsoft.com/azure/architecture/patterns/retry) sans perte de données doivent être utilisés pour éviter les nouvelles tentatives agressives/rafales en cas de défaillance temporaire ou de reprise d’activité après défaillance.
La création d’une connexion TCP pour chaque opération HTTP (également appelée « connexions atomiques ») est un anti-modèle.  Les connexions atomiques empêchent votre application d’être correctement mise à l’échelle et gaspillent des ressources.  Canalisez toujours plusieurs opérations dans la même connexion.  Votre application tire parti de la vitesse des transactions et des coûts des ressources.  Quand votre application utilise le chiffrement de la couche de transport (par exemple, TLS), un coût élevé est associé au traitement des nouvelles connexions.  Passez en revue les [Modèles de conception Azure Cloud](https://docs.microsoft.com/azure/architecture/patterns/) pour obtenir des modèles de bonnes pratiques supplémentaires.

#### <a name="mitigations"></a>Corrections

Vous pouvez mettre à l’échelle la connectivité sortante comme suit :

| Scénario | Limitation des risques |
|---|---|
| Vous rencontrez des problèmes de contention pour les ports SNAT et l’épuisement des ports SNAT au cours des périodes d’utilisation intensive. | Déterminez si vous pouvez ajouter des ressources d’adresses IP publiques ou des ressources de préfixes d’adresses IP publiques supplémentaires. Cet ajout permet jusqu’à 16 adresses IP au total pour votre passerelle NAT. Cet ajout fournit davantage d’inventaire pour les ports SNAT disponibles (64 000 par adresse IP) et vous permet d’affiner la misse à l’échelle de votre scénario.|
| Vous avez déjà donné 16 adresses IP et êtes toujours confronté à un épuisement des ports SNAT. | Distribuez votre environnement d’application sur plusieurs sous-réseaux et fournissez une ressource de passerelle NAT pour chaque sous-réseau. |

>[!NOTE]
>Il est important de comprendre la raison de l’épuisement des ressources SNAT. Veillez à utiliser les modèles appropriés pour des scénarios évolutifs et fiables.  L’ajout de ports SNAT supplémentaires à un scénario sans comprendre la cause de la demande doit être envisagé en dernier recours. Si vous ne comprenez pas pourquoi votre scénario applique une charge sur l’inventaire des ports SNAT, le fait d’ajouter des ports SNAT supplémentaires à l’inventaire en ajoutant plus d’adresses IP ne fera que retarder le même échec dû à l’épuisement quand votre application est mise à l’échelle.  Vous masquez peut-être d’autres manques d’efficacité et anti-modèles.

### <a name="icmp-ping-is-failing"></a>Échec de la commande ping ICMP

Le service [NAT du réseau virtuel](nat-overview.md) prend en charge les protocoles TCP et UDP IPv4. ICMP n’est pas pris en charge et est censé échouer.  Utilisez plutôt des tests de connexion TCP (par exemple « ping TCP ») et des tests de la couche Application spécifiques à UDP pour valider la connectivité de bout en bout.

Le tableau suivant peut être utilisé comme point de départ pour savoir quels outils utiliser pour démarrer des tests.

| Système d’exploitation | Test de connexion TCP générique | Test de la couche Application TCP | UDP |
|---|---|---|---|
| Linux | nc (test de connexion générique) | curl (test de la couche Application TCP) | spécifique à l’application |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) de PowerShell | spécifique à l’application |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur le service [Nat de Réseau virtuel](nat-overview.md).
- Découvrir la [ressource de passerelle NAT](nat-gateway-resource.md)
- Découvrez les [métriques et les alertes pour les ressources de passerelle NAT](nat-metrics.md).
