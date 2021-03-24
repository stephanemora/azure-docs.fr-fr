---
title: Différences par rapport à la version d’origine
titleSuffix: Azure Digital Twins
description: Découvrez ce qui a changé dans la nouvelle version d’Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072929"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Qu’est-ce que la nouvelle version Azure Digital Twins ? En quoi est-elle différente de la version originale (2018) ?

La première préversion publique d’Azure Digital Twins a été publiée en octobre 2018. Bien que les principaux concepts de cette version originale soient toujours valables dans le service actuel, la plupart des interfaces et détails d’implémentation ont été modifiés et le service a ainsi gagné en flexibilité et en accessibilité. Ces modifications ont été motivées par les commentaires envoyés par les clients.

> [!IMPORTANT]
> En raison des capacités étendues du nouveau service, le service Azure Digital Twins original a été supprimé. Depuis janvier 2021, ses API et ses données associées ne sont plus disponibles.

Si vous avez utilisé la première version d’Azure Digital Twins au cours de la première préversion publique, utilisez les informations et les bonnes pratiques de cet article pour savoir comment utiliser le service actuel et tirer parti de ses fonctionnalités.

## <a name="differences-by-topic"></a>Différences par rubrique

Le graphique ci-dessous fournit une vue côte à côte des concepts qui ont changé entre la version originale du service et le service actuel.

| Rubrique | Dans la version originale | Dans la version actuelle |
| --- | --- | --- | --- |
| **Modélisation**<br>*Flexibilité accrue* | La version originale était conçue autour des espaces intelligents et était donc fournie avec un vocabulaire intégré pour les bâtiments. | La version actuelle d’Azure Digital Twins n’est pas réservée à un domaine spécifique. Vous pouvez définir votre propre vocabulaire personnalisé et vos propres modèles personnalisés pour votre solution, afin de représenter plus de types d’environnements de manière plus flexible.<br><br>Pour plus d’informations, consultez [*Concepts : Modèles personnalisés*](concepts-models.md). |
| **Topologie**<br>*Flexibilité accrue*| La version originale prenait en charge une structure de données sous forme d’arborescence, adaptée aux espaces intelligents. Les jumeaux numériques étaient connectés avec des relations hiérarchiques. | Avec la version actuelle, votre jumeau numérique peut être connecté à des topologies de graphe arbitraires, organisées comme vous le souhaitez. Vous bénéficiez ainsi d’une plus grande flexibilité pour exprimer les relations complexes du monde réel.<br><br>Pour plus d’informations, consultez [*Concepts : Jumeaux numériques et graphe de jumeaux*](concepts-twins-graph.md). |
| **Calcul**<br>*Plus riche et plus flexible* | Dans la version originale, la logique de traitement des événements et de télémétrie était définie dans des fonctions JavaScript définies par l’utilisateur. Le débogage avec les fonctions définies par l’utilisateur était limité. | La version actuelle a un modèle de calcul ouvert : vous fournissez une logique personnalisée en attachant des ressources de calcul externes comme [Azure Functions](../azure-functions/functions-overview.md). Cela vous permet d’utiliser le langage de programmation de votre choix, d’accéder à des bibliothèques de code personnalisées sans restriction et de tirer parti des éventuelles ressources de développement et de débogage du service externe.<br><br>Pour plus d’informations, consultez [*Guide pratique : Configurer une fonction Azure pour le traitement des données*](how-to-create-azure-function.md). |
| **Gestion des appareils avec IoT Hub**<br>*Accessibilité accrue* | La version originale gérait les appareils avec une instance [IoT Hub](../iot-hub/about-iot-hub.md) qui était interne au service Azure Digital Twins. Ce hub intégré n’était pas entièrement accessible aux développeurs. | Dans la version actuelle, vous « apportez votre propre IoT Hub » en attachant une instance IoT Hub créée de manière indépendante (ainsi que les appareils qu’elle gère déjà). Cela vous donne un accès complet aux fonctionnalités IoT Hub et vous permet de contrôler la gestion des appareils.<br><br>Pour plus d’informations, consultez [*Guide pratique : Ingérer la télémétrie depuis IoT Hub*](how-to-ingest-iot-hub-data.md). |
| **Sécurité**<br>*Plus standard* | La version originale comportait des rôles prédéfinis que vous pouviez utiliser pour gérer l’accès à votre instance. | La version actuelle s’intègre avec le même service principal de [Contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/overview.md) que celui qu’utilisent les autres services Azure. Cela simplifie l’authentification entre les autres services Azure de votre solution, comme IoT Hub, Azure Functions, Event Grid et d’autres.<br>Avec le contrôle d’accès en fonction du rôle (RBAC), vous pouvez utiliser des rôles prédéfinis ou créer et configurer des rôles personnalisés.<br><br>Pour plus d’informations, consultez [*Concepts : Sécurité pour les solutions Azure Digital Twins*](concepts-security.md). |
| **Extensibilité**<br>*Une échelle plus grande* | La version originale avait des limitations d’échelle pour les appareils, les messages, les graphes et les unités d’échelle. Une seule instance d’Azure Digital Twins était prise en charge par abonnement.  | La version actuelle s’appuie sur une nouvelle architecture avec une scalabilité améliorée et offre une plus grande puissance de calcul. Elle prend également en charge 10 instances par région et par abonnement.<br><br>Consultez [*Limites du service Azure Digital Twins*](reference-service-limits.md) pour plus d’informations sur les limites de la version actuelle. |

## <a name="service-limits"></a>Limites du service

Pour obtenir la liste des limites d’Azure Digital Twins, consultez [*Limites du service Azure Digital Twins*](reference-service-limits.md).

## <a name="next-steps"></a>Étapes suivantes

* Découvrez en détail comment utiliser la version actuelle dans le démarrage rapide suivant : [*Démarrage rapide : Explorer un exemple de scénario*](quickstart-adt-explorer.md).

* Ou découvrez les concepts clés en lisant [*Concepts : Modèles personnalisés*](concepts-models.md).