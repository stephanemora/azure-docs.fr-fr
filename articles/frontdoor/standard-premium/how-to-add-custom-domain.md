---
title: Guide pratique pour ajouter un domaine personnalisé à votre configuration SKU Standard/Premium d’Azure Front Door
description: Avec ce tutoriel, vous allez apprendre à intégrer un domaine personnalisé à un SKU Standard/Premium d’Azure Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 164e06024844fb5262586450b737db9c807e373a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097691"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Créer un domaine personnalisé sur un SKU Standard/Premium d’Azure Front Door (préversion) à l’aide du portail Azure

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Quand vous utilisez Azure Front Door Standard/Premium pour la livraison d’applications, vous avez besoin d’un domaine personnalisé si vous souhaitez que votre propre nom de domaine soit visible dans vos requêtes d’utilisateur final. Un nom de domaine visible peut être pratique pour vos clients et utile à des fins de personnalisation.

Quand vous créez un profil Azure Front Door Standard/Premium, l’hôte front-end par défaut a le sous-domaine azurefd.net. Ce sous-domaine est inclus dans l’URL quand Azure Front Door Standard/Premium distribue le contenu à partir de votre back-end par défaut. Par exemple : `https://contoso-frontend.azurefd.net/activeusers.htm`. Par commodité, Azure Front Door permet d’associer un domaine personnalisé à l’hôte par défaut. Avec cette option, vous distribuez votre contenu avec un domaine personnalisé dans votre URL et non un nom de domaine appartenant à Azure Front Door Standard/Premium. Par exemple : https://www.contoso.com/photo.png.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis
* Avant de pouvoir effectuer les étapes de ce tutoriel, vous devez d’abord créer un Front Door. Pour plus d’informations, consultez [Démarrage rapide : Créer un profil Azure Front Door Standard/Premium](create-front-door-portal.md).

* Si vous n’avez pas encore de domaine personnalisé, vous devez tout d’abord en acheter un auprès d’un fournisseur de domaine. Par exemple, consultez [Acheter un nom de domaine personnalisé](../../app-service/manage-custom-dns-buy-domain.md).

* Si vous utilisez Azure pour héberger vos [domaines DNS](../../dns/dns-overview.md), vous devez déléguer le DNS (Domain Name System) du fournisseur de domaine à Azure DNS. Pour plus d’informations, voir [Délégation de domaine à Azure DNS](../../dns/dns-delegate-domain-azure-dns.md). Si, au contraire, vous utilisez un fournisseur de domaine pour gérer votre domaine DNS, vous devez valider manuellement le domaine en entrant les enregistrements TXT DNS demandés.

## <a name="add-a-new-custom-domain"></a>Ajouter un nouveau domaine personnalisé

Les domaines personnalisés sont gérés dans la section Domaines du portail. Un domaine personnalisé peut être créé et validé avant d’être associé à un point de terminaison. Un domaine personnalisé et ses sous-domaines peuvent être associés à un seul point de terminaison à la fois. Toutefois, vous pouvez utiliser différents sous-domaines du même domaine personnalisé pour différentes instances de Front Door. Vous pouvez également mapper des domaines personnalisés ayant différents sous-domaines au même point de terminaison Front Door.

1. Sous Paramètres, dans votre profil Azure Front Door, sélectionnez *Domaines*, puis le bouton **Ajouter un domaine**.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Capture d’écran du bouton Ajouter un domaine sur la page d’arrivée des domaines.":::

1. La page **Ajouter un domaine** s’affiche. Vous pouvez y entrer des informations sur le domaine personnalisé. Vous pouvez choisir un DNS managé par Azure (recommandé) ou utiliser votre propre fournisseur DNS. Si vous choisissez un DNS managé par Azure, sélectionnez une zone DNS existante, puis sélectionnez un sous-domaine personnalisé ou créez-en un. Si vous utilisez un autre fournisseur DNS, entrez manuellement le nom du domaine personnalisé. Sélectionnez **Ajouter** pour ajouter votre domaine personnalisé.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Capture d’écran de la page Ajouter un domaine.":::

    Un nouveau domaine personnalisé est créé avec l’état de validation **Soumission**.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Capture d’écran montrant l’état de validation de domaine Soumission.":::

    Attendez que l’état de validation passe à **En attente**. Cette opération peut prendre quelques minutes.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Capture d’écran montrant l’état de validation de domaine En attente.":::

