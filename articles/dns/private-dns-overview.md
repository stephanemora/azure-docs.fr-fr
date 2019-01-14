---
title: Utilisation d’Azure DNS pour les domaines privés
description: Vue d’ensemble du service d’hébergement DNS privé sur Microsoft Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 012/5/2018
ms.author: victorh
ms.openlocfilehash: 4d817e71cffd782bdcfdfb91492dbd5d08fb8479
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967093"
---
# <a name="use-azure-dns-for-private-domains"></a>Utilisation d’Azure DNS pour les domaines privés

Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de service en une adresse IP. Azure DNS est un service d’hébergement pour les domaines DNS qui offre une résolution de noms à l’aide de l’infrastructure Microsoft Azure. En plus de la prise en charge des domaines DNS accessibles sur Internet, Azure DNS prend désormais également en charge les domaines DNS privés en tant que fonctionnalité en préversion.

Azure DNS fournit un service DNS fiable et sécurisé pour gérer et résoudre les noms de domaine dans un réseau virtuel sans avoir à ajouter de solution DNS personnalisée. Grâce aux zones DNS privées, vous pouvez utiliser vos propres noms de domaine personnalisés au lieu des noms fournis par Azure actuellement disponibles. L’utilisation de noms de domaine personnalisés vous aide à adapter votre architecture de réseau virtuel en fonction des besoins de votre organisation. Elle offre une résolution de noms pour les machines virtuelles au sein d’un réseau virtuel et entre plusieurs réseaux virtuels. De plus, vous pouvez configurer des noms de zones avec une vue divisée qui permet à des zones DNS publique et privée de partager le même nom.

Si vous spécifiez un réseau virtuel d’inscription, les enregistrements DNS pour les machines virtuelles de ce réseau virtuel qui sont inscrits dans la zone privée ne seront pas visibles ou récupérables à partir des API Azure Powershell et Azure CLI, mais les enregistrements de machine virtuelle sont tout de même enregistrés et résolus avec succès.

![Vue d’ensemble de DNS](./media/private-dns-overview/scenario.png)

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="benefits"></a>Avantages

Azure DNS offre les avantages suivants :

* **Évite d’avoir à utiliser des solutions DNS personnalisées**. Auparavant, un grand nombre de clients devaient créer des solutions DNS personnalisées pour gérer les zones DNS dans leur réseau virtuel. Vous pouvez désormais gérer les zones DNS à l’aide de l’infrastructure native d’Azure, ce qui supprime la lourde tâche de créer et gérer des solutions DNS personnalisées.

* **Prise en charge de tous les types d’enregistrements DNS courants**. Azure DNS prend en charge les enregistrements A, AAAA, CNAME, MX, NS, PTR, SOA, SRV et TXT.

* **Gestion automatique des enregistrements de noms d’hôte**. En plus d’héberger vos enregistrements DNS personnalisés, Azure gère automatiquement les enregistrements de noms d’hôte pour les machines virtuelles dans les réseaux virtuels spécifiés. Dans ce scénario, vous pouvez optimiser les noms de domaine que vous utilisez sans avoir à créer de solutions DNS personnalisées ni modifier les applications.

* **Résolution de noms d’hôte entre réseaux virtuels**. Contrairement aux noms d’hôte fournis par Azure, les zones DNS privé peuvent être partagées entre des réseaux virtuels. Cette fonctionnalité simplifie les scénarios de détection de services et réseaux croisés, tels que l’homologation de réseaux virtuels.

* **Outils familiers et expérience utilisateur**. Pour réduire la courbe d’apprentissage, cette nouvelle offre utilise des outils Azure DNS bien établis (PowerShell, modèles Azure Resource Manager et API REST).

* **Prise en charge DNS avec découpage d’horizon**. Azure DNS vous permet de créer des zones portant le même nom capables de résoudre des résultats différents au sein d’un réseau virtuel et à partir de l’Internet public. Un scénario classique de DNS avec découpage d’horizon consiste à fournir une version dédiée d’un service pour une utilisation au sein du réseau virtuel.

* **Disponible dans toutes les régions Azure**. Les zones privées Azure DNS sont disponibles dans toutes les régions Azure dans le cloud public Azure.

## <a name="capabilities"></a>Fonctionnalités

Azure DNS offre les fonctionnalités suivantes :

