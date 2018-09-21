---
title: Questions fréquentes (FAQ) sur les accélérateurs de solution Azure IoT | Microsoft Docs
description: Questions fréquentes (FAQ) sur les accélérateurs de solution IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 66494ca6bd9b4d8276469e85f4c6bb435ae245f7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716398"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Questions fréquentes (FAQ) sur les accélérateurs de solution IoT

Consultez également les [questions fréquentes sur la solution préconfigurée Usine connectée](iot-accelerators-faq-cf.md) et les [questions fréquentes sur la surveillance à distance](iot-accelerators-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Où trouver le code source des accélérateurs de solution ?

Le code source est stocké dans les référentiels GitHub suivants :

* [Accélérateur de solution de surveillance à distance (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Accélérateur de solution de surveillance à distance (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Accélérateur de solution de maintenance prédictive](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Accélérateur de solution Usine connectée](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quels SDK puis-je utiliser afin de développer des clients d’appareils pour les accélérateurs de solution ?

Vous trouverez des liens vers les kits de développement logiciel des appareils IoT en différentes langues (C, .NET, Java, Node.js, Python) dans les référentiels GitHub [Kits de développement logiciel Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks).

Si vous utilisez le périphérique DevKit, vous pouvez trouver des ressources et des exemples dans le référentiel GitHub [Kit de développement logiciel IoT DevKit](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>La nouvelle architecture de microservices est-elle disponible pour les trois accélérateurs de solution ?

Actuellement, la solution de surveillance à distance utilise l’architecture de microservices, car elle couvre le scénario le plus large.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quels sont les avantages de la nouvelle architecture basée sur des microservices open source dans la nouvelle mise à jour ?

Ces deux dernières années, l’architecture cloud a considérablement évolué. Les microservices ont émergé comme un modèle efficace pour obtenir mise à l’échelle et flexibilité sans sacrifier la vitesse de développement. Plusieurs services de Microsoft utilisent ce modèle d’architecture en interne et obtiennent de très bons résultats en termes d’extensibilité et de fiabilité. Nous mettons ces apprentissages en pratique pour que nos clients puissent en bénéficier.

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Le nouvel accélérateur de solution est-il disponible dans la même zone géographique que la solution existante ?

Oui, la nouvelle solution de surveillance à distance est disponible dans les mêmes zones géographiques.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsuitecom"></a>Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et un clic sur l’option Supprimer d’un accélérateur de solution sur azureiotsuite.com ?

* Si vous supprimez l’accélérateur de solution sur [azureiotsuite.com](https://www.azureiotsolutions.com/), vous supprimez toutes les ressources qui ont été provisionnées lors de la création de l’accélérateur. Si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées.
* Si vous supprimez le groupe de ressources sur le [portail Azure](https://portal.azure.com), vous supprimez uniquement les ressources de ce groupe de ressources. Vous devez également supprimer l’application Azure Active Directory associée à l’accélérateur de solution.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Puis-je continuer à tirer parti de mes investissements existants dans les accélérateurs de solution Azure IoT ?

Oui. Toute solution qui existe aujourd'hui continue à fonctionner dans votre abonnement Azure et le code source reste disponible dans GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’IoT Hub puis-je configurer dans un abonnement ?

Par défaut, vous pouvez approvisionner [10 instances IoT Hub par abonnement](../azure-subscription-service-limits.md#iot-hub-limits). Vous pouvez créer un [ticket de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter cette limite. Par conséquent, étant donné que chaque accélérateur de solution provisionne un nouvel IoT Hub, vous ne pouvez configurer que 10 accélérateurs de solution au maximum dans un abonnement.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’Azure Cosmos DB puis-je configurer dans un abonnement ?

Cinquante. Vous pouvez créer un [ticket de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que 50 instances de Cosmos DB par abonnement.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux cartes Bing - Transactions internes - Niveau 1 pour les plans d’entreprise dans un abonnement Azure. La solution de surveillance à distance est provisionnée par défaut avec le plan Transactions internes - Niveau 1. Par conséquent, vous pouvez provisionner au plus deux solutions de surveillance à distance dans un abonnement sans modification.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Puis-je créer un accélérateur de solution si je dispose de Microsoft Azure pour DreamSpark ?

> [!NOTE]
> Microsoft Azure pour DreamSpark s’appelle désormais Microsoft Imagine pour les étudiants.

Il n’est pas possible de créer un accélérateur de solution avec un compte [Microsoft Azure pour DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Vous pouvez toutefois créer en quelques minutes un [compte d’essai gratuit pour Azure](https://azure.microsoft.com/free/), que vous pouvez utiliser pour créer un accélérateur de solution.

### <a name="how-do-i-delete-an-aad-tenant"></a>Comment supprimer un client AAS ?

Consultez le billet de blog d’Eric Golpe, [Procédure pas à pas pour la suppression d’un client Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités des accélérateurs de solution IoT :

* [Explorer les fonctionnalités de l’accélérateur de solution de surveillance à distance](quickstart-remote-monitoring-deploy.md)
* [Présentation de l’accélérateur de solution de maintenance prédictive](iot-accelerators-predictive-overview.md)
* [Déployer l’accélérateur de solution Usine connectée](quickstart-connected-factory-deploy.md)
* [Sécurisation de l’Internet des objets de bout en bout](/azure/iot-fundamentals/iot-security-ground-up)
