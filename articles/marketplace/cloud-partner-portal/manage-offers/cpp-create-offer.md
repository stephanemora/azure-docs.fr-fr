---
title: Créer des offres sur une place de marché | Place de marché Azure
description: Créer des offres sur les places de marché Microsoft Azure et AppSource à l’aide du Portail Cloud Partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: dsindona
ms.openlocfilehash: d14940eff569a80759e9db0bfa2d6d1541a01377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286470"
---
# <a name="create-azure-marketplace-and-appsource-offers"></a>Créer des offres sur la Place de marché Microsoft Azure et AppSource

Le but principal du Portail Cloud Partner est de permettre aux éditeurs de créer (et publier) des offres sur les places de marché Microsoft Azure et AppSource.  Cette opération commence toujours par la sélection du type d’offre souhaité dans le [menu New Offer](../portal-tour/cpp-new-offer-menu.md).  Ensuite, la page **New Offer** correspondante s’affiche.  Par exemple, l’illustration suivante montre la page **New Offer** par défaut pour un type d’application Azure.

![Page New Offer par défaut](./media/new-offer-page.png)

Deux onglets sont disponibles dans la barre de menus horizontale affichée en haut de cette page : 
- Onglet **Editor** : permet d’entrer les informations et de charger les ressources liées à l’instance de la nouvelle offre.  Cet onglet s’affiche par défaut.
- Onglet **Status** : indique l’état de publication et liste les problèmes de validation et de révision. 

Quand vous créez une offre, vous utilisez l’onglet **Editor** pour entrer les informations relatives à celle-ci. 

## <a name="editing-operations"></a>Opérations de modification

La barre d’outils horizontale, située au-dessus de la zone d’entrée des données, affiche les boutons suivants :

|   Bouton    |   Objectif                                                          |
|   ------    |  --------                                                          |
| **Save**    | Enregistre les modifications d’entrée de données récentes.  Vous devez enregistrer manuellement les modifications avant de quitter la page, sinon vos modifications sont perdues. | 
| **Discard** (Ignorer) | Ignore les modifications d’entrée de données récentes (depuis le dernier enregistrement).             |
| **Compare** | Compare l’état de l’offre actuelle avec l’offre publiée.  Cette option est uniquement activée quand une offre a été correctement publiée.  |
| **Publier** | Commence le processus de publication de cette offre.                       |
| **Supprimer**  | Supprime cette offre après sa création, mais avant sa publication. |
|   |   |


## <a name="editing-tabs"></a>Onglets d’édition

Quand vous créez une offre, fournissez les données obligatoires et facultatives sous chaque onglet situé dans la colonne gauche verticale de la page **New Offer**.  Des contrôles d’interface utilisateur standard, tels que des zones de texte, des menus déroulants et des cases à cocher, sont affichés pour la collecte des données.  Bien que le jeu d’onglets d’édition varie selon le type d’offre, le tableau suivant liste certains des onglets courants.

|      Nom de l’onglet       |   Objectif                                                            |
|      --------       |   -------                                                            |
| **Paramètres de l’offre**  | Collecte les informations liées à l’offre et à l’identité de l’éditeur.                    |
| **Références SKU**            | Définit les caractéristiques techniques et commerciales de chaque version de référence SKU de votre offre. |
| **Version d’évaluation**      | Pour les types qui prennent en charge cette fonctionnalité optionnelle, définit une démonstration de votre offre.  Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’une version d’évaluation ?](../test-drive/what-is-test-drive.md)  |
| **Marketplace** (Place de marché) ou **Storefront** (Vitrine) | Collecte les chaînes de texte, documents et images servant à référencer l’offre dans la place de marché. |
| **Support**         | Collecte les informations de contact du client, de l’équipe technique et du support en ligne.  |
|  |  |

Le contenu des onglets portant le même nom peut différer d’un type d’offre à l’autre.  Les détails propres à l’offre de ces onglets sont fournis dans la section « Create offer » pour chaque type d’offre.


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez créé et enregistré une offre et avant ou après l’avoir publiée, vous pouvez [voir son état](./cpp-view-status-offer.md).
