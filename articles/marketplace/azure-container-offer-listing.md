---
title: Configurer les détails d’une annonce d’offre Azure Container sur Microsoft AppSource
description: Configurez les détails d’une annonce d’offre Azure Container sur Microsoft AppSource.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 16bac156af04394552c280271d13ecbe41a1982b
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110615177"
---
# <a name="configure-azure-container-offer-listing-details"></a>Configurer les détails d’une annonce d’offre Azure Container

Cette page vous permet de définir les détails de l’offre, notamment le nom de l’offre, la description, les liens, les contacts, les logos et les captures d’écran.

> [!NOTE]
> Fournissez les détails de l’offre dans une seule langue uniquement. L’anglais n’est pas obligatoire tant que la description de l’offre commence par la phrase « This application is available only in [langue non anglaise]. » Vous pouvez également de fournir une *URL de lien utile* pour offrir du contenu dans une langue autre que celle utilisée dans le contenu du référencement de l’offre.

## <a name="marketplace-details"></a>Détails de la Place de marché

Le **nom** que vous entrez ici est présenté aux clients comme le titre de l’offre. Ce champ est prérempli avec le nom que vous avez entré pour **Alias d’offre** lorsque vous avez créé l’offre, mais vous pouvez le modifier. Le nom :

- Peut inclure les symboles de marque déposée et de copyright.
- Doit avoir 50 caractères au maximum.
- Ne peut pas inclure d’emojis.

Entrez une courte description de votre offre pour le **résumé des résultats de recherche** (100 caractères maximum). Cette description pourra être utilisée dans les résultats de recherche de la place de marché.

Fournissez une **brève description** de votre offre, jusqu’à 256 caractères. Elle apparaîtra dans les résultats de la recherche et sur la page de détails de votre offre.

[!INCLUDE [Long description-1](includes/long-description-1.md)]

[!INCLUDE [Long description-2](includes/long-description-2.md)]

Utilisez des balises HTML pour mettre en forme votre description afin qu’elle soit plus engageante. Pour obtenir la liste des balises autorisées, consultez [Balises HTML prises en charge](supported-html-tags.md).

Entrez l’adresse web (URL) de la politique de confidentialité de votre organisation. Veillez à ce que votre offre respecte les lois et les réglementations en matière de confidentialité. Vous devez également publier une politique de confidentialité valide sur votre site Web.

## <a name="useful-links"></a>Liens utiles

Fournissez des documents en ligne complémentaires à propos de votre offre. Vous pouvez ajouter jusqu’à 25 liens. Pour ajouter un lien, sélectionnez **+ Ajouter un lien**, puis complétez les champs suivants :

- **Nom** : Les clients le verront dans la page de détails de votre offre.
- **Lien** (URL) : Entrez un lien pour que les clients puissent voir votre document en ligne. Le lien doit démarrer par http:// ou https://.

### <a name="contact-information"></a>Informations de contact

Entrez le nom, l’adresse e-mail et le numéro de téléphone d’un **contact du support**, d’un **contact ingénierie** et d’un **contact du programme de fournisseur de solutions Cloud**. Ces informations ne sont pas présentées aux clients, mais seront disponibles pour Microsoft et pourront être fournies aux partenaires CSP.

Dans la section **Contact du support**, fournissez le **site web du support** où les clients d’Azure Global et d’Azure Government (le cas échéant) peuvent joindre votre équipe du support technique.

## <a name="marketplace-media"></a>Médias de la Place de marché

Fournissez des logos et images à utiliser avec votre offre. Toutes les images doivent être au format PNG. Votre demande sera rejetée si les images sont floues.

>[!NOTE]
>Si vous rencontrez un problème pendant le chargement de fichiers, vérifiez que votre réseau local ne bloque pas le service https://upload.xboxlive.com utilisé par l’Espace partenaires.

### <a name="logos"></a>Logos

Fournissez un fichier PNG pour le logo de **Grande taille**. L'Espace partenaires l'utilisera pour créer d’autres tailles requises. Plus tard, vous pourrez éventuellement le remplacer par une autre image.

Ces logos sont utilisés à différents emplacements dans la liste :

[!INCLUDE [logos-appsource-only](includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](includes/graphics-suggestions.md)]

### <a name="screenshots"></a>Captures d’écran.

Ajoutez au moins une capture d’écran (et jusqu’à cinq maximum) qui illustre le fonctionnement de votre offre. Toutes les captures d’écran doivent être au format PNG en 1280 x 720 pixels. Ajoutez une légende pour chaque capture d’écran.

### <a name="videos"></a>Vidéos

Ajoutez jusqu’à cinq vidéos facultatives qui illustrent votre offre. Elles doivent être hébergées sur un service vidéo externe. Entrez le nom, l’adresse web et une image miniature PNG de 1280 x 720 pixels de chaque vidéo.

Pour obtenir des ressources supplémentaires concernant la place de marché, consultez [Meilleures pratiques pour les annonces d’offre d’une place de marché](gtm-offer-listing-best-practices.md).

Sélectionnez **Enregistrer le brouillon** avant de passer à l’onglet suivant du menu de navigation gauche **Public de la préversion**.
<!-- #### Offer examples

The following examples show how the offer listing fields appear in different places of the offer.

This shows search results in Azure Marketplace:

[![Illustrates the search results in Azure Marketplace](media/azure-container/azure-create-7-search-results-mkt-plc-small.png)](media/azure-container/azure-create-7-search-results-mkt-plc.png#lightbox)

This shows the **Offer listing** page in Azure portal:

:::image type="content" source="media/azure-container/azure-create-8-offer-listing-portal.png" alt-text="Illustrates the Offer listing page in Azure portal.":::

This shows search results in Azure portal:

[![Illustrates the search results in Azure portal.](media/azure-container/azure-create-9-search-results-portal-small.png)](media/azure-container/azure-create-9-search-results-portal.png#lightbox) -->

## <a name="next-steps"></a>Étapes suivantes

- [Définir le public de la préversion de l’offre](azure-container-preview-audience.md)
