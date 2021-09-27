---
title: Configurer un nom de domaine personnalisé pour une instance Gestion des API Azure
titleSuffix: Azure API Management
description: Cette rubrique explique comment configurer un nom de domaine personnalisé pour votre instance de gestion des API Azure.
services: api-management
documentationcenter: ''
author: dlepow
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/24/2021
ms.author: danlep
ms.openlocfilehash: 4cdd269c0acb7568695cbc63b47422ad9b42085b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598762"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Configurer un nom de domaine personnalisé pour votre instance Gestion des API Azure

Lorsque vous créez une instance du service Gestion des API Azure, Azure lui attribue un sous-domaine `azure-api.net` (par exemple, `apim-service-name.azure-api.net`). Toutefois, vous pouvez exposer vos points de terminaison API Management via votre propre nom de domaine personnalisé, par exemple, **`contoso.com`** . Ce didacticiel explique comment mapper un nom DNS personnalisé existant à des points de terminaison exposés par une instance APIM.

> [!IMPORTANT]
> API Management accepte uniquement les demandes avec des valeurs d'[en-tête d’hôte](https://tools.ietf.org/html/rfc2616#section-14.23) correspondant :
>
>* Nom de domaine par défaut
>* N’importe quel nom de domaine personnalisé configuré

> [!WARNING]
> Si vous souhaitez améliorer la sécurité de vos applications avec épinglage de certificat, vous devez utiliser un nom de domaine personnalisé et un certificat que vous gérez, et non pas le certificat par défaut. L’épinglage du certificat par défaut prend une dépendance matérielle sur les propriétés du certificat que vous ne gérez pas, ce qui n’est pas recommandé.

## <a name="prerequisites"></a>Prérequis

