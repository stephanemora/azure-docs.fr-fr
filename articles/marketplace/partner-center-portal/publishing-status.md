---
title: Vérifier l’état de publication de votre offre place de marché Commercial
description: Vérification de l’état de validation, de la certification et de la préversion étapes requises pour publier une offre par le biais de la place de marché Commercial dans Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474529"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Vérifier l’état de publication de votre offre place de marché Commercial

Vous pouvez afficher votre actuel **l’état de publication** sur le **offrent la vue d’ensemble** onglet de la [portail place de marché Commercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) dans Partner Center.

Un des indicateurs d’état suivants doit être affiché pour chaque offre.

| **État**    | **Description**  |
| :---------- | :-------------------|
| **Draft** | Offre a été créée, mais il n’est pas en cours de publication. |
| **Publish in progress** (Publication en cours) | Plan/offre travaille livrée à travers les étapes du processus de publication. |
| **Faites attention** | Un problème critique a été détecté lors de la certification par Microsoft ou l’une des étapes de publication. |
| **Préversion** | Offre a été certifiée par Microsoft et attend maintenant une vérification finale par le serveur de publication. Sélectionnez mise en ligne pour rendre l’offre en direct. |
| **En direct** | Offre est également disponible dans la place de marché et peut les voir et acquis par les clients. |
| **Vente d’arrêt en attente** | Serveur de publication sélectionné « arrêter la vente » sur l’offre ou de plan, mais l’action n’est pas encore terminée. |
| **Non disponible dans la place de marché** | Un plan/offre déjà publié dans la place de marché a été supprimé. |

## <a name="automated-validation"></a>Validation automatique

La première étape dans le processus de publication est un ensemble de validations automatisés. Chaque étape de validation correspond à une fonctionnalité que vous avez choisi d’activer la création de votre offre. Si cette fonctionnalité n’était pas activée, la validation avance à l’étape suivante de publication. Chaque contrôle de validation doit être effectuée avant que l’état de publication est approuvée.

- **Offre des paramètres de flux d’achat (< 10 min)**

Dans cette étape, nous nous assurer que votre offre peut être traitée lors de l’achat par les clients via le portail Azure. Cette étape est uniquement applicable aux offres vendus via Microsoft.

- **Tester la validation de données de lecteur (~ 5 min)**

Dans cette étape, nous validons les données que vous avez fourni dans la section de configuration technique de l’offre version d’évaluation. Test des fonctionnalités de lecteur sont testée et approuvée. Cette étape est uniquement applicable pour les offres avec une version d’évaluation est activée.

- **Tester la mise en service de lecteur (environ 30 min)**

Dans cette étape, après avoir vérifié les données et les fonctionnalités de votre version d’évaluation à l’étape précédente, nous déployer et répliquer les instances de votre version d’évaluation afin qu’ils sont prêts pour une utilisation par le client.  Cette étape est uniquement applicable pour les offres avec une version d’évaluation est activée.

- **Entraîner la validation de la gestion et l’enregistrement (< 15 min)**

Dans cette étape, nous confirmer que votre système de gestion responsable peut recevoir des prospects selon les détails fournis dans le programme d’installation de l’offre. Cette étape est uniquement applicable pour les offres avec gestion des prospects est activée.

## <a name="certification"></a>Certification

