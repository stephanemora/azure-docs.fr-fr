---
title: Vue d’ensemble de l’infrastructure d’interface utilisateur d’Azure Communication Services
titleSuffix: An Azure Communication Services conceptual document
description: Découvrir l’infrastructure d’interface utilisateur d’Azure Communication Services
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b6f9987c21ecdcf0e1b5358486312dceb26c8b82
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008551"
---
# <a name="azure-communication-services-ui-framework"></a>Infrastructure d’interface utilisateur d’Azure Communication Services

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Comparaison des composites prédéfinis et des composites personnalisés":::

L’infrastructure d’interface utilisateur d’Azure Communication Services vous permet de créer facilement des expériences utilisateur de communications modernes. Elle vous donne accès à une bibliothèque de composants d’interface utilisateur prêts pour la production que vous pouvez placer dans vos applications :

- **Composants composites** – Ces composants sont des solutions clés en main qui implémentent des scénarios de communication courants. Vous pouvez ajouter rapidement des expériences d’appel vidéo ou de conversation à leurs applications. Les composites sont des composants open source construits avec des composants de base.
- **Composants de base** – Ces composants sont open source et vous permettent de créer une expérience de communication personnalisée. Il existe des composants pour les appels et la conversation et peuvent être combinés pour créer des expériences. 

Ces bibliothèques de client d’interface utilisateur utilisent toutes les ressources et le [langage de conception Fluent de Microsoft](https://developer.microsoft.com/fluentui/). L’interface utilisateur Fluent est la couche de base sur laquelle s’appuie l’infrastructure d’interface utilisateur qui a été testée sur les produits Microsoft.

## <a name="differentiating-components-and-composites"></a>**Différenciation des composants et des composites**

Les **composants de base** reposent sur les principales bibliothèques de client Azure Communication Services et implémentent des actions de base telles que l’initialisation des principales bibliothèques de client, l’affichage vidéo et la mise à disposition de contrôles utilisateur pour l’activation/désactivation du son, de la vidéo, etc. Vous pouvez utiliser ces **composants de base** pour créer vos propres expériences de disposition personnalisées à l’aide de composants de communication prédéfinis prêts pour la production.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Vue d’ensemble des composants pour l’infrastructure d’interface utilisateur":::

Les **composants composites** combinent plusieurs **composants de base** pour créer des expériences de communication plus complètes. Ces composants de niveau supérieur peuvent être facilement intégrés à une application existante pour apporter une expérience de communication complète sans avoir à la créer en partant de zéro. Les développeurs peuvent se concentrer sur la création de l’expérience environnante et du flux souhaités dans leurs applications et laisser gérer la complexité des communications aux composants composites.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Vue d’ensemble des composites pour l’infrastructure d’interface utilisateur":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Quelle infrastructure d’interface utilisateur convient le mieux pour mon projet ?

La compréhension de ces exigences vous aidera à choisir la bibliothèque de client appropriée :

- **Quel degré de personnalisation souhaitez-vous ?** Les principales bibliothèques de client Azure Communication ne disposent pas d’une expérience utilisateur. Elles sont conçues pour vous permettre d’en créer une qui répond à vos besoins. Les composants de l’infrastructure d’interface utilisateur proposent des ressources d’interface utilisateur au prix d’une personnalisation limitée.
- **Avez-vous besoin de fonctionnalités de réunion ?** Le système de réunion offre plusieurs fonctionnalités uniques qui ne sont actuellement pas disponibles dans les principales bibliothèques de client Azure Communication Services, telles que l’arrière-plan flou et la main levée.
- **Quelles plateformes ciblez-vous ?** Les capacités varient d’une plateforme à l’autre.

Des informations détaillées sur les fonctionnalités disponibles dans les divers kits [SDK d’interface utilisateur sont présentées ici](ui-sdk-features.md), mais les principaux arbitrages sont résumés ci-dessous.

|Bibliothèque de cliente/SDK|Complexité de l’implémentation|    Possibilité de personnalisation|  Appel| Conversation| [Interopérabilité avec Teams](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Composants composites|Faible|Faible|✔|✔|✕
|Composants de base|Moyenne|Moyenne|✔|✔|✕
|Principales bibliothèques de client|Élevé|Élevé|✔|✔ |✔

## <a name="cost"></a>Coût

L’utilisation des infrastructures d’interface utilisateur Azure n’induit pas de coûts Azure supplémentaires ni de contrôles. Vous payez uniquement l’utilisation du service sous-jacent, en utilisant les mêmes compteurs d’appel, de conversation et RTPC.

## <a name="supported-use-cases"></a>Cas d’usage pris en charge

Appel :

- Participer à un appel Azure Communication Services avec un ID de groupe

Conversation :

- Participer à une conversation Azure Communication Services avec un ID de thread

## <a name="supported-identities"></a>Identités prises en charge :

Une identité Azure Communication Services est nécessaire pour initialiser l’infrastructure d’interface utilisateur et s’authentifier auprès du service. Pour plus d’informations sur l’authentification, consultez [Authentification](../authentication.md) et [Jetons d’accès](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Architecture recommandée 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Architecture recommandée pour l’infrastructure d’interface utilisateur avec une architecture client-serveur ":::

Les composants composites et de base sont initialisés à l’aide d’un jeton d’accès Azure Communication Services. Les jetons d’accès doivent être obtenus auprès d’Azure Communication Services via un service approuvé que vous gérez. Consultez [Démarrage rapide : Créer des jetons d’accès](../../quickstarts/access-tokens.md) et [Tutoriel Service approuvé](../../tutorials/trusted-service-tutorial.md) pour plus d’informations.

Ces bibliothèques de client ont également besoin du contexte de l’appel ou de la conversation auxquels elles se joignent. À l’instar des jetons d’accès utilisateur, ce contexte doit être distribué aux clients par l’intermédiaire de votre propre service approuvé. La liste ci-dessous résume les fonctions d’initialisation et de gestion des ressources que vous devez rendre opérationnelles.

| Responsabilités de Contoso                                 | Responsabilités de l’infrastructure d’interface utilisateur                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Fournir un jeton d’accès d’Azure                    | Transférer le jeton d’accès fourni pour initialiser les composants        |
| Fournir une fonction d’actualisation                                 | Actualiser le jeton d’accès à l’aide de la fonction fournie par les développeurs          |
| Récupérer/transmettre les informations permettant de se joindre à l’appel ou à la conversation          | Transmettre les informations d’appel et de conversation pour initialiser les composants |
| Récupérer/transmettre des informations utilisateur pour un modèle de données personnalisé | Transmettre le modèle de données personnalisé aux composants à afficher          |
