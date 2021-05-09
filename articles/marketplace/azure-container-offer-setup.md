---
title: Créer une offre Azure Container dans la Place de marché Azure
description: Créez une offre Azure Container dans la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: a8c10ea5bdfab20ed5a250fba656527cd7935527
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129402"
---
# <a name="create-an-azure-container-offer"></a>Créer une offre de conteneur Azure

Cet article explique comment créer une offre Azure Container. Toutes les offres sont soumises à un processus de certification. Celui-ci vérifie que votre solution respecte les exigences standard, la compatibilité et les pratiques appropriées.

Avant de commencer, créez un compte de marketplace commercial dans [Espace partenaires](./create-account.md) et vérifiez qu’il est inscrit au programme de marketplace commercial.

## <a name="before-you-begin"></a>Avant de commencer

Consultez [Planifier une offre de conteneur Azure](marketplace-containers.md). Cet article explique les exigences techniques de cette offre et répertorie les informations et ressources dont vous aurez besoin pour la créer.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans la page de présentation, sélectionnez **+ Nouvelle offre** > **Conteneur Azure**.

    :::image type="content" source="media/azure-container/new-offer-azure-container.png" alt-text="Options du menu du volet gauche et bouton « Nouvelle offre »":::

> [!IMPORTANT]
> Après publication d’une offre, les modifications que vous y apportez dans l’Espace partenaires apparaissent sur la Place de marché Azure uniquement après la republication de l’offre. Veillez à toujours republier une offre après l’avoir modifiée.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre et les modèles Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si votre ID d’éditeur est `testpublisherid` et que vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé sur la Place de marché Azure. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.

Sélectionnez **Créer** pour générer l’offre. Espace partenaires ouvre la page **Configuration de l’offre**.

## <a name="alias"></a>Alias

Entrez un nom descriptif que nous utiliserons pour faire référence à cette offre uniquement dans l’Espace partenaires. L’alias de l’offre (pré-rempli avec les informations que vous avez entrées lors de la création de l’offre) n’est pas utilisé dans la Place de marché et est différent du nom de l’offre présenté aux clients. Si vous souhaitez mettre à jour le nom de l’offre par la suite, consultez la page [Annonce de l’offre](azure-container-offer-listing.md).

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Pour plus d’informations, consultez [Prospects de votre offre de place de marché commerciale](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers le prochaine tabulation du menu de navigation gauche **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les propriétés de l’offre](azure-container-properties.md)
- [Bonnes pratiques pour le référencement des offres](gtm-offer-listing-best-practices.md)