1. Sélectionnez l’état de validation **En attente**. Une nouvelle page s’affiche avec les informations d’enregistrement TXT DNS nécessaires à la validation du domaine personnalisé. L’enregistrement TXT se présente sous la forme `_dnsauth.<your_subdomain>`. Si vous utilisez une zone Azure DNS, sélectionnez le bouton **Ajouter**. Un nouvel enregistrement TXT avec la valeur d’enregistrement affichée sera créé dans la zone Azure DNS. Si vous utilisez un autre fournisseur DNS, créez manuellement un enregistrement TXT nommé `dnsauth.<your_subdomain>` avec la valeur d’enregistrement comme indiqué sur la page.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Capture d’écran de la page Valider le domaine personnalisé.":::

1. Sélectionnez le bouton Actualiser l’état. Une fois le domaine validé avec l’enregistrement TXT DNS, l’état de validation passe à **vérifié**. La validation peut prendre quelques minutes.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Capture d’écran montrant l’état vérifié du domaine personnalisé.":::

1. Fermez la page pour revenir à la page d’arrivée de la liste de domaines personnalisés. L’état de provisionnement du domaine personnalisé doit passer à **Provisionné** et l’état de validation doit passer à **Approuvé**.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Capture d’écran montrant les états Provisionné et Approuvé.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Associer le domaine personnalisé au point de terminaison Front Door

Après avoir validé votre domaine personnalisé, vous pouvez l’ajouter à votre point de terminaison Azure Front Door Standard/Premium.

1. Une fois le domaine personnalisé validé, vous pouvez l’associer à un point de terminaison et une route Azure Front Door Standard/Premium existants. Sélectionnez le lien **Association du point de terminaison** pour ouvrir la page **Associer le point de terminaison et les routes**. Sélectionnez un point de terminaison et les routes que vous souhaitez lui associer. Sélectionnez ensuite **Associer**. Une fois l’opération d’association terminée, fermez la page.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Capture d’écran de la page Associer le point de terminaison et les routes.":::

    L’état d’association du point de terminaison doit changer selon le point de terminaison auquel le domaine personnalisé est actuellement associé. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Capture d’écran montrant lien d’association du point de terminaison.":::

1. Sélectionnez le lien État DNS.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="Capture d’écran du lien État DNS.":::

1. La page **Ajouter ou mettre à jour l’enregistrement CNAME** s’affiche. Elle présente les informations de l’enregistrement CNAME qui doivent être fournies pour que le trafic commence à circuler. Si vous utilisez des zones hébergées Azure DNS, vous pouvez créer les enregistrements CNAME en sélectionnant le bouton **Ajouter** sur la page. Si vous utilisez un autre fournisseur DNS, vous devez entrer manuellement le nom et la valeur de l’enregistrement CNAME comme indiqué sur la page.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="Capture d’écran de la page d’ajout ou mise à jour de l’enregistrement CNAME.":::

1. Une fois l’enregistrement CNAME créé et le domaine personnalisé associé au point de terminaison Azure Front Door, le trafic commence à circuler.

    > [!NOTE]
    > Si le protocole HTTPS est activé, le provisionnement et la propagation des certificats peuvent prendre quelques minutes. En effet, la propagation s’effectue sur tous les emplacements de périphérie. 

## <a name="verify-the-custom-domain"></a>Vérifier le domaine personnalisé

Après avoir validé et associé le domaine personnalisé, vérifiez qu’il est correctement référencé à votre point de terminaison.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Capture d’écran montrant le domaine personnalisé validé et associé.":::

Enfin, vérifiez que le contenu de votre application est bien traité à l’aide d’un navigateur.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment activer le protocole HTTPS de votre domaine personnalisé, passez au tutoriel suivant.

> [!div class="nextstepaction"]
> [Activer le protocole HTTPS pour un domaine personnalisé]()
