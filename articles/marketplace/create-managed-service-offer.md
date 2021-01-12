---
title: Création d’une offre de service managé sur la Place de marché commerciale Microsoft
description: Découvrez comment créer une nouvelle offre de service managé pour la Place de marché Azure à l’aide du programme de place de marché commerciale dans Espace partenaires Microsoft.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97917951"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Création d’une offre de service managé pour la Place de marché commerciale

Cet article explique comment créer une offre de service managé sur la Place de marché commerciale de Microsoft à l’aide d’Espace partenaires.

Pour publier une offre de service managé, vous devez avoir acquis une compétence Microsoft Gold ou Silver dans la plateforme cloud. Si vous ne l’avez pas déjà fait, lisez [Planifier une offre de service managé pour la Place de marché commerciale](./plan-managed-service-offer.md). Cela vous aidera à préparer les ressources nécessaires lorsque vous créez l’offre dans Espace partenaires.

## <a name="create-a-new-offer"></a>Créer une offre

1. Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/home).
2. Dans le menu de navigation de gauche, sélectionnez **Place de marché commerciale** > **Vue d’ensemble**.
3. Dans l’onglet Vue d’ensemble, sélectionnez **+ Nouvelle offre** > **Service géré**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Illustre le menu de navigation de gauche.":::

4. Dans la boîte de dialogue **Nouvelle offre**, entrez l’**ID de l’offre**. Il s’agit d’un identificateur unique par offre dans votre compte. Cet ID est visible dans l’URL du référencement de la place de marché commercial et des modèles Azure Resource Manager, le cas échéant. Par exemple, si vous entrez test-offer-1 dans cette zone, l’adresse web de l’offre sera `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

    * Chaque offre au sein de votre compte doit avoir un ID d’offre unique.
    * Utilisez uniquement des lettres minuscules et des chiffres. Il peut inclure des traits d’union et des traits de soulignement, mais pas d’espaces, et est limité à 50 caractères.
    * L’ID d’offre ne peut pas être changé une fois que vous avez sélectionné **Créer**.

5. Entrez un **Alias d’offre**. Il s’agit du nom attribué à l’offre dans l’Espace partenaires. Il n’est pas visible dans les magasins en ligne et est différent du nom de l’offre présenté aux clients.
6. Pour générer l’offre et continuer, sélectionnez **Créer**.

## <a name="configure-lead-management"></a>Configurer la gestion des prospects

Connectez votre système de Gestion des relations avec la clientèle (CRM) à votre offre de marketplace commercial pour recevoir les coordonnées d’un client qui exprime son intérêt pour votre service de conseil. Vous pouvez modifier cette connexion à tout moment pendant ou après la création de l’offre. Pour obtenir des instructions détaillées, consultez [Prospects de votre offre de marketplace commercial](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Pour configurer la gestion des prospects dans Espace partenaires :

1. Dans Espace partenaires, accédez à l’onglet **Configuration de l’offre**.
2. Sous **Prospects**, sélectionnez le lien **Se connecter**.
3. Dans la boîte de dialogue **Détails de la connexion**, sélectionnez une destination de prospect dans la liste.
4. Renseignez les champs qui s’affichent. Pour des instructions détaillées, consultez les articles suivants :

    * [Configurer votre offre pour envoyer des prospects à la table Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configurer votre offre pour envoyer des prospects à Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anciennement Dynamics CRM en ligne)
    * [Configurer votre offre pour l’envoi des prospects au point de terminaison HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configurer votre offre pour envoyer des prospects à Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configurer votre offre pour envoyer des prospects à Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Pour valider la configuration que vous avez fournie, sélectionnez le lien **Valider**.
6. Une fois que vous avez configuré les détails de la connexion, sélectionnez **Connexion**.
7. Sélectionnez **Enregistrer le brouillon**.

Une fois que vous avez envoyé votre offre dans Espace partenaires en vue de sa publication, nous validons la connexion et vous envoyons un prospect de test. Quand vous visualisez l’offre avant son lancement, testez votre connexion de prospect en essayant d’acheter vous-même l’offre dans l’environnement de préversion.

> [!TIP]
> Assurez-vous que la connexion à la destination de prospect reste à jour afin de ne perdre aucun prospect.

## <a name="configure-offer-properties"></a>Configurer les propriétés de l’offre

Dans la page Propriétés de votre offre dans Espace partenaires, vous allez définir les catégories applicables à votre offre et vos contrats juridiques. Ces informations garantissent que votre service géré est correctement affiché dans le magasin en ligne et proposé au bon groupe de clients.

### <a name="select-a-category"></a>Sélectionner une catégorie

Sous **Catégories**, sélectionnez au moins une et jusqu’à cinq catégories pour grouper votre offre dans les zones de recherche commerciales appropriées de la place de marché.

### <a name="provide-terms-and-conditions"></a>Entrer des conditions d’utilisation

Sous **Juridique**, entrez les conditions générales de cette offre. Les clients devront les accepter avant d’utiliser l’offre. Vous pouvez également fournir l’URL permettant d’accéder à vos conditions générales.

Sélectionnez **Enregistrer le brouillon** avant de continuer.

## <a name="next-step"></a>Étape suivante

* [Configurer le référencement de votre offre de service managé](./create-managed-service-offer-listing.md)