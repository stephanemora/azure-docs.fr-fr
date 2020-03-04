---
title: Configurer un nom de domaine personnalisé pour une instance Gestion des API Azure
titleSuffix: Azure API Management
description: Cette rubrique explique comment configurer un nom de domaine personnalisé pour votre instance de gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 887019bbdb92807d49c09af3a83313470f334a52
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649538"
---
# <a name="configure-a-custom-domain-name"></a>Configuration d’un nom de domaine personnalisé

Lorsque vous créez une instance du service Gestion des API Azure, Azure lui attribue un sous-domaine d’`azure-api.net` (par exemple, `apim-service-name.azure-api.net`). Toutefois, vous pouvez exposer vos points de terminaison APIM via votre propre nom de domaine personnalisé, par exemple, **contoso.com**. Ce didacticiel explique comment mapper un nom DNS personnalisé existant à des points de terminaison exposés par une instance APIM.

> [!IMPORTANT]
> Le service Gestion des API accepte uniquement les demandes avec des valeurs d’[en-tête d’hôte](https://tools.ietf.org/html/rfc2616#section-14.23) correspondant au nom de domaine par défaut ou à l’un des noms de domaine personnalisés configurés.

> [!WARNING]
> Les clients qui souhaitent utiliser un épinglage de certificat pour améliorer la sécurité de leurs applications doivent utiliser un nom de domaine personnalisé et un certificat qu’ils gèrent, et non pas le certificat par défaut. Les clients qui épinglent le certificat par défaut à la place auront une dépendance forte vis-à-vis des propriétés du certificat qu’ils ne contrôlent pas, ce qui n’est pas une pratique recommandée.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

-   Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
-   Un nom de domaine personnalisé qui vous appartient ou à votre organisation. Cette rubrique ne fournit aucune instruction sur l’approvisionnement d’un nom de domaine personnalisé.
-   Un enregistrement CNAME hébergé sur un serveur DNS qui mappe le nom de domaine personnalisé au nom de domaine par défaut de votre instance Gestion des API. Cette rubrique ne fournit aucune instruction sur l’hébergement d’un enregistrement CNAME.
-   Vous devez disposer d’un certificat valide et d’une clé publique et privée (.PFX). L’objet ou l’autre nom de l’objet (SAN) doit correspondre au nom du domaine. Cela permet à l’instance APIM d’exposer des URL de manière sécurisée, via SSL.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utiliser le portail Azure pour définir un nom de domaine personnalisé

1. Accédez à votre instance APIM dans le [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Domaines personnalisés**.

    Vous pouvez attribuer un nom de domaine personnalisé à plusieurs points de terminaison. Actuellement, les points de terminaison disponibles sont les suivants :

    - **Passerelle** (valeur par défaut : `<apim-service-name>.azure-api.net`)
    - **Portail** (valeur par défaut : `<apim-service-name>.portal.azure-api.net`)
    - **Gestion** (valeur par défaut : `<apim-service-name>.management.azure-api.net`)
    - **SCM** (valeur par défaut : `<apim-service-name>.scm.azure-api.net`)
    - **NewPortal** (valeur par défaut : `<apim-service-name>.developer.azure-api.net`)

    > [!NOTE]
    > Seul le point de terminaison de la **passerelle** peut être configuré dans le niveau Consommation.
    > Vous pouvez mettre à jour tous les points de terminaison ou certains d’entre eux. En règle générale, les clients mettent à jour les points de terminaison **Passerelle** (cette URL est utilisée pour appeler l’API exposée via la gestion des API) et **Portail** (URL du portail des développeurs).
    > Les points de terminaison **Gestion** et **SCM** sont utilisés en interne par les propriétaires d’instance APIM uniquement. Pour cette raison, ils se voient moins fréquemment attribuer un nom de domaine personnalisé.
    > Toutefois, le niveau **Premium** prend en charge la définition de plusieurs noms d’hôte pour le point de terminaison **Passerelle**.

1. Sélectionnez le point de terminaison que vous souhaitez mettre à jour.
1. Dans la fenêtre de droite, cliquez sur **Personnalisé**.

    - Dans la zone **Nom de domaine personnalisé**, spécifiez le nom que vous souhaitez utiliser. Par exemple : `api.contoso.com`.
    - Dans le **certificat**, sélectionnez un certificat de Key Vault. Vous pouvez également charger un fichier .PFX valide et fournir son **mot de passe** si le certificat est protégé par un mot de passe.

    > [!NOTE]
    > Les noms de domaine génériques, `*.contoso.com` par exemple, sont pris en charge à tous les niveaux, à l’exception du niveau Consommation.

    > [!TIP]
    > Nous vous recommandons d’utiliser Azure Key Vault pour gérer les certificats et les définir sur Rotation automatique.
    > Si vous utilisez Azure Key Vault pour gérer le certificat SSL de domaine personnalisé, assurez-vous que le certificat est inséré dans Key Vault [comme un _certificat_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), et non un _secret_.
    >
    > Pour extraire un certificat SSL, Gestion des API doit disposer de la liste et des autorisations nécessaires pour obtenir les secrets sur le coffre de clés Azure Key Vault contenant le certificat. Lorsque vous utilisez le portail Azure, toutes les étapes nécessaires pour la configuration sont effectuées automatiquement. Lorsque vous utilisez les outils de ligne de commande ou APIM, ces autorisations doivent être accordées manuellement. Cette opération comprend deux étapes. Utilisez tout d’abord la page Identités managées sur votre instance APIM pour vous assurer que l’identité managée est activée. Notez aussi l’ID du principal qui s’affiche sur cette page. Ensuite, sur le coffre de clés Azure Key Vault contenant le certificat, fournissez la liste des autorisations à cet ID du principal et accordez-lui les autorisations nécessaires pour obtenir les secrets.
    >
    > Si le certificat est défini sur Rotation automatique, APIM utilisera automatiquement la dernière version sans que le service ne rencontre le moindre temps d’arrêt (si votre niveau d’APIM comprend un contrat de niveau de service, c’est-à-dire tous les niveaux sauf le niveau Développeur).

1. Cliquez sur Appliquer.

    > [!NOTE]
    > Le processus d’attribution du certificat peut prendre 15 minutes ou plus, selon la taille du déploiement. La référence SKU de développeur présente des temps d’arrêt, mais pas les références SKU de base ou supérieures.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuration DNS

Lorsque vous configurez DNS pour votre nom de domaine personnalisé, vous avez le choix entre deux options :

-   Configurer un enregistrement CNAME qui pointe vers le point de terminaison de votre nom de domaine personnalisé configuré.
-   Configurer un enregistrement A qui pointe vers l’adresse IP de votre passerelle de gestion des API.

> [!NOTE]
> Bien que l’adresse IP de l’instance de gestion des API soit statique, elle peut changer dans quelques scénarios. Pour cette raison, il est recommandé d’utiliser CNAME lors de la configuration d’un domaine personnalisé. Tenez-en compte lorsque vous choisissez la méthode de configuration DNS. Pour plus d’informations, consultez le [FAQ sur la gestion des API](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau votre service et le mettre à l’échelle](upgrade-and-scale.md)
