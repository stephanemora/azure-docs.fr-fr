---
title: Publier une offre d'application Power BI - Place de marché Microsoft Azure | Microsoft Docs
description: Publiez une offre d'application Power BI sur la Place de marché Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: pbutlerm
ms.openlocfilehash: 2b3783060cf5502076ce3bc98cf07f005366a9e2
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665710"
---
# <a name="publish-power-bi-app-offer"></a>Publier une offre d'application Power BI

La dernière étape, après avoir défini l’offre dans le portail et créé les ressources techniques associées, consiste à envoyer l’offre à la publication.  Pour lancer ce processus, cliquez sur le bouton **Publier** du menu vertical de la fenêtre **Nouvelle offre**.  Pour plus d'informations, consultez [Publier des offres sur la Place de marché Microsoft Azure et sur AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Étapes de publication

Le schéma suivant illustre les principales étapes du processus de publication.

![Étapes du processus de publication d'une application Power BI](./media/publishing-process-steps.png)

Le tableau suivant décrit ces étapes et fournit une estimation de leur durée maximale :

|   Étape de publication            |   Temps     |   Description                                                                  |
| --------------------         |------------| ----------------                                                               |
| Valider les prérequis       | 15 min     | Les informations de l’offre et les paramètres de l’offre sont validés.                            |
| Certification                | 1 à 7 jours   | L'équipe de certification Power BI analyse votre offre. Nous soumettons votre application Power BI à un test de vérification manuel en l'installant via l'URL d'installation fournie. Les principales validations sont effectuées dans le cadre du processus de certification de l'application ; voir ci-dessous.         |
| Packaging                    | \< 1 heure  | Les ressources techniques de l'offre sont empaquetées pour permettre au client de les utiliser.                        |
| Référencement de génération de prospects | \< 1 heure  | Les systèmes de prospects sont configurés et déployés.                                      |
| Validation de l’éditeur            | \-         | Révision finale de l’éditeur et confirmation avant la mise en ligne de l’offre. Vous disposez maintenant d'un lien pour afficher un aperçu de votre offre. Une fois satisfait par l'aperçu, cliquez sur le bouton **Démarrer** de l'onglet **État**. Cette action envoie une requête à l'équipe d'intégration pour qu'elle répertorie votre application sur AppSource.    |
| En direct                         | \< 3 heures | Votre offre est désormais disponible en direct sur AppSource, et les clients sont en mesure d'afficher et de déployer votre application dans leurs abonnements Power BI. Vous recevrez également un e-mail de confirmation. À tout moment, vous pouvez cliquer sur l'onglet **Toutes les offres** et voir l'état de toutes vos offres dans la colonne de droite. Vous pouvez cliquer sur l'onglet **État** pour afficher l'état détaillé du flux de publication de votre offre. |
|   |   |

Ce processus peut prendre jusqu'à huit jours. Après avoir suivi ces étapes de publication, votre offre d'application Power BI est répertoriée dans la section Applications Power BI d'[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20).


### <a name="app-certification-process"></a>Processus de certification de l'application

L'équipe d'intégration de Microsoft utilise le processus suivant pour valider la soumission de votre offre Power BI :

1. Les documents juridiques et les liens d'aide sont passés en revue.
2. Les coordonnées du support sont validées.
3. L'URL d'installation est utilisée pour vérifier que l'installation s'effectue correctement. 
4. L'application est analysée pour détecter d'éventuels programmes et contenus malveillants. 
5. Le processus vérifie que le contenu affiché correspond à la description de l'application.
6. Les opérations liées à l'application fonctionnent comme prévu dans Power BI : ouverture de rapports et de tableaux de bord contenant des échantillons de données, connexion à des sources de données personnalisées, actualisation, etc.

L'équipe de certification communique les éventuels problèmes rencontrés.  Pour plus d'informations sur les exigences liées aux applications Power BI, consultez la [documentation correspondante](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons de surveiller régulièrement votre application sur la [Place de marché Microsoft Azure](https://appsource.microsoft.com).  En outre, vous devez utiliser la fonctionnalité [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) du [Portail Cloud Partner](https://cloudpartner.azure.com/#insights) pour fournir des informations sur les clients et leur utilisation de votre Place de marché.  Vous pouvez également procéder à certaines [mises à jour de votre offre](./cpp-update-existing-offer.md).
