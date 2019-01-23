---
title: Publier une offre d’application Azure - Place de marché Microsoft Azure | Microsoft Docs
description: Décrit le processus et les étapes de publication d’une offre d’application Azure sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: pbutlerm
ms.openlocfilehash: deab8298d09972a4e60be5c72d6d7e43dc2683e6
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329988"
---
# <a name="publish-azure-application-offer"></a>Publier une offre d’application Azure

Une fois que vous avez créé une offre en fournissant les informations correspondantes dans la page **New Offer**, vous pouvez publier cette offre. Sélectionnez **Publier** pour démarrer le processus de publication.

Le schéma ci-après illustre les principales étapes du processus de publication d’une offre.

![Étapes de publication d’offre](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Description détaillée des étapes de publication

Le tableau ci-après liste et décrit chacune des étapes de publication, et fournit une estimation de la durée nécessaire à leur exécution.  Les estimations de durées en « jours » sont exprimées en jours ouvrables, ce qui exclut les week-ends et les jours fériés.

|  **Étape de publication**           | **Time**    | **Description**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Valider les prérequis         | < 15 min    | Les informations de l’offre et les paramètres de l’offre sont validés.                        |
| Valider les paramètres du chiffre d’affaires influencé | < 15 min  |      |
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


## <a name="errors-and-review-feedback"></a>Erreurs et commentaires de révision

En plus de l’affichage de l’état de la publication de votre offre, l’onglet **Status** affiche des messages d’erreur et des commentaires à partir de l’étape **Révision Microsoft**.  En règle générale, les problèmes de révision sont référencés sous forme de demande de tirage (pull request).  Chaque demande de tirage est liée à un élément Visual Studio Team Services (VSTS, renommé [Azure DevOps](https://azure.microsoft.com/services/devops/)) en ligne, qui contient des détails sur le problème.  L’image suivante affiche un exemple de référence de demande de tirage de révision.  Pour les cas plus complexes, les équipes de support et de révision peuvent vous envoyer un e-mail. 

![Onglet Status affichant les commentaires de révision](./media/status-tab-ms-review.png)

Vous devez résoudre chaque problème signalé pour que le processus de publication de l’offre puisse se poursuivre.  Le diagramme suivant illustre le lien entre ce processus de commentaires et le processus de publication.

![Étapes de publication avec commentaires VSTS](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>Accès VSTS

Pour voir les éléments VSTS référencés dans les commentaires de révision, les éditeurs doivent se voir accorder l’autorisation appropriée.  Sinon, les nouveaux éditeurs reçoivent une page de réponse `401 - Not Authorized`.  Pour demander l’accès au système VSTS de révision des offres, effectuez les étapes suivantes :

1. Collectez les informations suivantes :
    - Vos nom et ID d’éditeur
    - Type d’offre (application Azure), nom et ID de référence SKU de l’offre
    - Lien de la demande de tirage, par exemple : `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Cette URL peut être récupérée à partir du message de notification ou de l’adresse de la page de réponse 401.
    - Les adresses e-mail des personnes de votre organisation de publication auxquelles vous souhaitez accorder l’accès.  Elles doivent inclure les adresses de propriétaire que vous avez fournies lors de votre inscription en tant qu’éditeur sur le Portail Cloud Partner.
2. Créez un incident de support.  Dans la barre de titre du Portail Cloud Partner, sélectionnez le bouton **Help**, puis choisissez **Support** dans le menu.  Votre navigateur web par défaut doit se lancer et accéder à la page de nouvel incident de support Microsoft.  (Vous devrez peut-être vous connecter préalablement.)
3. Spécifiez **marketplace onboarding** (Intégration à la place de marché) pour **Problem type** et **Access problem** pour **Category**, puis sélectionnez **Start request** (Démarrer la demande).

    ![Catégorie de ticket de support](./media/support-incident1.png)

4. Dans la page **Step 1 of 2**, fournissez vos informations de contact et sélectionnez **Continue**.
5. Dans la page **Step 2 of 2**, spécifiez un titre d’incident (par exemple `Request VSTS access`) et indiquez les informations que vous avez recueillies à la première étape (ci-dessus).  Lisez et acceptez le contrat, puis sélectionnez **Submit**.

Si la création de l’incident a réussi, une page de confirmation s’affiche.  Enregistrez les informations de confirmation à titre de référence.  Le support Microsoft doit répondre à votre demande d’accès sous quelques jours ouvrés.


## <a name="next-steps"></a>Étapes suivantes

Une fois qu’une application Azure est publiée, vous pouvez [mettre à jour une offre existante](./cpp-update-existing-offer.md) afin de refléter l’évolution des exigences techniques ou métier. 
