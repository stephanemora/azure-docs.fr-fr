---
title: Comment créer une offre de services de conseil dans le marketplace commercial de Microsoft
description: Découvrez comment créer une nouvelle offre de service de conseil pour Microsoft AppSource ou Place de marché Azure à l’aide du programme de marketplace commercial dans Espace partenaires Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: af8e238d57d6168e8c9620177cbf8e97a7d08727
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083014"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Comment créer une offre de services de conseil dans le marketplace commercial

Cet article explique comment créer une offre de services de conseil pour le marketplace commercial de Microsoft à l’aide d’Espace partenaires.

## <a name="before-you-begin"></a>Avant de commencer

Pour publier une offre de service de conseil, vous devez respecter certaines conditions d’éligibilité pour démontrer votre expertise dans votre domaine. Si vous ne l’avez pas déjà fait, lisez [Planifier une offre de services de conseil pour le marketplace commercial](./plan-consulting-service-offer.md). L’article décrit les conditions préalables à un service de conseil et les ressources dont vous aurez besoin lorsque vous créerez l’offre avec Espace partenaires.

## <a name="create-a-new-consulting-service-offer"></a>Créer une offre de service de conseil

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[Vues d’espaces de travail](#tab/workspaces-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
1. Dans la page d’accueil, sélectionnez la vignette **Offres de la Place de marché**.

    [ ![Illustre la vignette des offres de la Place de marché dans la page d’accueil de l’Espace partenaires.](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Dans la page des offres de la Place de marché, sélectionnez **+ Nouvelle offre** > **Service de conseil**.

    [ ![Illustre la liste des nouvelles offres dans la page des offres de la Place de marché.](./media/new-offer-consulting-service-workspaces.png) ](./media/new-offer-consulting-service-workspaces.png#lightbox)

1. Dans la boîte de dialogue **Nouveau service de conseil**, entrez un **ID d’offre**. Cet ID est visible dans l’URL de la publication sur le marketplace commercial. Par exemple, si vous entrez test-offer-1 dans cette zone, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    * Chaque offre au sein de votre compte doit avoir un ID d’offre unique.
    * Utilisez uniquement des lettres minuscules et des chiffres. L’ID de l’offre peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères.
    * Vous ne pouvez pas modifier L’ID de l’offre une fois que vous avez sélectionné **Créer**.

1. Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires. Il n’est pas visible dans les magasins en ligne et est différent du nom de l’offre présenté aux clients.
1. Pour générer l’offre et continuer, sélectionnez **Créer**.

#### <a name="current-view"></a>[Affichage actuel](#tab/current-view)

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
1. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
1. Dans l’onglet Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Service de conseil**.

    ![Illustre le menu de navigation de gauche.](./media/new-offer-consulting-service.png)

1. Dans la boîte de dialogue **Nouvelle offre**, entrez l’**ID de l’offre**. Cet ID est visible dans l’URL de la publication sur le marketplace commercial. Par exemple, si vous entrez test-offer-1 dans cette zone, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    * Chaque offre au sein de votre compte doit avoir un ID d’offre unique.
    * Utilisez uniquement des lettres minuscules et des chiffres. L’ID de l’offre peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères.
    * Vous ne pouvez pas modifier L’ID de l’offre une fois que vous avez sélectionné **Créer**.

1. Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires. Il n’est pas visible dans les magasins en ligne et est différent du nom de l’offre présenté aux clients.
1. Pour générer l’offre et continuer, sélectionnez **Créer**.

---

## <a name="configure-lead-management"></a>Configurer la gestion des prospects

Connectez votre système de Gestion des relations avec la clientèle (CRM) à votre offre de marketplace commercial pour recevoir les coordonnées d’un client qui exprime son intérêt pour votre service de conseil. Vous pouvez modifier cette connexion à tout moment pendant ou après la création de l’offre. Pour obtenir des instructions détaillées, consultez [Prospects de votre offre de marketplace commercial](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Pour configurer la gestion des prospects dans Espace partenaires :

1.  Dans Espace partenaires, accédez à l’onglet **Configuration de l’offre**.
2.  Sous **Prospects**, sélectionnez le lien **Se connecter**.
3.  Dans la boîte de dialogue **Détails de la connexion**, sélectionnez une destination de prospect dans la liste.
4.  Renseignez les champs qui s’affichent. Pour des instructions détaillées, consultez les articles suivants :

    * [Configurer votre offre pour envoyer des prospects à la table Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configurer votre offre pour envoyer des prospects à Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anciennement Dynamics CRM en ligne)
    * [Configurer votre offre pour l’envoi des prospects au point de terminaison HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configurer votre offre pour envoyer des prospects à Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configurer votre offre pour envoyer des prospects à Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Pour valider la configuration que vous avez fournie, sélectionnez le lien **Valider**.
6.  Une fois que vous avez configuré les détails de la connexion, sélectionnez **Connexion**.
7.  Sélectionnez **Enregistrer le brouillon**.

Une fois que vous avez envoyé votre offre dans Espace partenaires en vue de sa publication, nous validons la connexion et vous envoyons un prospect de test. Quand vous visualisez l’offre avant son lancement, testez votre connexion de prospect en essayant d’acheter vous-même l’offre dans l’environnement de préversion.

> [!TIP]
> Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

## <a name="next-steps"></a>Étapes suivantes

* [Comment configurer les propriétés de votre offre de service de conseil](./create-consulting-service-offer-properties.md)