Avant d’être publiée, les offres soumises à la place de marché Commercial Partner Center doivent être certifiées. Soumis offres subissent des tests rigoureux, certaines automatisés et manuels, y compris un contrôle par rapport aux autres la [stratégies de certification place de marché Azure](https://docs.microsoft.com/legal/marketplace/general-policies). Offre soumissions doivent être marquées éligibles pour la certification avant qu’ils se déroulent à l’étape suivante dans le flux de publication.

### <a name="types-of-validation-that-take-place-during-certification"></a>Types de validation qui se produisent lors de la certification

Il existe trois niveaux de validation inclus dans le processus de certification pour chaque offre soumise.

- Éligibilité des professionnels de serveur de publication
- Validation du contenu
- Validation technique

#### <a name="publisher-business-eligibility"></a>Éligibilité des professionnels de serveur de publication

Chaque type d’offre vérifie un ensemble de critères d’éligibilité de base que le serveur de publication doit satisfaire. Critères d’éligibilité peuvent inclure statut MPN de l’éditeur, compétences détenus, les niveaux de compétence, etc.

#### <a name="content-validation"></a>Validation du contenu

Au cours de la validation du contenu, les informations entrées lorsque vous avez créé votre offre sont activés pour la qualité et la pertinence. Ces vérifications passerons en revue vos entrées pour la place de marché détails, tarification, disponibilité, les plans associés, etc. de l’annonce. Pour répondre à la place de marché Azure et/ou AppSource répertoriant les critères, nous vérifions que votre offre inclut :

- un titre qui décrit précisément l’offre ;
- descriptions bien écrites qui fournissent une vue d’ensemble approfondie et une proposition de valeur ;
- captures d’écran de la qualité et qui l’accompagne vidéos ; et
- Explication de la manière dont l’offre utilise les outils et plateformes Microsoft.

En savoir plus sur les critères de validation du contenu en lisant le [général liste des stratégies](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies).

#### <a name="technical-validation"></a>Validation technique

Lors de la validation technique, l’offre (package ou binaire) subit les vérifications suivantes.
- Analyser les logiciels malveillants
- Appels réseau surveillés
- Package analysée
- Analyse complète de fonctionnalités réelles de l’offre

L’offre est testé sur différentes plateformes et versions afin de garantir qu’il est fiable.

Passez en revue les détails de configuration spécifique requises pour votre offre dans la section de configuration technique de ce document.

### <a name="certification-failure-report"></a>Rapport d’échec de certification

À la fin de la révision, si votre offre a passé la certification puis il se déplace le long à l’étape suivante dans le processus de publication. Si votre offre n’a pas pu un des contrôles de liste, techniques ou de stratégie, ou si vous n’êtes pas autorisé à soumettre une offre de ce type, un rapport d’échec de certification est généré et envoyé par courrier électronique pour vous.

Ce rapport contient des descriptions de toutes les stratégies qui ont échoué, ainsi que des remarques de vérification. Passez en revue ce rapport par courrier électronique, de résoudre les problèmes, effectuer des mises à jour à votre offre où nécessaires et soumettez à nouveau l’offre à l’aide la [portail place de marché Commercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) dans Partner Center. (Vous pouvez soumettre à nouveau l’offre aussi souvent que nécessaire jusqu'à ce que la certification de passage).

## <a name="preview-creation"></a>Création de la version préliminaire

Lors de la **afficher un aperçu de la création** étape, nous créons une version de votre offre accessible au public que vous avez spécifié dans la section Aperçu de votre offre uniquement.

## <a name="publisher-approval"></a>Approbation du serveur de publication

Dans cette étape, vous serez envoyé par e-mail avec une demande pour examiner et approuver la version d’évaluation offre avant la dernière étape de publication.

Si vous avez choisi de vendre votre offre via Microsoft, vous serez en mesure de tester l’acquisition et le déploiement de votre offre pour vous assurer qu’il répond à vos besoins au cours de cette étape d’approbation de version préliminaire. Votre offre pas encore sera disponible dans la place de marché publique. Une fois que vous testez et approuvez cette version préliminaire, vous devrez sélectionner **Go-Live** sur le [ **offrent une vue d’ensemble** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) tableau de bord.

Si vous souhaitez apporter des modifications à l’offre pendant cette étape de la version préliminaire, vous pouvez modifier et soumettre à nouveau pour publier une nouvelle version préliminaire. Consultez l’article [mise à jour de la place de marché existant offre](#update-existing-marketplace-offers) pour plus d’informations sur les autres modifications.

Si votre offre est déjà en direct et disponible pour le grand public à la place de marché, les mises à jour que vous apportez ne sont pas lancés jusqu'à ce que vous sélectionniez **Go-live** sur le [ **offrent une vue d’ensemble** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) tableau de bord.

### <a name="publish-offer-to-the-public"></a>Publier l’offre au public

Connectez-vous au centre de partenaires et accéder à l’offre. Vous êtes redirigé vers la **offrent la vue d’ensemble** page. En haut de cette page, vous voyez une option pour **mise en ligne**. Sélectionnez **mise en ligne,** et après avoir confirmé, l’offre commencent à être publié au public. Vous recevrez une notification par e-mail lors de l’offre est publiée.

## <a name="publish"></a>Publish

Maintenant que vous avez choisi de **lancés** avec votre offre, rendant disponibles dans la place de marché, il existe une série de vérifications de validation finale qui sera présenté pour vous assurer que l’offre en direct est configurée comme la version préliminaire version de l’offre.

- **Offre des paramètres de flux d’achat (> 10 min)**

Dans cette étape, nous nous assurer que votre offre peut être traitée lors de l’achat par les clients via le portail Azure. Cette étape est uniquement applicable aux offres vendus via Microsoft.

- **Tester la validation de données de lecteur (~ 5 min)**

Dans cette étape, nous validons les données que vous avez fourni dans la section de configuration technique de l’offre version d’évaluation. Test des fonctionnalités de lecteur sont testée et approuvée. Cette étape est uniquement applicable pour les offres avec une version d’évaluation est activée.

- **Tester la mise en service de lecteur (environ 30 min)**

Dans cette étape, nous déployer et répliquer les instances de votre version d’évaluation afin qu’ils sont prêts pour une utilisation par le client.  Cette étape est uniquement applicable pour les offres avec une version d’évaluation est activée.

- **Entraîner la validation de la gestion et l’enregistrement (> 15 min)**

Dans cette étape, nous confirmer que votre système de gestion responsable peut recevoir des prospects selon les détails fournis dans le programme d’installation de l’offre. Cette étape est uniquement applicable pour les offres avec gestion des prospects est activée.

- **Dernière publication (> 30 minutes)**

Dans cette étape, nous nous assurons que votre offre est publiquement disponible dans la place de marché.

## <a name="update-existing-marketplace-offers"></a>Mettre à jour les offres de la place de marché

Si vous souhaitez apporter des modifications à une offre que vous avez déjà publié, vous devez tout d’abord mettre à jour de l’offre existante, puis publiez-le de nouveau.

## <a name="next-steps"></a>Étapes suivantes

- [Mise à jour une offre existante dans la place de marché Commercial](./update-existing-offer.md)
