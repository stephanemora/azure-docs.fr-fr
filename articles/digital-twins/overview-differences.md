---
title: Différences avec la version précédente
titleSuffix: Azure Digital Twins
description: Découvrez ce qui a changé dans la nouvelle version d’Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 8a69e0bfe172d2cc7a291ee51ffa73ae7b484731
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613293"
---
# <a name="how-is-the-new-azure-digital-twins-different-from-the-previous-version-2018"></a>En quoi la nouvelle version d’Azure Digital Twins est-elle différente de la version précédente (2018) ?

La première préversion publique d’Azure Digital Twins a été publiée en octobre 2018. Alors que les principaux concepts de cette version précédente ont été transférés dans le nouveau service en préversion publique, la plupart des interfaces et détails d’implémentation ont été modifiés et le service a ainsi gagné en flexibilité et en accessibilité. Ces modifications ont été motivées par les commentaires envoyés par les clients.

> [!IMPORTANT]
> En raison des capacités étendues du nouveau service, le précédent service Azure Digital Twins sera supprimé à la fin de l’année 2020.

Si vous avez utilisé la première version d’Azure Digital Twins au cours de la préversion publique précédente, utilisez les informations et les meilleures pratiques de cet article pour savoir comment utiliser le nouveau service et tirer parti de ses fonctionnalités.

## <a name="differences-by-topic"></a>Différences par rubrique

Le graphique ci-dessous fournit une vue côte à côte des concepts qui ont changé entre la version précédente du service et le nouveau service (actuel).

| Rubrique | Version précédente | Nouvelle version |
| --- | --- | --- | --- |
| **Modélisation**<br>*Flexibilité accrue* | La version précédente était conçue autour des espaces intelligents et était donc fournie avec un vocabulaire intégré pour les bâtiments. | La nouvelle version d’Azure Digital Twins n’est pas réservée à un domaine spécifique. Vous pouvez définir votre propre vocabulaire personnalisé et vos propres modèles personnalisés pour votre solution, afin de représenter plus de types d’environnements de manière plus flexible.<br><br>Pour en savoir plus : [Concepts : Modèles personnalisés](concepts-models.md). |
| **Topologie**<br>*Flexibilité accrue*| La version précédente prenait en charge une structure de données sous forme d’arborescence, adaptée aux espaces intelligents. Les jumeaux numériques étaient connectés avec des relations hiérarchiques. | Avec la nouvelle version, votre jumeau numérique peut être connecté à des topologies de graphe arbitraires, organisées comme vous le souhaitez. Vous bénéficiez ainsi d’une plus grande flexibilité pour exprimer les relations complexes du monde réel.<br><br>Pour en savoir plus : [Concepts : Jumeaux numériques et graphe de jumeaux](concepts-twins-graph.md). |
| **Calcul**<br>*Plus riche et plus flexible* | Dans la version précédente, la logique de traitement des événements et de télémétrie était définie dans des fonctions JavaScript définies par l’utilisateur. Le débogage avec les fonctions définies par l’utilisateur était limité. | La nouvelle version a un modèle de calcul ouvert : vous fournissez une logique personnalisée en attachant des ressources de calcul externes comme [Azure Functions](../azure-functions/functions-overview.md). Cela vous permet d’utiliser le langage de programmation de votre choix, d’accéder à des bibliothèques de code personnalisées sans restriction et de tirer parti des éventuelles ressources de développement et de débogage du service externe.<br><br>En savoir plus :[Guide pratique : Configurer une fonction Azure pour le traitement des données](how-to-create-azure-function.md). |
| **Gestion des appareils avec IoT Hub**<br>*Accessibilité accrue* | La version précédente gérait les appareils avec une instance [IoT Hub](../iot-hub/about-iot-hub.md) qui était interne au service Azure Digital Twins. Ce hub intégré n’était pas entièrement accessible aux développeurs. | Dans la nouvelle version, vous « apportez votre propre IoT Hub » en attachant une instance IoT Hub créée de manière indépendante (ainsi que les appareils qu’elle gère déjà). Cela vous donne un accès complet aux fonctionnalités IoT Hub et vous permet de contrôler la gestion des appareils.<br><br>En savoir plus :[Guide pratique : Ingérer la télémétrie depuis IoT Hub](how-to-ingest-iot-hub-data.md). |
| **Sécurité**<br>*Plus standard* | La version précédente comportait des rôles prédéfinis que vous pouviez utiliser pour gérer l’accès à votre instance. | La nouvelle version s’intègre au même service backend de [contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/overview.md) que celui des autres services Azure. Cela simplifie l’authentification entre les autres services Azure de votre solution, comme IoT Hub, Azure Functions, Event Grid et d’autres.<br>Avec le contrôle d’accès en fonction du rôle (RBAC), vous pouvez utiliser des rôles prédéfinis ou créer et configurer des rôles personnalisés.<br><br>Pour en savoir plus : [Concepts : Sécurité pour les solutions Azure Digital Twins](concepts-security.md). |
| **Extensibilité**<br>*Une échelle plus grande* | La version précédente avait des limitations d’échelle pour les appareils, les messages, les graphes et les unités d’échelle. Une seule instance d’Azure Digital Twins était prise en charge par abonnement.  | La nouvelle version s’appuie sur une nouvelle architecture avec une scalabilité améliorée et offre une plus grande puissance de calcul. Elle prend également en charge 10 instances par région et par abonnement.<br><br>Consultez [Référence : Limites du service en préversion publique](reference-service-limits.md) pour plus d’informations sur les limites de la préversion publique. |

## <a name="service-limits-in-public-preview"></a>Limites du service en préversion publique

Pour obtenir la liste des limites Azure Digital Twins pendant cette préversion publique, consultez [Référence : Limites de service de la préversion publique](reference-service-limits.md).

## <a name="next-steps"></a>Étapes suivantes

Apprenez à utiliser Azure Digital Twins en suivant le premier tutoriel :

> [!div class="nextstepaction"]
> [Tutoriel : Coder une application cliente](tutorial-code.md)