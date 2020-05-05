---
title: Prérequis des machines virtuelles pour Microsoft Azure | Place de marché Azure
description: Liste des conditions préalables requises pour publier une offre de machine virtuelle sur la place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80277614"
---
# <a name="virtual-machine-prerequisites"></a>Configuration requise pour les machines virtuelles

Cet article liste les exigences techniques et commerciales que vous devez respecter avant de pouvoir publier une offre de machine virtuelle sur la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/).  Si ce n’est pas déjà fait, consultez le [Guide de publication d’une offre de machine virtuelle](../../marketplace-virtual-machines.md).


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

- Vous devez être inscrit en tant qu’éditeur de la place de marché Cloud.  Si vous n’êtes pas encore inscrit, suivez les étapes décrites dans l’article [Become a Cloud Marketplace Publisher](https://docs.microsoft.com/azure/marketplace/become-publisher) (Devenir un éditeur de place de marché Cloud).

    > [!NOTE]
    > Vous devez utiliser le même compte d’inscription à Microsoft Developer Center pour se connecter au [portail Cloud Partner](https://cloudpartner.azure.com).
    > Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Celui-ci ne doit pas être propre à des services ou offres individuels.
    
- Votre entreprise (ou sa filiale) doit être située dans un pays/une région expéditeur pris en charge par la Place de marché Azure.  Pour obtenir la liste actuelle de ces pays/régions, consultez les [politiques concernant la participation à la Place de marché Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Microsoft Azure.  Pour plus d’informations, consultez [Options de facturation sur la Place de marché Azure](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
- Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
- Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail Azure. <!-- TD: Meaning/links? -->
- Vous devez accepter les termes de la [Stratégie de participation de la Place de marché Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) et du Contrat d’éditeur.
- Vous devez vous engager à respecter les [Conditions d’utilisation du site web Microsoft Azure](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [Déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement) et le [Contrat relatif au programme Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous remplissez ces prérequis, vous pouvez [créer votre offre de machine virtuelle](./cpp-create-offer.md).
