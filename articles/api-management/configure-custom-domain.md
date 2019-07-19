---
title: Configurer un nom de domaine personnalisé pour une instance de gestion des API Azure | Microsoft Docs
description: Cette rubrique explique comment configurer un nom de domaine personnalisé pour votre instance de gestion des API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 07/01/2019
ms.author: apimpm
ms.openlocfilehash: 59b44dcc9ec3a1f7c274f426a19aa8ed2258db3e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509299"
---
# <a name="configure-a-custom-domain-name"></a>Configuration d’un nom de domaine personnalisé

Lorsque vous créez une instance de service APIM Azure, Azure l’attribue à un sous-domaine d’azure-api.net (par exemple, `apim-service-name.azure-api.net`). Toutefois, vous pouvez exposer vos points de terminaison APIM via votre propre nom de domaine personnalisé, par exemple, **contoso.com**. Ce didacticiel explique comment mapper un nom DNS personnalisé existant à des points de terminaison exposés par une instance APIM.

> [!WARNING]
> Les clients qui souhaitent utiliser un épinglage de certificat pour améliorer la sécurité de leurs applications doivent utiliser un nom de domaine personnalisé > et le certificat qu’ils gèrent, pas le certificat par défaut. Les clients qui épinglent le certificat par défaut à la place > prendront une dépendance dure sur les propriétés du certificat qu’ils ne contrôlent pas, ce qui n’est pas une pratique recommandée.

## <a name="prerequisites"></a>Prérequis

Pour effectuer les étapes décrites dans cet article, vous devez disposer des éléments suivants :

-   Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Une instance APIM. Pour en savoir plus, voir [Créer une instance de gestion des API Azure](get-started-create-service-instance.md).
-   Un nom de domaine personnalisé qui vous appartient. Vous devez vous procurer séparément le nom de domaine personnalisé que vous souhaitez utiliser. De plus, il doit être hébergé sur un serveur DNS. Cette rubrique ne donne aucune instruction sur l’hébergement d’un nom de domaine personnalisé.
-   Vous devez disposer d’un certificat valide et d’une clé publique et privée (.PFX). L’objet ou l’autre nom de l’objet (SAN) doit correspondre au nom du domaine. Cela permet à l’instance APIM d’exposer des URL de manière sécurisée, via SSL.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Utiliser le portail Azure pour définir un nom de domaine personnalisé

1. Accédez à votre instance APIM dans le [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Domaines personnalisés et SSL**.

    Vous pouvez attribuer un nom de domaine personnalisé à plusieurs points de terminaison. Actuellement, les points de terminaison disponibles sont les suivants :

    - **Proxy** (valeur par défaut : `<apim-service-name>.azure-api.net`)
    - **Portail** (valeur par défaut : `<apim-service-name>.portal.azure-api.net`)
    - **Gestion** (valeur par défaut : `<apim-service-name>.management.azure-api.net`)
    - **SCM** (valeur par défaut : `<apim-service-name>.scm.azure-api.net`)

    > [!NOTE]
    > Vous pouvez mettre à jour tous les points de terminaison ou certains d’entre eux. En règle générale, les clients mettent à jour les points de terminaison **Proxy** (cette URL est utilisée pour appeler l’API exposée via la gestion des API) et **Portal** (URL du portail des développeurs). Les points de terminaison **Gestion** et **SCM** sont utilisés en interne par les propriétaires d’instance APIM uniquement. Pour cette raison, ils se voient moins fréquemment attribuer un nom de domaine personnalisé. Dans la plupart des cas, vous pouvez définir un seul nom de domaine personnalisé pour un point de terminaison donné. Toutefois, le niveau **Premium** prend en charge la définition de plusieurs noms d’hôte pour le point de terminaison **Proxy**.

1. Sélectionnez le point de terminaison que vous souhaitez mettre à jour.
1. Dans la fenêtre de droite, cliquez sur **Personnalisé**.

    - Dans la zone **Nom de domaine personnalisé**, spécifiez le nom que vous souhaitez utiliser. Par exemple : `api.contoso.com`. Les noms de domaine avec des caractères génériques (par exemple, \*.domaine.com) sont également pris en charge.
    - Dans le **certificat**, sélectionnez un certificat de Key Vault. Vous pouvez également charger un fichier .PFX valide et fournir son **mot de passe** si le certificat est protégé par un mot de passe.

    > [!TIP]
    > Nous vous recommandons d’utiliser Azure Key Vault pour gérer les certificats et les définir sur Rotation automatique.
    > Si vous utilisez Azure Key Vault pour gérer le certificat SSL de domaine personnalisé, assurez-vous que le certificat est inséré dans Key Vault [comme un _certificat_](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate), et non un _secret_.
    >
    > Pour extraire un certificat SSL, APIM doit disposer de la liste et des autorisations nécessaires pour obtenir les secrets sur le coffre de clés Azure Key Vault contenant le certificat. Lorsque vous utilisez le portail Azure, toutes les étapes nécessaires pour la configuration sont effectuées automatiquement. Lorsque vous utilisez les outils de ligne de commande ou APIM, ces autorisations doivent être accordées manuellement. Cette opération comprend deux étapes. Utilisez tout d’abord la page Identités managées sur votre instance APIM pour vous assurer que l’identité managée est activée. Notez aussi l’ID du principal qui s’affiche sur cette page. Ensuite, sur le coffre de clés Azure Key Vault contenant le certificat, fournissez la liste des autorisations à cet ID du principal et accordez-lui les autorisations nécessaires pour obtenir les secrets.
    >
    > Si le certificat est défini sur Rotation automatique, APIM utilisera automatiquement la dernière version sans que le service ne rencontre le moindre temps d’arrêt (si votre niveau d’APIM comprend un contrat de niveau de service, c’est-à-dire tous les niveaux sauf le niveau Développeur).

1. Cliquez sur Appliquer.

    > [!NOTE]
    > Le processus d’attribution du certificat peut prendre 15 minutes ou plus, selon la taille du déploiement. La référence SKU de développeur présente des temps d’arrêt, mais pas les références SKU de base ou supérieures.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>Étapes suivantes

[Mettre à niveau votre service et le mettre à l’échelle](upgrade-and-scale.md)
