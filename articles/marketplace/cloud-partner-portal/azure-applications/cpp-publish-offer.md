---
title: Publier une offre d’application Azure | Place de marché Azure
description: Décrit le processus et les étapes de publication d’une offre d’application Azure sur la Place de marché Microsoft Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280097"
---
# <a name="publish-azure-application-offer"></a>Publier une offre d’application Azure

Une fois que vous avez créé une offre en fournissant les informations correspondantes dans la page **New Offer**, vous pouvez publier cette offre. Sélectionnez **Publier** pour démarrer le processus de publication.

Le schéma ci-après illustre les principales étapes du processus de publication d’une offre.

![Étapes de publication d’une offre](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Description détaillée des étapes de publication

Le tableau ci-après liste et décrit chacune des étapes de publication, et fournit une estimation de la durée nécessaire à leur exécution.  Les estimations de durées en « jours » sont exprimées en jours ouvrables, ce qui exclut les week-ends et les jours fériés.

|  **Étape de publication**           | **Time**    | **Description**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Valider les prérequis         | < 15 min    | Les informations de l’offre et les paramètres de l’offre sont validés.                        |
| Valider les paramètres du chiffre d’affaires influencé | < 15 min  | L’attribution d’utilisation de ressources Azure pour l’offre est vérifiée.             |
| Certification                  | < 1 jour     | L’offre est analysée par l’équipe de certification d’Azure. Elle vérifie la présence de virus, de programmes malveillants, de problèmes de sécurité, ainsi que la conformité aux normes de sécurité. L’offre est vérifiée pour voir si elle répond aux critères d’éligibilité. Pour plus d’informations, consultez [Prérequis](./cpp-prerequisites.md). Si un problème est détecté, des commentaires sont fournis. |
| Validation Test Drive          | < 2 heures   | (Facultatif) Si un Test Drive est présent, Microsoft valide qu’il peut être déployé et répliqué.  |
| Empaquetage et référencement de génération de prospects | < 1 heure  | Les ressources techniques de l’offre sont empaquetées à des fins d’utilisation par le client, et les systèmes de prospection sont installés et déployés. |
|  Approbation de l’éditeur             |  manual    | Révision finale de l’éditeur et confirmation avant la mise en ligne de l’offre. L’offre est désormais disponible en préversion.  Vous pouvez déployer votre offre dans les abonnements sélectionnés (dans les étapes d’informations de l’offre) pour vérifier qu’elle répond à toutes vos exigences.  Après avoir vérifié l’offre, sélectionnez **Go Live** pour faire passer votre offre à l’étape suivante. |
| Révision Microsoft                | 7 à 14 jours | Microsoft examine votre application Azure de manière holistique et vous avertit par e-mail en cas de problèmes.  La durée de cette étape varie selon la complexité de l’application, les problèmes détectés et la rapidité avec laquelle vous les traitez.  |
| En direct                           | < 1 jour | L’offre est publiée, répliquée dans les régions spécifiées et mise à la disposition du public. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

Vous pouvez superviser le processus de publication sous l’onglet **Status** de votre offre dans le Portail Cloud Partner.

![Onglet Status d’une offre d’application Azure](./media/offer-status-tab.png)

Une fois que vous avez terminé le processus de publication, votre offre est listée dans la [catégorie d’applications Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>L’abonnement à un réseau de partenaires fournisseurs de solution cloud est maintenant disponible.  Consultez [Fournisseurs de solutions cloud](../../cloud-solution-providers.md) pour plus d’informations sur le marketing de votre offre via les réseaux de partenaires fournisseurs de solutions cloud Microsoft.

## <a name="errors-and-review-feedback"></a>Erreurs et commentaires de révision

En plus d'afficher l'état de publication de votre offre, l'onglet **État** affiche également les messages d'erreur et les commentaires des étapes de publication au cours desquelles un problème survient.  Si le problème est critique, la publication est annulée.  Vous devez alors corriger les problèmes signalés et republier l'offre.  Dans la mesure où l'étape **Révision Microsoft** équivaut à un examen approfondi de votre offre et des ressources techniques associées à celle-ci (modèle Azure Resource Manager, notamment), les problèmes sont généralement présentés sous la forme de liens de demande de tirage (pull request).  Pour savoir comment afficher ces demandes de tirage (pull requests) et y répondre, consultez [Gestion des commentaires de révision](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des erreurs au cours d'une ou plusieurs étapes de publication, vous devez les corriger et republier votre offre.  Si des problèmes critiques surviennent lors de l'étape **Révision Microsoft**, vous devez [gérer les commentaires de révision](./cpp-handling-review-feedback.md) en accédant au référentiel Azure DevOps de l'équipe de révision Microsoft.

Une fois qu'une application Azure est publiée avec succès, vous pouvez [mettre à jour l'offre existante](./cpp-update-existing-offer.md) afin de refléter l'évolution des exigences techniques ou métier. 
