---
title: Conditions préalables de la machine virtuelle pour Microsoft Azure | Microsoft Docs
description: Liste des conditions préalables requises pour publier une offre de machine virtuelle sur la place de marché Azure.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010847"
---
# <a name="virtual-machine-prerequisites"></a>Configuration requise pour les machines virtuelles

Cet article répertorie les deux techniques et les besoins de l’entreprise qui doivent être réunies avant de vous peuvent publier une offre de machine virtuelle pour le [place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/).  Si vous ne le n'avez pas déjà fait, passez en revue la [Guide de publication d’ordinateur virtuel offre](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Exigences techniques

Les conditions techniques préalables pour la publication d’une solution de machine virtuelle (VM) sont simples :

- Vous devez disposer d’un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire sur le [site Microsoft Azure](https://azure.microsoft.com).  
- Vous devez disposer d’un environnement configuré pour prendre en charge le développement des VM Windows ou Linux.  Pour plus d’informations, consultez la documentation relative aux machines virtuelles :
    - [Documentation sur les machines virtuelles Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Documentation sur les machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Exigences commerciales

Les exigences de l’entreprise incluent des obligations procédurales, contractuelles et légales : 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Vous devez être inscrit en tant qu’éditeur de la place de marché Cloud.  Si vous n’êtes pas encore inscrit, suivez les étapes décrites dans l’article [Become a Cloud Marketplace Publisher](../../become-publisher.md) (Devenir un éditeur de place de marché Cloud).

    > [!NOTE]
    > Vous devez utiliser le même compte d’inscription à Microsoft Developer Center pour se connecter au [portail Cloud Partner](https://cloudpartner.azure.com).
    > Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Celui-ci ne doit pas être propre à des services ou offres individuels.
    
- Votre entreprise (ou sa filiale) doit être située dans un pays expéditeur pris en charge par la Place de marché Microsoft Azure.  Pour obtenir la liste actuelle de ces pays, consultez les [politiques concernant la participation à la Place de marché Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Microsoft Azure.  Pour plus d’informations, consultez [Options de facturation sur la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
- Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
- Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail Azure. <!-- TD: Meaning/links? -->
- Vous devez accepter les termes de la [Stratégie de participation de la Place de marché Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) et du Contrat d’éditeur.
- Vous devez respecter les [Microsoft Azure site Web de conditions d’utilisation](https://azure.microsoft.com/support/legal/website-terms-of-use/), [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement), et [accord du programme Azure Certified Microsoft](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous remplissez ces conditions préalables, vous pouvez [créer votre offre de machine virtuelle](./cpp-create-offer.md).
