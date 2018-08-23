---
title: DNS dans Azure Stack | Microsoft Docs
description: Utilisation de DNS dans Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: acb8b262256031ae8615180e0f55c98cb56b538d
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41946605"
---
# <a name="using-dns-in-azure-stack"></a>Utilisation de DNS dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack prend en charge les fonctionnalités DNS (Domain Name System) suivantes :

* Résolution de nom d’hôte DNS
* Création et gestion d’enregistrements et de zones DNS à l’aide de l’API

## <a name="support-for-dns-hostname-resolution"></a>Prise en charge de la résolution de nom d’hôte DNS

Vous pouvez spécifier une étiquette de nom de domaine DNS pour les ressources de l’adresse IP publique. Azure Stack utilise *étiquette_nom_domaine.emplacement*.cloudapp.azurestack.external comme nom d’étiquette et le mappe à l’adresse IP publique dans les serveurs DNS gérés par Azure Stack.

Par exemple, si vous créez une ressource d’adresse IP publique avec **contoso** comme étiquette de nom de domaine à l’emplacement Azure Stack local, le [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) **contoso.local.cloudapp.azurestack.external** est résolu en l’adresse IP publique de la ressource. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure Stack.

Pour en savoir plus sur la résolution de noms, reportez-vous à l’article [Résolution DNS](https://docs.microsoft.com/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Chaque étiquette de nom de domaine que vous créez doit être unique dans son emplacement Azure Stack.

La capture de l’écran suivante montre la boîte de dialogue **Créer une adresse IP publique** pour créer une adresse IP publique à l’aide du portail.

![Création d’une adresse IP publique](media/azure-stack-whats-new-dns/image01.png)

**Exemple de scénario**

Vous pouvez avoir un équilibreur de charge traitant des requêtes à partir d’une application web. Derrière l’équilibreur de charge se trouve un site web s’exécutant sur une ou plusieurs machines virtuelles. Vous pouvez accéder au site web avec équilibrage de charge à l’aide d’un nom DNS, au lieu d’une adresse IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Créer et gérer des enregistrements et des zones DNS à l’aide de l’API

Vous pouvez créer et gérer des enregistrements et des zones DNS dans Azure Stack.

Azure Stack fournit un service DNS comme celui d’Azure, à l’aide d’API qui sont cohérentes avec les API DNS Azure.  En hébergeant vos domaines dans le système DNS Azure Stack, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, API et outils. Vous pouvez également utiliser la même facturation et le même support que vos autres services Azure.

L’infrastructure DNS Azure Stack est plus compacte que celle d’Azure. La taille et l’emplacement d’un déploiement Azure Stack affecte l’étendue, l’échelle et les performances DNS. Cela signifie également que les performances, la disponibilité, la distribution globale et la haute disponibilité peuvent varier d’un déploiement à l’autre.

## <a name="comparison-with-azure-dns"></a>Comparaison avec le système DNS Azure

Le système DNS dans Azure Stack est semblable au système DNS dans Azure, mais il existe des exceptions majeures qu’il est important d’assimiler.

* **Il ne prend pas en charge les enregistrements AAAA**

    Azure Stack ne prend pas en charge les enregistrements AAAA, car il ne prend pas en charge les adresses IPv6.  Il s’agit d’une différence essentielle entre les systèmes DNS Azure et Azure Stack.
* **Il n’est pas multilocataire**

    Le service DNS dans Azure Stack n’est pas multilocataire. Chaque client ne peut pas créer la même zone DNS. Seul le premier abonnement qui tente de créer la zone y parvient, et les requêtes suivantes échouent.  Il s’agit d’un problème connu et d’une différence essentielle entre le système DNS dans Azure et dans Azure Stack. Ce problème sera résolu dans une version ultérieure.
* **Balises, métadonnées et ETags**

    Il existe des différences mineures dans la façon dont Azure Stack gère les balises, les métadonnées, les ETags et les limites.

Pour en savoir plus sur le système Azure DNS, consultez [Enregistrements et zones DNS](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>Balises, métadonnées et ETags

**Balises**

Le système DNS Azure Stack prend en charge l’utilisation de balises Azure Resource Manager sur des ressources de zone DNS. Il ne prend pas en charge les balises sur les jeux d’enregistrements DNS, bien que l’alternative « métadonnées » soit prise en charge sur les jeux d’enregistrements DNS comme expliqué ci-dessous.

**Métadonnées**

À la place de balises de jeu d’enregistrements, le système DNS Azure Stack prend en charge l’annotation des jeux d’enregistrements à l’aide de « métadonnées ». Comme des balises, les métadonnées permettent d’associer des paires nom-valeur à chaque jeu d’enregistrements. Cela peut être utile, par exemple pour indiquer l’objectif de chaque jeu d’enregistrements. Contrairement aux balises, vous ne pouvez pas utiliser les métadonnées pour produire une vue filtrée de votre facture Azure. Et vous ne pouvez pas non plus en spécifier dans une stratégie Azure Resource Manager.

**ETags**

Supposons que deux personnes ou deux processus tentent de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il qu’il a remplacé les modifications créées par quelqu’un d’autre ?

Le système DNS Azure Stack utilise des ETags pour gérer les modifications simultanées de la même ressource en toute sécurité. Les ETags sont différents des « Balises » Azure Resource Manager. Chaque ressource DNS (zone ou jeu d’enregistrements) est associée à un Etag. Chaque fois qu’une ressource est récupérée, son Etag l’est également. Lors de la mise à jour d’une ressource, vous pouvez choisir de retransmettre l’ETag pour que le système DNS Azure Stack vérifie que l’ETag qui se trouve sur le serveur correspond. Étant donné que chaque mise à jour d’une ressource entraîne la régénération de l’Etag, l’absence de concordance entre les Etags indique qu’une modification simultanée a eu lieu. Les Etags sont également utilisés lorsque vous créez une ressource pour vous assurer que la ressource n’existe pas déjà.

Par défaut, PowerShell dans le système DNS Azure Stack utilise des ETags pour bloquer les modifications simultanées apportées à des zones et des jeux d’enregistrements. Le commutateur facultatif *-Overwrite* peut être utilisé pour supprimer les vérifications d’Etags, auquel cas toutes les modifications simultanées qui se sont produites sont remplacées.

Au niveau de l’API REST du système DNS Azure Stack, les ETags sont spécifiés à l’aide d’en-têtes HTTP. Leur comportement est indiqué dans le tableau suivant :

| En-tête | Comportement|
|--------|---------|
| Aucun   | PUT réussit toujours (aucune vérification Etag)|
| If-match| PUT ne réussit que si la ressource existe et que l’Etag correspond|
| If-match *| PUT réussit seulement si la ressource existe|
| If-none-match *| PUT réussit seulement si la ressource n’existe pas|

### <a name="limits"></a>limites

Les limites par défaut suivantes s’appliquent lors de l’utilisation du système DNS Azure Stack :

| Ressource| Limite par défaut|
|---------|--------------|
| Zones par abonnement| 100|
| Jeux d’enregistrements par zone| 5 000|
| Enregistrements par jeu d’enregistrements| 20|

## <a name="next-steps"></a>Étapes suivantes

[Introduction aux noms de domaine internationaux (IDN) pour Azure Stack](azure-stack-understanding-dns.md)
