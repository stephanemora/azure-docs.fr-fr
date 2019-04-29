---
title: Publier une offre d'application Power BI - Place de marché Microsoft Azure | Microsoft Docs
description: Publier une offre d’application Power BI sur la place de marché Microsoft AppSource.
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
ms.openlocfilehash: 0e82041dab1932ed7c129b598740b2441aa59315
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725707"
---
# <a name="publish-a-power-bi-app-offer"></a>Publier une offre d’application Power BI

La dernière étape, après avoir défini une offre dans le portail Cloud Partner et créé les ressources techniques associées, consiste à soumettre l’offre pour la publication. Pour démarrer ce processus, dans le volet gauche de la **nouvelle offre** fenêtre, sélectionnez **publier**. Pour plus d'informations, consultez [Publier des offres sur la Place de marché Microsoft Azure et sur AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Étapes de publication

Voici les principales étapes dans le processus de publication :

![Offrent des étapes de processus de publication pour l’application Power BI](./media/publishing-process-steps.png)

Ce tableau décrit chaque étape et fournit son heure d’achèvement estimé :

|   Étape de publication            |   Temps     |   Description                                                                  |
| --------------------         |------------| ----------------                                                               |
| Valider les prérequis       | 15 minutes     | Les informations de l’offre et les paramètres de l’offre sont validés.                            |
| Certification                | 1 à 7 jours   | L’équipe de Certification de Power BI analyse votre offre. L’équipe s’exécute votre application Power BI via un test de vérification manuelle en installant l’application par le biais de l’URL d’installation fourni. Principales validations sont effectuées dans le cadre du processus de certification d’application (décrit plus loin dans ce document).         |
| Packaging                    | \< 1 heure  | Ressources techniques de l’offre sont empaquetées pour une utilisation par le client.                        |
| Inscription de la génération de leads | \< 1 heure  | Les systèmes de prospects sont configurés et déployés.                                      |
| Validation de l’éditeur            | \-         | Vous effectuez une vérification finale et la confirmation avant de l’offre publiée. Vous aurez également désormais un lien pour afficher un aperçu de votre offre. Une fois que vous êtes satisfait de l’aspect de la version d’évaluation, sélectionnez **Go Live** sur le **état** onglet. Ceci envoie une demande à l’équipe d’intégration pour répertorier votre application sur AppSource.    |
| En direct                         | \< 3 heures | Votre offre est désormais publiquement répertorié (« en temps réel ») sur AppSource, et les clients peuvent afficher votre application et déployez-la dans leurs abonnements Power BI. Vous recevrez également un e-mail de confirmation. Dans la colonne de droite sur la **toutes les offres** onglet, vous pouvez voir l’état de toutes vos offres. Sur le **état** onglet, vous pouvez voir l’état de flux de publication détaillées pour votre offre. |
|   |   |

Autoriser jusqu'à huit jours pour ce processus soit terminé. Une fois que vous effectuez ces étapes de publication, votre offre d’application Power BI s’afficheront dans le [AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20) section applications de Power BI.


### <a name="app-certification-process"></a>Processus de certification de l'application

L’équipe d’intégration de Microsoft utilise ce processus pour valider votre soumission d’offre d’application Power BI :

1. Passez en revue les liens juridiques de documents et de l’aide.
2. Valider les informations de contact de prise en charge.
3. Utilisez l’URL du programme d’installation pour vérifier l’installation correcte.
4. Analyse de l’application pour les logiciels malveillants et autres contenus malveillants.
5. Vérifiez que le contenu affiché correspond à la description de l’application.
6. Vérifiez que les opérations liées à l’application fonctionnent comme prévu dans Power BI. L’équipe ouvre des rapports et tableaux de bord avec des exemples de données, se connecte aux sources de données personnalisées, actualise les données et ainsi de suite.

L'équipe de certification communique les éventuels problèmes rencontrés.  Pour plus d’informations concernant les spécifications des applications Power BI, consultez le [documentation d’application Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons de régulièrement surveiller votre application dans le [place de marché AppSource](https://appsource.microsoft.com).  Vous devez également utiliser le [Insights de vendeur](../../cloud-partner-portal-orig/si-getting-started.md) fonctionnalité de la [portail Microsoft Cloud Partner](https://cloudpartner.azure.com/#insights) pour obtenir des informations sur les clients de la place de marché et l’utilisation des applications. Enfin, vous pouvez [mettre à jour votre offre](./cpp-update-existing-offer.md).
