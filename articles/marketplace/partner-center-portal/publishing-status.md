---
title: Vérifier l’état de publication de votre offre de Place de marché commerciale
description: Vérifiez l’état des étapes de validation, de certification et d’aperçu nécessaires pour publier une offre par le biais de la Place de marché commerciale dans Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275677"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Vérifier l’état de publication de votre offre de Place de marché commerciale

Vous pouvez afficher votre **État de publication** actuel sous l’onglet **Vue d’ensemble de l’offre** du [portail de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) dans l’Espace partenaires.

Un des indicateurs d’état suivants doit être affiché pour chaque offre.

| **État**    | **Description**  |
| :---------- | :-------------------|
| **Draft** | L’offre a été créée, mais elle n’est actuellement pas publiée. |
| **Publish in progress** (Publication en cours) | Le processus de publication de l’offre/du plan est en cours. |
| **Attention requise** | Un problème critique a été détecté lors de la certification par Microsoft ou de l’une des étapes de publication. |
| **Préversion** | L’offre a été certifiée par Microsoft et doit maintenant faire l’objet d’une vérification finale par l’éditeur. Sélectionnez Mise en ligne pour proposer l’offre en direct. |
| **En direct** | L’offre est également disponible en direct dans la Place de marché. Des clients peuvent la voir et l’acheter. |
| **Arrêt de la vente en attente** | L’éditeur a sélectionné « Arrêter la vente » sur l’offre ou le plan, mais l’action n’est pas encore terminée. |
| **Non disponible dans la Place de marché** | Une offre/un plan publié dans la Place de marché a été supprimé. |

## <a name="automated-validation"></a>Validation automatique

La première étape du processus de publication est un ensemble de validations automatiques. Chaque étape de validation correspond à une fonctionnalité que vous avez choisi d’activer dans la création de votre offre. Si cette fonctionnalité n’a pas été activée, la validation passe à l’étape de publication suivante. Chaque vérification de la validation doit être effectuée avant que l’état de la publication soit approuvé.

- **Configuration du flux d’achat de l’offre (< 10 min)**

Dans cette étape, nous faisons en sorte que votre offre puisse être traitée quand elle est achetée par des clients à l’aide du portail Azure. Cette étape est applicable uniquement aux offres vendues par le biais de Microsoft.

- **Validation des données de la version d’évaluation (environ 5 min)**

Dans cette étape, nous validons les données que vous avez fournies dans la section Configuration technique de la version d’évaluation de l’offre. Les fonctionnalités de la version d’évaluation sont testées et approuvées. Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

- **Provisionnement de la version d’évaluation (environ 30 min)**

Dans cette étape, après avoir validé les données et les fonctionnalités de votre version d’évaluation à l’étape précédente, nous déployons et répliquons des instances de votre version d’évaluation afin qu’elles soient prêtes à être utilisées par les clients.  Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

- **Validation et enregistrement de la gestion des prospects (< 15 min)**

Dans cette étape, nous vérifions que votre système de gestion des prospects peut recevoir des prospects en fonction des détails fournis dans la configuration de l’offre. Cette étape est applicable uniquement pour les offres avec la gestion des prospects activée.

## <a name="certification"></a>Certification

