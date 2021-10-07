---
title: Créer une offre de visuels Power BI dans l’Espace partenaires pour Microsoft AppSource
description: Découvrez comment créer une offre de visuels Power BI dans l’Espace partenaires.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: eca02cf776eb1f6fa690ecefd865dadbc3d9be78
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080058"
---
# <a name="create-a-power-bi-visual-offer"></a>Créer une offre de visuels Power BI

Cet article explique comment utiliser l’Espace partenaires pour soumettre une offre de visuels Power BI à [Microsoft AppSource](https://appsource.microsoft.com) afin que d’autres utilisateurs puissent la découvrir et l’utiliser.

Avant de commencer, créez un compte de marketplace commercial dans [Espace partenaires](./create-account.md) et vérifiez qu’il est inscrit au programme de marketplace commercial.

## <a name="before-you-begin"></a>Avant de commencer

Consultez [Planifier une offre de visuels Power BI](marketplace-power-bi-visual.md). Cet article explique les exigences techniques de cette offre et répertorie les informations et ressources dont vous aurez besoin pour la créer.

## <a name="create-a-new-offer"></a>Créer une offre

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vues d’espaces de travail](#tab/workspaces-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).

1. Dans la page d’accueil, sélectionnez la vignette **Offres de la Place de marché**.

    [ ![Illustre la vignette des offres de la Place de marché dans la page d’accueil de l’Espace partenaires.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Dans la page des offres de la Place de marché, sélectionnez **+ Nouvelle offre** > **Visuel Power BI**.

    [ ![Montre les options du menu du volet gauche et le bouton Nouvelle offre.](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png) ](media/power-bi-visual/new-offer-power-bi-visual-workspaces.png#lightbox)

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
1. Dans le menu de navigation de gauche, sélectionnez **Marketplace commercial** > **Vue d’ensemble**.
1. Dans la page Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Visuel Power BI**.

    :::image type="content" source="media/power-bi-visual/new-offer-power-bi-visual.png" alt-text="Montre les options du menu du volet gauche et le bouton Nouvelle offre.":::

---

> [!IMPORTANT]
> Une fois qu’une offre est publiée, les modifications que vous y apportez dans l’Espace partenaires n’apparaissent dans AppSource qu’après la republication de l’offre. Veillez à toujours republier une offre après l’avoir modifiée.

## <a name="new-offer"></a>Nouvelle offre

Entrez un **ID d’offre**. Il s’agit d’un identificateur unique par offre dans votre compte.

- Cet ID est visible par les clients dans l’adresse web de l’offre et dans les modèles Azure Resource Manager, le cas échéant.
- Utilisez uniquement des lettres minuscules et des chiffres. L’ID peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères. Par exemple, si votre ID d’éditeur est `testpublisherid` et que vous entrez **test-offer-1**, l’adresse web de l’offre sera `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1`.
- L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.
- L’ID d’offre doit être unique dans la liste de toutes les autres offres de visuels Power BI au sein de l’Espace partenaires.

Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires.

- Ce nom n’est pas utilisé dans AppSource. Il est différent du nom de l’offre et des autres valeurs présentées aux clients.

Sélectionnez **Créer** pour générer l’offre. Espace partenaires ouvre la page **Configuration de l’offre**.

## <a name="setup-details"></a>Détails de la configuration

Pour les **achats supplémentaires**, indiquez si votre offre nécessite ou non des achats de services ou des achats intégrés supplémentaires.

Pour la **certification Power BI** (facultatif), lisez attentivement la description. Si vous souhaitez demander la certification Power BI, cochez la case. Les visuels Power BI [certifiés](https://aka.ms/PBIvisualcertification) répondent à certains impératifs de code spécifiques, que l’équipe de Microsoft Power BI a testés et approuvés. Nous vous recommandons de soumettre et de publier votre visuel Power BI *avant* de demander une certification, car le processus de certification nécessite un délai supplémentaire, ce qui peut retarder la publication de votre offre.

## <a name="customer-leads"></a>Prospects

[!INCLUDE [Connect lead management](includes/customer-leads.md)]

La connexion à un CRM est facultative. Pour plus d’informations, consultez [Prospects de votre offre de marketplace commercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de poursuivre vers le prochaine tabulation du menu de navigation gauche **Propriétés**.

## <a name="next-steps"></a>Étapes suivantes

- [**Propriétés de l’offre**](power-bi-visual-properties.md)
