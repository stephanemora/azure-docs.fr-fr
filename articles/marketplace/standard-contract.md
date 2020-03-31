---
title: Contrat Standard | Azure
description: Contrat Standard pour la Place de marché Azure et AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: dsindona
ms.openlocfilehash: 00a83a1b3005043f317ed49cafa735540cd21793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284957"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Contrat standard pour la place de marché commerciale de Microsoft

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard pour la place de marché commerciale de Microsoft afin de faciliter les transactions sur la place de marché. Plutôt que d’élaborer des conditions générales personnalisées, les éditeurs de la place de marché commerciale peuvent choisir de proposer leur logiciel dans le cadre du contrat Standard, que les clients ne doivent accepter qu’une seule fois. Le contrat Standard est disponible ici : [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178).

Les conditions générales d’une offre sont définies lors de la création de l’offre dans l’Espace partenaires ou le Portail Cloud Partner. Vous pouvez choisir d’utiliser le contrat Standard pour la place de marché commerciale de Microsoft au lieu de fournir vos propres conditions générales.

>[!Note]
>Après avoir publié une offre à l’aide du contrat Standard pour la place de marché commerciale de Microsoft, vous ne pouvez pas utiliser vos propres conditions générales. Vous devez faire un choix entre les deux. Vous proposez votre solution avec le contrat Standard *ou* avec vos propres conditions générales. Si vous souhaitez modifier les conditions du contrat Standard, vous pouvez le faire par le biais des Modifications du contrat Standard.

## <a name="standard-contract-amendments"></a>Contrat Standard | Modifications

Les modifications du contrat Standard permettent aux éditeurs de sélectionner le contrat Standard par souci de simplicité, et avec des conditions personnalisées pour leur produit ou entreprise. Les clients sont invités à examiner les modifications apportées au contrat, puis à accepter le contrat Standard de Microsoft.

Deux types de modifications sont proposées aux éditeurs de la place de marché commerciale :

* Modifications universelles : Ces modifications sont appliquées de manière universelle au contrat Standard de tous les clients. Les modifications universelles s’affichent pour chaque client de l’offre dans le flux d’achat. Les clients doivent accepter les conditions du contrat Standard et la modification avant de pouvoir utiliser votre offre.

* Modifications personnalisées : Il s’agit de modifications spéciales apportées au contrat Standard qui ciblent certains clients uniquement via les ID de locataires Azure. Les éditeurs peuvent choisir le locataire qu'ils souhaitent cibler. Seuls les clients du locataire recevront les conditions personnalisées dans le flux d’achat de l’offre.  Les clients doivent accepter les conditions du contrat Standard et la ou les modifications avant de pouvoir utiliser votre offre.

>[!Note]
>Ces deux types de modifications s’ajoutent l’un à l’autre. Les clients ciblés par les modifications personnalisées reçoivent aussi la modification universelle apportée au contrat Standard lors de l’achat.

Vous pouvez tirer parti du contrat Standard pour la place de marché commerciale de Microsoft pour les types d’offre suivants :  applications Azure (modèles de solution et applications managées), machines virtuelles, conteneurs, applications de conteneur, modules IoT Edge et SaaS.

## <a name="customer-experience"></a>Expérience client

Pendant l’expérience de découverte dans la Place de marché Azure ou AppSource, les clients pourront voir les conditions associées à l’offre sous la forme du contrat Standard pour la place de marché commerciale de Microsoft et toute modification universelle.

![Expérience de découverte du client sur le portail Azure.](media/marketplace-publishers-guide/azure-discovery-process.png)

Pendant le processus d’achat sur le portail Azure, les clients pourront voir les conditions associées à l’offre sous la forme du contrat Standard pour la place de marché commerciale de Microsoft et toute modification universelle et/ou spécifique au locataire.

![Expérience d’achat du client sur le portail Azure.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Les clients peuvent utiliser Get-AzureRmMarketplaceTerms pour récupérer les conditions d’une offre et l’accepter. Le contrat Standard et les modifications qui s'y rapportent sont renvoyés dans la sortie de la cmdlet.
