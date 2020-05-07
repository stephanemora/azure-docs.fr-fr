---
title: Publier une offre d’application Power BI - Place de marché Microsoft Azure
description: Publiez une offre d’application Power BI sur la Place de marché Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: eb31520c81a4064edbe54a0256b694c4ad88fb49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141710"
---
# <a name="publish-a-power-bi-app-offer"></a>Publier une offre d’application Power BI

>[!Important]
>À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres d’applications Power BI vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions indiquées dans [Vue d’ensemble de la création d’applications Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) pour gérer vos offres migrées.

La dernière étape, après avoir défini l’offre dans le portail Cloud Partner et créé les ressources techniques associées, consiste à soumettre l’offre en vue de sa publication. Pour démarrer ce processus, dans le volet gauche de la fenêtre **Nouvelle offre**, sélectionnez **Publier**. Pour plus d'informations, consultez [Publier des offres sur la Place de marché Microsoft Azure et sur AppSource](../manage-offers/cpp-publish-offer.md).


## <a name="publishing-steps"></a>Étapes de publication

Voici les principales étapes du processus de publication :

![Étapes du processus de publication pour une offre d’application Power BI](./media/publishing-process-steps.png)

Ce tableau décrit chaque étape et indique la durée estimée :

|   Étape de publication            |   Temps     |   Description                                                                  |
| --------------------         |------------| ----------------                                                               |
| Valider les prérequis       | 15 minutes     | Les informations de l’offre et les paramètres de l’offre sont validés.                            |
| Certification                | 1 à 7 jours   | L’équipe de certification Power BI analyse votre offre. L’équipe soumet votre application Power BI à un test de vérification manuel en l’installant via l’URL d’installation fournie. Les principales validations sont effectuées dans le cadre du processus de certification de l’application (voir plus loin dans ce document).         |
| Packaging                    | \< 1 heure  | Les ressources techniques de l’offre sont packagées de façon à être utilisées par le client.                        |
| Inscription de génération de prospects | \< 1 heure  | Les systèmes de prospects sont configurés et déployés.                                      |
| Validation de l’éditeur            | \-         | Vous effectuez une révision finale et donnez confirmation avant la mise en ligne de l’offre. Vous disposez maintenant d’un lien pour afficher un aperçu de votre offre. Une fois que vous êtes satisfait de la préversion, sélectionnez **Démarrer** dans l’onglet **État**. Cette action envoie une requête à l’équipe d’intégration pour qu’elle répertorie votre application sur AppSource.    |
| En direct                         | \< 3 heures | Votre offre est désormais disponible sur AppSource, et les clients sont en mesure d’afficher et de déployer votre application dans leurs abonnements Power BI. Vous recevrez également un e-mail de confirmation. Dans la colonne de droite de l’onglet **Toutes les offres**, vous pouvez consulter l’état de toutes vos offres. Cliquez sur l’onglet **État** pour afficher l’état détaillé du flux de publication de votre offre. |
|   |   |

Ce processus peut prendre jusqu’à huit jours. Après avoir suivi ces étapes de publication, votre offre d’application Power BI est répertoriée dans la section Applications Power BI d’[AppSource](https://appsource.microsoft.com/marketplace/apps?product=power-bi%20).


### <a name="app-certification-process"></a>Processus de certification de l'application

L’équipe d’intégration de Microsoft utilise le processus suivant pour valider la soumission de votre offre d’application Power BI :

1. Révision des documents juridiques et des liens d’aide.
2. Validation des coordonnées de support.
3. Utilisation de l’URL d’installation pour vérifier que l’installation s’effectue correctement.
4. Analyse de l’application pour détecter d’éventuels programmes et contenus malveillants.
5. Vérification que le contenu affiché correspond à la description de l’application.
6. Vérification que les opérations liées à l’application fonctionnent comme prévu dans Power BI. L’équipe ouvre des rapports et des tableaux de bord avec des exemples de données, se connecte aux sources de données personnalisées, actualise les données, etc.

L'équipe de certification communique les éventuels problèmes rencontrés.  Pour plus d’informations sur les exigences liées aux applications Power BI, consultez la [documentation correspondante](https://go.microsoft.com/fwlink/?linkid=2028636).


## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons de surveiller régulièrement votre application sur la [Place de marché Microsoft Azure](https://appsource.microsoft.com).  Vous devez également utiliser la fonctionnalité [Seller Insights](../../cloud-partner-portal-orig/si-getting-started.md) du [Portail Cloud Partner](https://cloudpartner.azure.com/#insights) pour obtenir des informations sur les clients et leur utilisation de votre Place de marché. Enfin, vous pouvez [mettre à jour votre offre](./cpp-update-existing-offer.md).