-   Un abonnement Azure actif. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
-   Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
-   Un nom de domaine personnalisé qui vous appartient ou à votre organisation. Cette rubrique ne fournit aucune instruction sur l’approvisionnement d’un nom de domaine personnalisé.
-   Un [enregistrement CNAME hébergé sur un serveur DNS](#dns-configuration) qui mappe le nom de domaine personnalisé au nom de domaine par défaut de votre instance API Management. Cette rubrique ne fournit aucune instruction sur l’hébergement d’un enregistrement CNAME.
-   Vous devez disposer d’un certificat valide et d’une clé publique et privée (.PFX). Le sujet ou l’autre nom du sujet (SAN) doit correspondre au nom de domaine. Cela permet à l’instance de Gestion des API d’exposer les URL de manière sécurisée sur TSL.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utiliser le portail Azure pour définir un nom de domaine personnalisé

1. Accédez à votre instance APIM dans le [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Domaines personnalisés**.

    Vous pouvez attribuer un nom de domaine personnalisé à plusieurs points de terminaison. Actuellement, les points de terminaison disponibles sont les suivants :

    | Point de terminaison | Default |
    | -------- | ----------- |
    | **Passerelle** | La valeur par défaut est `<apim-service-name>.azure-api.net`. La passerelle est le seul point de terminaison pouvant être configuré dans le niveau Consommation. |
    | **Portail des développeurs (hérité)** | La valeur par défaut est `<apim-service-name>.portal.azure-api.net`. |
    | **Portail des développeurs** | La valeur par défaut est `<apim-service-name>.developer.azure-api.net`. |
    | **Gestion** | La valeur par défaut est `<apim-service-name>.management.azure-api.net`. |
    | **SCM** | La valeur par défaut est `<apim-service-name>.scm.azure-api.net`. |

    > [!NOTE]
    > Vous pouvez mettre à jour n’importe lequel des points de terminaison. En règle générale, les clients mettent à jour les points de terminaison **Passerelle** (cette URL est utilisée pour appeler l’API exposée via API Management) et **Portail** (URL du portail des développeurs).
    > 
    > Seuls les propriétaires d’instance API Management peuvent utiliser des points de terminaison **Gestion** et **SCM** en interne. Ces points de terminaison sont moins souvent affectés à un nom de domaine personnalisé.
    >
    > Toutefois, les niveaux **Premium** et **Developer** prennent en charge la définition de plusieurs noms d’hôte pour le point de terminaison **Passerelle**.

1. Sélectionnez **+Ajouter**, ou sélectionnez un point de terminaison existant que vous souhaitez mettre à jour.
1. Dans la fenêtre de droite, sélectionnez le **Type** de point de terminaison pour le domaine personnalisé.
1. Dans la zone **Nom d’hôte**, spécifiez le nom que vous souhaitez utiliser. Par exemple : `api.contoso.com`.
1. Sous **certificat**, sélectionnez **Key Vault** ou **Personnalisé**.
    - **Key Vault**
        - Cliquez sur **Sélectionner**.
        - Sélectionnez **Abonnement** dans la liste déroulante.
        - Sélectionnez le **Coffre de clés** dans la liste déroulante.
        - Une fois les certificats chargés, sélectionnez le **certificat** dans la liste déroulante.
        - Cliquez sur **Sélectionner**.
    - **Personnalisée**
        - Sélectionnez le champ **Fichier de certificat** pour sélectionner et télécharger un certificat.
        - Chargez un fichier .PFX valide et fournissez son **Mot de passe** si le certificat est protégé par un mot de passe.
1. Lors de la configuration d’un point de terminaison de passerelle, sélectionnez ou désélectionnez d'[autres options si nécessaire](#clients-calling-with-server-name-indication-sni-header), par exemple **Négocier le certificat client** ou **Liaison SSL par défaut**.
1. Sélectionnez **Mettre à jour**.

    > [!NOTE]
    > Les noms de domaine génériques, `*.contoso.com` par exemple, sont pris en charge à tous les niveaux, à l’exception du niveau Consommation.

    > [!TIP]
    > Nous vous recommandons d’utiliser [Azure Key Vault pour gérer les certificats](../key-vault/certificates/about-certificates.md) et les définir sur `autorenew`.
    >
    > Si vous utilisez Azure Key Vault pour gérer le certificat TLS/SSL de domaine personnalisé, assurez-vous que le certificat est inséré dans Key Vault [en tant que _certificat_](/rest/api/keyvault/createcertificate/createcertificate), pas en tant que _secret_.
    >
    > Pour extraire un certificat TLS/SSL, le service Gestion des API doit disposer des autorisations nécessaires pour répertorier et obtenir les secrets sur l’Azure Key Vault contenant le certificat. 
    >
    >* Lorsque vous utilisez le portail Azure, toutes les étapes nécessaires pour la configuration sont effectuées automatiquement. 
    >* Lorsque vous utilisez les outils de ligne de commande ou API de gestion, ces autorisations doivent être accordées manuellement, en deux étapes :
    >    * Utilisez tout d’abord la page **Identités managées** sur votre instance API Management pour vous assurer que l’identité managée est activée. Notez aussi l’ID du principal qui s’affiche sur cette page. 
    >    * Sur le coffre de clés Azure Key Vault contenant le certificat, fournissez la liste des autorisations à cet ID du principal et accordez-lui les autorisations nécessaires pour obtenir les secrets.
    >
    > Si le certificat est défini sur`autorenew` et que votre niveau API Management comprend un contrat de niveau de service (c’est-à-dire tous les niveaux sauf le niveau Développeur), API Management sélectionnera automatiquement la dernière version sans que le service ne rencontre le moindre temps d’arrêt.

1. Cliquez sur Appliquer.

    > [!NOTE]
    > Le processus d’attribution du certificat peut prendre 15 minutes ou plus, selon la taille du déploiement. La référence SKU de développeur présente des temps d’arrêt, mais pas les références SKU de base ou supérieures.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>Configuration DNS

Lorsque vous configurez DNS pour votre nom de domaine personnalisé, vous avez le choix :

-   Configurer un enregistrement CNAME qui pointe vers le point de terminaison de votre nom de domaine personnalisé configuré, ou
-   Configurer un enregistrement A qui pointe vers l’adresse IP de votre passerelle de gestion des API.

Bien que les enregistrements CNAME (ou enregistrements d’alias) et A permettent d’associer un nom de domaine avec un serveur ou service spécifique, ils fonctionnent différemment. 

### <a name="cname-or-alias-record"></a>Enregistrement CNAME ou d'alias
Un enregistrement CNAME associe un domaine *spécifique*, tel que `contoso.com` ou www\.contoso.com) à un nom de domaine canonique. Une fois créé, l’enregistrement CNAME émet un alias pour le domaine. L’entrée CNAME devient automatiquement l’adresse IP de votre service de domaine personnalisé. Ainsi, même si l’adresse IP change, vous n’avez aucune action à effectuer.

> [!NOTE]
> Certains bureaux d’enregistrement de domaines autorisent le mappage de sous-domaines uniquement lorsqu’un enregistrement CNAME est utilisé (par exemple, www\.contoso.com) et non un nom racine (tel que contoso.com). Pour plus d'informations sur les enregistrements CNAME, consultez la documentation fournie par votre bureau d'enregistrement, la [page Wikipédia sur l'enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record) ou le document [Noms de domaine IETF - Implémentation et spécification](https://tools.ietf.org/html/rfc1035).

### <a name="a-record"></a>Enregistrement A
Un enregistrement A mappe un domaine, tel que `contoso.com` ou **www\.contoso.com**, *ou un nom de domaine générique* comme **\*.contoso.com**, sur une adresse IP. L’enregistrement A étant mappé à une adresse IP statique, les changements d’adresse IP de votre application web ne sont donc pas pris en compte automatiquement. Nous vous recommandons d’utiliser l’enregistrement CNAME plus stable à la place d’un enregistrement A.

> [!NOTE]
> Bien que l’adresse IP de l’instance API Management soit statique, elle peut changer dans quelques scénarios. Lors du choix de la méthode de configuration DNS, nous vous recommandons d’utiliser un enregistrement CNAME lors de la configuration d’un domaine personnalisé, car il est plus stable qu’un enregistrement A en cas de modification de l’adresse IP. Pour plus d’informations, voir l’[article de la documentation IP](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) et le [Forum aux questions sur la Gestion des API](./api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-).

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau votre service et le mettre à l’échelle](upgrade-and-scale.md)