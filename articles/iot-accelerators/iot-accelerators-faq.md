---
title: Questions fréquentes (FAQ) sur les accélérateurs de solution IoT - Azure | Microsoft Docs
description: Cet article répond aux questions fréquentes sur les accélérateurs de solution IoT. Il comporte les liens des référentiels GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 40c1fb314a2dea653740e7f87b5caa14c759fffb
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078219"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Questions fréquentes (FAQ) sur les accélérateurs de solution IoT

Consultez également les [questions fréquentes sur la solution préconfigurée Usine connectée](iot-accelerators-faq-cf.md) et les [questions fréquentes sur la surveillance à distance](iot-accelerators-faq-rm-v2.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Où trouver le code source des accélérateurs de solution ?

Le code source est stocké dans les référentiels GitHub suivants :

* [Accélérateur de solution de supervision à distance (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Accélérateur de solution de supervision à distance (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Accélérateur de solution de maintenance prédictive](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Accélérateur de solution Usine connectée](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quels SDK puis-je utiliser afin de développer des clients d’appareils pour les accélérateurs de solution ?

Vous trouverez des liens vers les kits de développement logiciel des appareils IoT en différentes langues (C, .NET, Java, Node.js, Python) dans les référentiels GitHub [Kits de développement logiciel Microsoft Azure IoT](https://github.com/Azure/azure-iot-sdks).

Si vous utilisez l’appareil DevKit, vous pouvez trouver des ressources et des exemples dans le dépôt GitHub [SDK IoT DevKit](https://github.com/Microsoft/devkit-sdk).

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>La nouvelle architecture de microservices est-elle disponible pour les trois accélérateurs de solution ?

Actuellement, la solution de supervision à distance utilise l’architecture de microservices, car elle couvre le scénario le plus large.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Quels sont les avantages de la nouvelle architecture basée sur des microservices open source dans la nouvelle mise à jour ?

Ces deux dernières années, l’architecture cloud a considérablement évolué. Les microservices ont émergé comme un modèle efficace pour obtenir mise à l’échelle et flexibilité sans sacrifier la vitesse de développement. Plusieurs services de Microsoft utilisent ce modèle d’architecture en interne et obtiennent de très bons résultats en termes d’extensibilité et de fiabilité. Microsoft met ces apprentissages en pratique dans les accélérateurs de solution afin que les clients puissent en bénéficier.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Je suis administrateur de service et je souhaite changer le mappage d’annuaire entre mon abonnement et un locataire Azure AD spécifique. Comment mener à bien cette tâche ?

Voir [Comment ajouter un abonnement existant à votre répertoire Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Je souhaite modifier la fonctionnalité administrateur de service ou coadministrateur lors d’une connexion avec un compte de société

Consultez l’article de support [Changement de l’administrateur et du co-administrateur de services lors d’une connexion avec un compte professionnel](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Pourquoi est-ce que je reçois cette erreur ? « Votre compte n’a pas les autorisations suffisantes pour créer une solution. Veuillez contacter votre administrateur de compte ou essayer avec un autre compte. »

Examinez le schéma suivant pour obtenir des conseils :

![Organigramme des autorisations](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Si l’erreur persiste après votre validation en tant qu’administrateur global du locataire Azure AD et que co-administrateur de l’abonnement, demandez à votre administrateur de compte de supprimer l’utilisateur et de réattribuer les autorisations nécessaires dans l’ordre suivant : tout d’abord, ajoutez l’utilisateur en tant qu’administrateur global, puis ajoutez un utilisateur en tant que coadministrateur sur l’abonnement Azure. Si les problèmes persistent, contactez le service [Aide et support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Pourquoi affiche-t-il cette erreur alors que j’ai un abonnement Azure ? « Vous devez avoir un abonnement Azure pour créer des solutions préconfigurées. Vous pouvez créer un compte d'essai gratuit en quelques minutes seulement. »

Si vous êtes sûr de disposer d’un abonnement Azure, validez le mappage de locataire pour votre abonnement et vérifiez que c’est le locataire correct qui est sélectionné dans la liste déroulante. Si vous avez validé que le locataire est correct, suivez le schéma précédent et validez le mappage de votre abonnement et ce locataire Azure AD.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Où puis-je trouver des informations sur la version précédente de la solution de supervision à distance ?

La version précédente de l’accélérateur de solution de supervision à distance était la solution préconfigurée de supervision à distante IoT Suite. Vous trouverez la documentation archivée à l’emplacement [https://docs.microsoft.com/previous-versions/azure/iot-suite/](/previous-versions/azure/iot-suite/).

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Le nouvel accélérateur de solution est-il disponible dans la même zone géographique que la solution existante ?

Oui, la nouvelle solution de surveillance à distance est disponible dans les mêmes zones géographiques.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Quelle est la différence entre supprimer un groupe de ressources dans le portail Azure et cliquer sur Supprimer pour un accélérateur de solution dans azureiotsolutions.com ?

* Si vous supprimez l’accélérateur de solution dans [azureiotsolutions.com](https://www.azureiotsolutions.com/), vous supprimez toutes les ressources qui ont été déployées lors de la création de cet accélérateur. Si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées.
* Si vous supprimez le groupe de ressources sur le [portail Azure](https://portal.azure.com), vous supprimez uniquement les ressources de ce groupe de ressources. Vous devez également supprimer l’application Azure Active Directory associée à l’accélérateur de solution.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Puis-je continuer à tirer parti de mes investissements existants dans les accélérateurs de solution Azure IoT ?

Oui. Toute solution qui existe aujourd’hui continue à fonctionner dans votre abonnement Azure et le code source reste disponible dans GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’IoT Hub puis-je configurer dans un abonnement ?

Par défaut, vous pouvez approvisionner [10 instances IoT Hub par abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Vous pouvez créer un [ticket de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter cette limite. Par conséquent, étant donné que chaque accélérateur de solution provisionne un nouvel IoT Hub, vous ne pouvez configurer que 10 accélérateurs de solution au maximum dans un abonnement.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’Azure Cosmos DB puis-je configurer dans un abonnement ?

Cinquante. Vous pouvez créer un [ticket de support Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que 50 instances de Cosmos DB par abonnement.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux cartes Bing - Transactions internes - Niveau 1 pour les plans d’entreprise dans un abonnement Azure. La solution de supervision à distance est provisionnée par défaut avec le plan Transactions internes - Niveau 1. Par conséquent, vous pouvez provisionner au plus deux solutions de surveillance à distance dans un abonnement sans modification.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Puis-je créer un accélérateur de solution si je dispose de Microsoft Azure pour DreamSpark ?

> [!NOTE]
> Microsoft Azure pour DreamSpark s’appelle désormais Microsoft Imagine pour les étudiants.

Il n’est pas possible de créer un accélérateur de solution avec un compte [Microsoft Azure pour DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). Vous pouvez toutefois créer en quelques minutes un [compte d’essai gratuit pour Azure](https://azure.microsoft.com/free/), que vous pouvez utiliser pour créer un accélérateur de solution.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Comment supprimer un client Azure AD ?

Consultez le billet de blog d’Eric Golpe, [Procédure pas à pas pour la suppression d’un client Azure AD](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités des accélérateurs de solution IoT :

* [Explorer les fonctionnalités de l’accélérateur de solution de supervision à distance](quickstart-remote-monitoring-deploy.md)
* [Présentation de l’accélérateur de solution de maintenance prédictive](./iot-accelerators-predictive-walkthrough.md)
* [Déployer l’accélérateur de solution Usine connectée](quickstart-connected-factory-deploy.md)
* [Sécurisation de l’Internet des objets de bout en bout](../iot-fundamentals/iot-security-ground-up.md)