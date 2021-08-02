---
title: Création d’une offre d’application Power BI sur Microsoft AppSource (Place de marché Azure)
description: Créez une offre d’application Power BI sur Microsoft AppSource (Place de marché Azure).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: e86a97b0f4aa9fef24203128970b8d9cc857cd41
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955385"
---
# <a name="create-a-power-bi-app-offer"></a>Créer une offre d’application Power BI

Cet article explique comment créer une offre d’application Power BI. Toutes les offres sont soumises à un processus de certification. Celui-ci vérifie que votre solution respecte les exigences standard, la compatibilité et les pratiques appropriées.

Avant de commencer, créez un compte de marketplace commercial dans [Espace partenaires](./create-account.md) et vérifiez qu’il est inscrit au programme de marketplace commercial.

## <a name="before-you-begin"></a>Avant de commencer

Consultez [Planification d’une offre Power BI](marketplace-power-bi.md). Cet article explique les exigences techniques de cette offre et répertorie les informations et ressources dont vous aurez besoin pour la créer.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Sur la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Application Power BI**.

    :::image type="content" source="media/power-bi/new-offer-power-bi-app.png" alt-text="Options du menu du volet gauche et bouton « Nouvelle offre »":::

> [!IMPORTANT]
> Après publication d’une offre, les modifications que vous y apportez dans Espace partenaires apparaissent sur Microsoft AppSource uniquement après la republication de l’offre. Veillez à toujours republier une offre après l’avoir modifiée.

Si **Application Power BI** ne s’affiche pas ou n’est pas activé, cela signifie que votre compte n’est pas autorisé à créer ce type d’offre. Vérifiez que vous remplissez toutes les [exigences](marketplace-dynamics-365.md) pour ce type d’offre, notamment l’inscription d’un compte de développeur.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre et dans les modèles Azure Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si votre ID d’éditeur est `testpublisherid` et que vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé sur AppSource. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.
- Ce nom ne peut pas être modifié après que vous ayez sélectionné **Créer**.

Sélectionnez **Créer** pour générer l’offre. Espace partenaires ouvre la page **Configuration de l’offre**.

## <a name="alias"></a>Alias

Entrez un nom descriptif que nous utiliserons pour faire référence à cette offre uniquement dans l’Espace partenaires. L’alias de l’offre (pré-rempli avec les informations que vous avez entrées lors de la création de l’offre) n’est pas utilisé dans la Place de marché et est différent du nom de l’offre présenté aux clients. Si vous souhaitez mettre à jour le nom de l’offre par la suite, consultez la page [Annonce de l’offre](power-bi-app-offer-listing.md).

## <a name="setup-details"></a>Détails de la configuration

Cette section est vide. Elle ne s’applique pas aux applications Power BI.

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

Pour plus d’informations, consultez [Prospects de votre offre de marketplace commercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers le prochaine tabulation du menu de navigation gauche **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les propriétés de l’offre](power-bi-app-properties.md)