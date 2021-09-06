---
title: Configurer un point de terminaison privé dans Azure Static Web Apps
description: Découvrez comment configurer l’accès de point de terminaison privé pour Azure Static Web Apps
services: static-web-apps
author: burkeholland
ms.author: buhollan
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 7/28/2021
ms.openlocfilehash: 8d6f5e019852200068d4db342ef4ab533d8779b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531829"
---
# <a name="configure-private-endpoint-in-azure-static-web-apps"></a>Configurer un point de terminaison privé dans Azure Static Web Apps

Vous pouvez utiliser un point de terminaison privé (également appelé liaison privée) pour restreindre l’accès à votre application web statique afin qu’elle ne soit accessible qu’à partir de votre réseau privé.

> [!NOTE]
> La prise en charge des points de terminaison privés dans les Static Web Apps est actuellement en préversion.

## <a name="how-it-works"></a>Fonctionnement

Un réseau virtuel (VNet) Azure est un réseau similaire à ce que vous pouvez retrouver dans un centre de données traditionnel, mais les ressources du réseau virtuel communiquent les unes avec les autres en toute sécurité sur le réseau principal de Microsoft.

La configuration de Static Web Apps avec un point de terminaison privé vous permet d’utiliser une adresse IP privée de votre réseau virtuel. Une fois ce lien créé, votre application web statique est intégrée à votre réseau virtuel. Par conséquent, votre application web statique n’est plus disponible pour l’Internet public et n’est accessible qu’à partir des machines dans votre réseau virtuel Azure.

> [!NOTE]
> Le fait de placer votre application derrière un point de terminaison privé signifie que votre application est disponible uniquement dans la région où se trouve votre réseau virtuel. Par conséquent, votre application n’est plus disponible sur plusieurs points de présence.

> [!WARNING]
> Actuellement, les points de terminaison privés sécurisent uniquement votre environnement de production. La prise en charge des environnements intermédiaires sera ajoutée dans une prochaine mise à jour du service.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif.
  - [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un [réseau virtuel Azure](../virtual-network/quick-create-portal.md).
- Une application déployée avec [Azure Static Web Apps](./get-started-portal.md) qui utilise le plan d'hébergement Standard.

## <a name="create-a-private-endpoint"></a>Créer un Private Endpoint

Dans cette section, vous créez un point de terminaison privé pour votre application web statique.

> [!IMPORTANT]
> Votre application web statique doit être déployée sur le plan d’hébergement Standard pour utiliser des points de terminaison privés. Vous pouvez changer le plan d'hébergement dans l’option **Plan d'hébergement** du menu latéral.

1. Dans le portail, ouvrez votre application web statique.

1. Sélectionnez l’option **Points de terminaison privés** dans le menu latéral.

1. Cliquez sur le bouton **Add** .

1. Dans la boîte de dialogue « Ajouter un point de terminaison privé », entrez les informations suivantes :

   | Paramètre                         | Valeur                         |
   | ------------------------------- | ----------------------------- |
   | Nom                            | Entrez **myPrivateEndpoint**.  |
   | Abonnement                    | Sélectionnez votre abonnement.     |
   | Réseau virtuel                 | Sélectionnez votre réseau virtuel.  |
   | Subnet                          | Sélectionnez votre sous-réseau.           |
   | Intégrer à une zone DNS privée | Conservez la valeur par défaut **Oui**. |

   :::image type="content" source="media/create-private-link-dialog.png" alt-text="./media/create-private-link-dialog.png":::

1. Sélectionnez **OK**.

## <a name="testing-your-private-endpoint"></a>Test de votre point de terminaison privé

Étant donné que votre application n’est plus disponible publiquement, le seul moyen d’y accéder est depuis l’intérieur de votre réseau virtuel. Pour tester, configurez une machine virtuelle dans votre réseau virtuel et accédez à votre site.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les points de terminaison privés](../private-link/private-endpoint-overview.md)