Avant d’être publiées, les offres soumises à la Place de marché commerciale dans l’Espace partenaires doivent être certifiées. Les offres soumises font l’objet de tests rigoureux, certains automatisés et d’autres manuels, notamment un contrôle par rapport aux [stratégies de certification de la Place de marché Azure](https://docs.microsoft.com/legal/marketplace/general-policies). Les soumissions d’offres doivent être marquées comme éligibles pour la certification avant de passer à l’étape suivante dans le flux de publication.

### <a name="types-of-validation-that-take-place-during-certification"></a>Types de validation qui se produisent lors de la certification

Le processus de certification comprend trois niveaux de validation pour chaque offre soumise.

- Éligibilité de l’entreprise de l’éditeur
- Validation du contenu
- Validation technique

#### <a name="publisher-business-eligibility"></a>Éligibilité de l’entreprise de l’éditeur

Chaque type d’offre vérifie un ensemble de critères d’éligibilité de base que l’éditeur doit satisfaire. Les critères d’éligibilité peuvent inclure l’état MPN de l’éditeur, les compétences détenues, les niveaux de compétence, etc.

#### <a name="content-validation"></a>Validation du contenu

Pendant la validation du contenu, la qualité et la pertinence des informations entrées lors de la création de votre offre sont vérifiées. Ces vérifications passent en revue vos entrées pour les détails de référencement dans la Place de marché, le tarif, la disponibilité, les plans associés, etc. Pour répondre aux critères de référencement dans la Place de marché Azure et/ou AppSource, nous vérifions que votre offre inclut les éléments suivants :

- Un titre qui décrit précisément l’offre
- Des descriptions bien écrites qui fournissent une vue d’ensemble détaillée et une proposition de valeur
- Des captures d’écran de qualité et des vidéos d’accompagnement, et
- Une description de la façon dont l’offre utilise les outils et des plateformes Microsoft.

Apprenez-en plus sur les critères de validation du contenu en lisant les [stratégies de référencement générales](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Validation technique

Pendant la validation technique, l’offre (package ou binaire) fait l’objet des vérifications suivantes.
- Analyse pour détecter les programmes malveillants
- Supervision des appels réseau
- Analyse du package
- Analyse complète des fonctionnalités réelles de l’offre

L’offre est testée sur différentes plateformes et versions afin de garantir qu’elle est fiable.

Passez en revue les détails de configuration spécifiques exigés pour votre offre dans la section Configuration technique de ce document.

### <a name="certification-failure-report"></a>Rapport d’échec de la certification

Une fois les vérifications terminées, si votre offre a réussi la certification, elle passe à l’étape suivante du processus de publication. Si l’une des vérifications de référencement, techniques ou de stratégie échoue, ou si vous n’êtes pas qualifié pour soumettre une offre de ce type, un rapport d’échec de certification est généré et vous est envoyé par e-mail.

Ce rapport contient des descriptions de toutes les stratégies ayant échoué, ainsi que des remarques sur les vérifications. Passez en revue ce rapport par e-mail, résolvez tous les problèmes, en mettant à jour votre offre si nécessaire, puis resoumettez l’offre à l’aide du [portail de la Place de marché commerciale](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) dans l’Espace partenaires. (Vous pouvez resoumettre l’offre autant de fois que nécessaire jusqu’à ce que la certification réussisse).

## <a name="preview-creation"></a>Création de la préversion

Lors de l’étape de **création de la préversion**, nous créons une version de votre offre accessible uniquement au public que vous avez spécifié dans la section Préversion de votre offre.

>[!Note]
> N'utilisez pas cette étape pour permettre aux personnes extérieures à votre organisation de visualiser une offre. Utilisez plutôt l'option **Private Offer (Offre privée)** . À ce stade, votre offre n'a pas encore été entièrement testée et validée, et elle n'est pas prête à être distribuée à l'extérieur.

## <a name="publisher-approval"></a>Approbation de l’éditeur

Dans cette étape, vous recevrez un e-mail vous demandant de passer en revue et d’approuver la préversion de votre offre avant l’étape finale de la publication.

Si vous avez choisi de vendre votre offre par le biais de Microsoft, vous pouvez tester son acquisition et son déploiement pour vérifier qu’elle répond à vos exigences au cours de cette phase d’approbation de la préversion. Votre offre ne sera pas encore disponible dans la marketplace publique. Une fois que vous avez testé et approuvé cette préversion, vous devez sélectionner **Mise en service** sur le tableau de bord [**Vue d’ensemble de l’offre**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

Si vous voulez apporter des changements à l’offre pendant cette phase de préversion, vous pouvez la modifier et la resoumettre pour publier une nouvelle préversion. Pour plus d’informations sur d’autres changements, consultez l’article [Mettre à des offres de la Place de marché existantes](#update-existing-marketplace-offers).

Si votre offre est déjà en direct et disponible pour le grand public dans la Place de marché, toutes les mises à jour que vous apportez ne sont pas mises en service tant que vous ne sélectionnez pas **Mise en service** sur le tableau de bord [**Vue d’ensemble de l’offre**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).

### <a name="publish-offer-to-the-public"></a>Publier l’offre au public

Connectez-vous à l’Espace partenaires et accédez à l’offre. Vous êtes alors redirigé vers la page **Vue d’ensemble de l’offre**. En haut de cette page, vous voyez une option pour **Mise en service**. Sélectionnez **Mise en ligne**, et après confirmation, l’offre commence à être publiée au public. Vous recevez une notification par e-mail quand l’offre est publiée.

## <a name="publish"></a>Publish

Maintenant que vous avez choisi de **Mettre en service** votre offre, en la rendant disponible dans la Place de marché, il y a une série de vérifications de validation finales qui seront effectuées pour garantir que l’offre en direct est configurée exactement comme la préversion de l’offre.

- **Configuration du flux d’achat de l’offre (> 10 min)**

Dans cette étape, nous faisons en sorte que votre offre puisse être traitée quand elle est achetée par des clients à l’aide du portail Azure. Cette étape est applicable uniquement aux offres vendues par le biais de Microsoft.

- **Validation des données de la version d’évaluation (environ 5 min)**

Dans cette étape, nous validons les données que vous avez fournies dans la section Configuration technique de la version d’évaluation de l’offre. Les fonctionnalités de la version d’évaluation sont testées et approuvées. Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

- **Provisionnement de la version d’évaluation (environ 30 min)**

Dans cette étape, nous déployons et répliquons des instances de votre version d’évaluation afin qu’elles soient prêtes à être utilisées par les clients.  Cette étape est applicable uniquement pour les offres avec une version d’évaluation activée.

- **Validation et enregistrement de la gestion des prospects (> 15 min)**

Dans cette étape, nous vérifions que votre système de gestion des prospects peut recevoir des prospects en fonction des détails fournis dans la configuration de l’offre. Cette étape est applicable uniquement pour les offres avec la gestion des prospects activée.

- **Dernière publication (> 30 minutes)**

Dans cette étape, nous vérifions que votre offre est publiquement disponible dans la Place de marché.

## <a name="update-existing-marketplace-offers"></a>Mettre à jour des offres de la Place de marché existantes

Si vous voulez apporter des changements à une offre que vous avez déjà publiée, vous devez tout d’abord mettre à jour l’offre existante, puis la republier.

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](./update-existing-offer.md)
