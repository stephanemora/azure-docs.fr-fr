---
title: Vider le cache dans Azure Front Door Standard/Premium (préversion)
description: Cet article vous aide à comprendre comment vider le cache dans Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101097677"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Vider le cache dans Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door Standard/Premium met en cache les ressources tant que leur durée de vie (TTL, time-to-live) n’est pas arrivée à expiration. Chaque fois qu’un client demande une ressource dont la durée de vie a expiré, l’environnement Azure Front Door en récupère une nouvelle copie mise à jour pour servir la demande, puis stocke le cache actualisé.

En guise de bonne pratique, veillez à ce que vos utilisateurs obtiennent toujours la dernière copie de vos ressources. Pour cela, versionnez vos ressources à chaque mise à jour et publiez-les avec de nouvelles URL. Azure Front Door Standard/Premium récupère immédiatement les nouvelles ressources pour les demandes de client suivantes. Il est parfois souhaitable de vider le contenu mis en cache sur tous les nœuds de périphérie et de tous les forcer à récupérer de nouvelles ressources mises à jour. Le vidage du contenu mis en cache peut en effet s’avérer utile si vous avez procédé à de nouvelles mises à jour de votre application ou si vous souhaitez mettre à jour des ressources qui contiennent des informations incorrectes.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

Consultez [Azure Front Door : mise en cache](concept-caching.md) pour comprendre le fonctionnement de la mise en cache.

## <a name="configure-cache-purge"></a>Configurer le vidage du cache

1. Accédez à la page Vue d’ensemble du profil Azure Front Door avec les ressources que vous souhaitez supprimer du cache, puis sélectionnez **Vider le cache**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Capture d’écran de la page Vue d’ensemble avec l’option de vidage du cache.":::

1. Sélectionnez le point de terminaison et le domaine que vous souhaitez supprimer des nœuds de périphérie. *(Vous pouvez sélectionner plusieurs domaines.)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Capture d’écran de la page de vidage du cache.":::

1. Pour effacer toutes les ressources, sélectionnez **vider toutes les ressources pour les domaines sélectionnés**. Sinon, sous **Chemins**, entrez le chemin de chaque ressource que vous souhaitez supprimer du cache.

Ces formats sont pris en charge dans les listes de chemins d’accès à vider :

* **Vidage à chemin unique** : Supprimez des ressources individuelles en spécifiant leur chemin complet (sans le protocole ni le domaine) avec l’extension de fichier, par exemple /pictures/strasbourg.png.
* **Vidage du domaine racine** : Videz la racine du point de terminaison avec « /* » dans le chemin.

Les vidages du cache dans Azure Front Door Standard/Premium ne tiennent pas compte de la casse. Par ailleurs, ils ne prennent pas en compte les chaînes de requête, ce qui signifie que le vidage d’une URL n’a pas pour effet de vider toutes ses variantes constituées de chaînes de requête. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
