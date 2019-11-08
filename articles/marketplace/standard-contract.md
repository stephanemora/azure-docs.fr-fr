---
title: Contrat Standard | Azure
description: Contrat Standard dans la Place de marché Azure et AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819490"
---
# <a name="standard-contract"></a>Contrat standard

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un modèle de contrat Standard afin de faciliter les transactions sur la place de marché. Plutôt que d’élaborer des conditions générales personnalisées, les éditeurs de la Place de marché Azure peuvent choisir de proposer leur logiciel dans le cadre du contrat Standard, que les clients ne doivent accepter qu’une seule fois. Le contrat Standard est disponible ici : [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

Les termes et conditions des offres sont définies sous l’onglet Place de marché lorsque vous créez une offre dans le Portail Cloud Partner. L’option de contrat Standard est activée en définissant le paramètre sur Oui.

![Activation de l’option de contrat Standard](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Si vous choisissez d’utiliser le contrat Standard, d’autres conditions générales sont encore nécessaires pour le [canal CSP](./cloud-solution-providers.md).

## <a name="standard-contract-amendments"></a>Contrat Standard | Modifications

Les modifications de contrat Standard permettent aux éditeurs de sélectionner le contrat Standard par souci de simplicité, et avec des conditions personnalisées pour leur produit ou entreprise.  Les clients sont invités à examiner les modifications apportées au contrat, puis à accepter le contrat Standard de Microsoft.

Deux types de modifications sont proposées aux éditeurs de la Place de marché Azure :

* Modifications universelles : Ces modifications sont appliquées de manière universelle au contrat Standard de tous les clients. Les modifications universelles s'affichent pour chaque client du produit dans le flux d’achat.

![Modifications universelles](media/marketplace-publishers-guide/universal-amendaments.png)

* Modifications personnalisées : La Place de marché Azure propose également des modifications personnalisées destinées aux locataires. Il s'agit de modifications spéciales apportées au contrat Standard qui ciblent certains clients uniquement. Les éditeurs peuvent choisir le locataire qu'ils souhaitent cibler. Les clients de ce locataire achètent le produit selon les conditions du contrat Standard et les modifications ciblées.

![Modifications personnalisées](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Les clients ciblés par les modifications personnalisées reçoivent aussi la modification universelle apportée aux conditions standard lors de l'achat.

>[!Note]
>Les types d’offres suivants prennent en charge les modifications du contrat Standard : applications Azure (modèles de solution et applications managées), machines virtuelles, conteneurs, applications de conteneur.

### <a name="customer-experience"></a>Expérience client

Lors du processus d’achat sur le portail Azure, les clients peuvent voir les conditions du contrat associé au produit en tant que contrat Standard de Microsoft, ainsi que les modifications.

![Expérience client sur le portail Azure.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Les clients peuvent utiliser `Get-AzureRmMarketplaceTerms` pour récupérer les conditions d’une offre et l’accepter. Le contrat Standard et les modifications qui s'y rapportent sont renvoyés dans la sortie de la cmdlet.

---
