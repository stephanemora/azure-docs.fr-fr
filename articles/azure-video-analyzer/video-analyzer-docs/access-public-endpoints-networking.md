---
title: Points de terminaison publics et réseau
description: Azure Video Analyzer expose un ensemble de points de terminaison réseau publics qui autorisent différents scénarios de produit, notamment la gestion, l’ingestion et la lecture. Cet article explique comment accéder aux points de terminaison publics et aux réseaux.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 4a15893e718f716f4d3858ad6823ab0c3aa0e46c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700485"
---
# <a name="public-endpoints-and-networking"></a>Points de terminaison publics et réseau

Azure Video Analyzer expose un ensemble de points de terminaison réseau publics qui autorisent différents scénarios de produit, notamment la gestion, l’ingestion et la lecture. Cet article décrit ces points de terminaison et fournit des détails sur leur utilisation. Le diagramme ci-dessous illustre ces points de terminaison, en plus de certains points de terminaison clés exposés par les services Azure associés.

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="L’image représente les points de terminaison publics Azure Video Analyzer.":::

## <a name="video-analyzer-endpoints"></a>Points de terminaison Video Analyzer 

Cette section fournit la liste des points de terminaison Video Analyzer.

### <a name="streaming"></a>Diffusion en continu

* **Objet** : expose des données audio, vidéo et d’inférence, qui peuvent être consommées par le [widget du lecteur Video Analyzer](player-widget.md) ou par des lecteurs DASH/HLS compatibles.
* **Authentification et autorisation** : l’autorisation du point de terminaison est appliquée par le biais de jetons émis par le service Video Analyzer. Les jetons sont limités à une seule vidéo, et sont émis implicitement en fonction des règles d’autorisation appliquées sur les API client et de gestion sur la base de chaque vidéo. Le flux d’autorisation est géré automatiquement par le widget du lecteur Video Analyzer.
* **Exigence** : l’accès à cet ensemble de points de terminaison est requis pour la lecture de contenu dans le cloud.

### <a name="client-apis"></a>API client

* **Objet** : expose les métadonnées (titre, description, etc.) de la [ressource vidéo Video Analyzer](terminology.md#video). Cela permet d’afficher des objets vidéo enrichis sur des applications clientes développées par le client. Ces métadonnées sont exploitées par le widget du lecteur Video Analyzer, et peuvent également être exploitées directement par les applications du client.
* **Authentification et autorisation** : l’autorisation de point de terminaison est appliquée par le biais d’une combinaison de [stratégies d’accès](access-policies.md) définies par le client et de jetons JWT émis par le client. Une ou plusieurs stratégies d’accès peuvent être définies par le biais des API de gestion Video Analyzer. Ces stratégies décrivent l’étendue de l’accès et les revendications qui doivent être validées sur les jetons. L’accès est refusé si aucune stratégie d’accès n’est créée dans le compte Video Analyzer.
* **Exigence** : l’accès à ce point de terminaison est requis pour que le widget de lecteur Video Analyzer et les applications clientes similaires développées par le client puissent récupérer les métadonnées vidéo, et pour que la lecture soit autorisée.

### <a name="edge-service-integration"></a>Intégration aux services de périphérie

* **Objet** : 

    * expose les stratégies qui sont téléchargées régulièrement par le module de périphérie Video Analyzer. Ces stratégies contrôlent les comportements de base du module de périphérie, tels que les besoins de facturation et de connectivité.
    * Orchestration de la publication vidéo dans le cloud, notamment la récupération des URL SAS de stockage Azure qui permet au module de périphérie Video Analyzer d’enregistrer des données vidéo dans le compte de stockage du client.
* **Authentification et autorisation** : l’authentification initiale s’effectue par le biais d’un jeton de provisionnement de courte durée émis par les API de gestion Video Analyzer. Une fois la liaison initiale établie, le module et le service échangent un ensemble de clés d’autorisation à rotation automatique qui sont utilisées à compter de ce moment-là.
* **Exigence** : l’accès à ce point de terminaison est requis pour le bon fonctionnement du module de périphérie Video Analyzer. Le module de périphérie cessera de fonctionner si ce point de terminaison est inaccessible au cours d’une période de 36 heures.

## <a name="telemetry"></a>Télémétrie :

* **Objet** : soumission périodique facultative de données de télémétrie qui permet à Microsoft de mieux comprendre comment le module de périphérie Video Analyzer est utilisé, et identifie de manière proactive les futures améliorations qui peuvent être apportées en matière de compatibilité, de performances et à d’autres zones du produit.
* **Authentification et autorisation** : l’autorisation est basée sur une clé préétablie.
* **Exigence** : l’accès à ce point de terminaison est facultatif et n’interfère pas avec la fonctionnalité du produit. La collecte et l’envoi de données peuvent être désactivés par le biais des propriétés de jumeau de module.

## <a name="associated-azure-endpoints"></a>Points de terminaison Azure associés 

> [!NOTE]
> La liste des points de terminaison décrits dans cet article n’est pas une liste exhaustive des points de terminaison de service associés. Il s’agit d’une liste informative des points de terminaison requis pour le fonctionnement normal de Video Analyzer. Pour obtenir la liste complète des points de terminaison exposés par chaque service respectif, reportez-vous à la documentation de chaque service Azure.

## <a name="next-steps"></a>Étapes suivantes

[Stratégies d’accès](access-policies.md) 