* **Inscription automatique de machines virtuelles à partir d’un seul réseau virtuel lié à une zone privée en tant que réseau virtuel d’inscription**. Les machines virtuelles sont inscrites (ajoutées) à la zone privée en tant qu’enregistrements A pointant vers leur adresse IP privée. Lorsqu’une machine virtuelle sur un réseau virtuel d’inscription est supprimée, Azure supprime aussi automatiquement l’enregistrement DNS correspondant de la zone privée liée. 

  Par défaut, les réseaux virtuels d’inscription agissent également en tant que réseaux virtuels de résolution dans la mesure où la résolution DNS dans la zone fonctionne à partir des machines virtuelles dans le réseau virtuel d’inscription.

  > [!NOTE]
  > Si vous spécifiez un réseau virtuel d’inscription, les enregistrements DNS pour les machines virtuelles de ce réseau virtuel qui sont inscrits dans la zone privée ne seront pas visibles ou récupérables à partir des API Azure Powershell et Azure CLI. Les enregistrements de machine virtuelle sont tout de même enregistrés et résolus avec succès.

* **Prise en charge de la résolution DNS sur plusieurs réseaux virtuels qui sont liés à la zone privée en tant que réseaux virtuels de résolution**. Pour la résolution DNS entre réseaux virtuels, il n’existe aucune dépendance explicite selon laquelle les réseaux virtuels doivent être appairés les uns avec les autres. Toutefois, les clients peuvent souhaiter appairer des réseaux virtuels pour d’autres raisons (par exemple, le trafic HTTP).

* **Prise en charge de la recherche DNS inversée dans l’étendue du réseau virtuel**. La recherche DNS inversée d’une adresse IP privée dans le réseau virtuel assigné à une zone privée retourne le nom de domaine complet qui inclut le nom de l’enregistrement/hôte, ainsi que le nom de la zone comme suffixe.

## <a name="limitations"></a>Limites

Azure DNS est soumis aux restrictions suivantes :

* Un seul réseau virtuel d’inscription est autorisé par zone privée.
* Jusqu’à 10 réseaux virtuels de résolution sont autorisés par zone privée.
* Un réseau virtuel donné ne peut être lié qu’à une seule zone privée en tant que réseau virtuel d’inscription.
* Un réseau virtuel donné peut être lié à un maximum de 10 zones privées en tant que réseau virtuel de résolution.
* Si vous spécifiez un réseau virtuel d’inscription, les enregistrements DNS pour les machines virtuelles de ce réseau virtuel qui sont inscrits dans la zone privée ne seront pas visibles ou récupérables à partir des API Azure Powershell et Azure CLI. Les enregistrements de machine virtuelle sont tout de même enregistrés et résolus avec succès.
* Le DNS inversé fonctionne uniquement pour l’espace IP privé sur le réseau virtuel d’inscription.
* Le DNS inversé pour une adresse IP privée qui n’est pas inscrite dans la zone privée (par exemple l’adresse IP privée d’une machine virtuelle sur un réseau virtuel qui est lié en tant que réseau virtuel de résolution à une zone privée) retourne *internal.cloudapp.net* comme suffixe DNS. Ce suffixe ne peut cependant pas être résolu.
* Le réseau virtuel doit être vide (autrement dit, il ne doit y avoir aucun enregistrement de machine virtuelle) lors de sa première association à une zone privée en tant que réseau virtuel d’inscription ou de résolution. Toutefois, le réseau virtuel peut ensuite être non vide pour les liaisons ultérieures à d’autres zones privées en tant que réseau virtuel d’inscription ou de résolution.
* À l’heure actuelle, le transfert conditionnel n’est pas pris en charge (par exemple, pour l’activation de la résolution entre les réseaux Azure et locaux). Pour plus d’informations sur la manière d’implémenter ce scénario par le biais d’autres mécanismes, consultez [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Consultez également la [FAQ](./dns-faq.md#private-dns) pour étudier certaines des questions et réponses courantes relatives aux zones privées dans Azure DNS, notamment le comportement de la résolution et de l’inscription DNS spécifique auquel vous pouvez vous attendre pour certains types d’opérations.  

## <a name="pricing"></a>Tarifs

La fonction de zones DNS privées est gratuite dans la préversion publique. Au stade de disponibilité générale, cette fonctionnalité utilisera un modèle tarifaire basé sur l’utilisation, similaire à l’offre Azure DNS actuelle. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment créer une zone privée dans Azure DNS à l’aide [d’Azure PowerShell](./private-dns-getstarted-powershell.md) ou [d’Azure CLI](./private-dns-getstarted-cli.md).

Passez en revue certains [scénarios de zones privées](./private-dns-scenarios.md) courants qui peuvent être réalisés avec des zones privées dans Azure DNS.

Consultez la [FAQ](./dns-faq.md#private-dns) pour étudier certaines des questions et réponses courantes relatives aux zones privées dans Azure DNS, notamment le comportement spécifique auquel vous pouvez vous attendre pour certains types d’opérations. 

Pour plus d’informations sur les zones et enregistrements DNS, consultez [Vue d’ensemble des enregistrements et zones DNS](dns-zones-records.md).

Découvrez certaines des autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure.
