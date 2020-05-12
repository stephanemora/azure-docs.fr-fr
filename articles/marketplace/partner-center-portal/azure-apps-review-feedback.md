---
title: Gestion des commentaires de révision pour l’offre Azure Apps dans la Place de marché commerciale Microsoft
description: Comment gérer les commentaires de révision pour une offre Azure Apps afin d’annoncer ou de vendre dans la Place de marché Azure, AppSource ou via le programme de fournisseur de solutions Cloud (CSP) en utilisant le portail de la Place de marché commerciale sur l’Espace partenaires Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 464f4d5434c8c0b8615e24eb1a323ae66a52273c
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744866"
---
# <a name="handling-review-feedback-for-azure-apps-offer-before-publishing-in-the-azure-marketplace"></a>Gestion des commentaires de révision pour Azure App avant publication dans la Place de marché Azure

Cet article explique comment accéder à l’environnement Azure DevOps utilisé par l’équipe de révision de la Place de marché Microsoft Azure. Si vous trouvez des problèmes critiques dans votre offre d’application Azure pendant l’étape de **révision Microsoft**, vous pouvez vous connecter à ce système pour afficher des informations détaillées sur ces problèmes (commentaires de révision). Après avoir résolu tous ces problèmes, vous devez renvoyer votre offre pour continuer à la publier sur la Place de marché Azure. Le diagramme suivant illustre le lien entre ce processus de commentaires et le processus de publication.

![Processus de révision des commentaires](./media/review-feedback-process.png)

En règle générale, les problèmes de révision sont référencés sous forme de demande de tirage (pull request). Chaque demande de tirage est liée à un élément [Azure DevOps](https://azure.microsoft.com/services/devops/) (précédemment nommé Visual Studio Team Services (VSTS)) en ligne, qui contient des détails sur le problème. L’illustration suivante montre un exemple d’expérience de l’espace partenaires si des problèmes sont détectés pendant les révisions. 

![État de publication](./media/publishing-status.png)

La demande de tirage contenant des détails spécifiques sur la soumission sera mentionnée dans le lien « Afficher le rapport de certification ». Pour les cas complexes, les équipes de support et de révision peuvent aussi vous envoyer un e-mail.

## <a name="azure-devops-access"></a>Accès à Azure DevOps

Tous les utilisateurs ayant accès au rôle « développeur » dans l’espace partenaires auront accès à l’affichage des éléments PR référencés dans les commentaires de révision.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>Révision de la demande de tirage

Utilisez la procédure suivante pour passer en revue les problèmes décrits dans la demande de tirage.

1. Dans la section **Révision Microsoft** du formulaire Étapes de publication, cliquez sur le lien d’une demande de tirage pour lancer votre navigateur et accéder à la page **Vue d’ensemble** (accueil) pour cette demande de tirage. L’image suivante illustre un exemple de page d’accueil de problème critique pour l’exemple d’offre d’application Contoso. Cette page contient des informations récapitulatives utiles sur les problèmes de révision trouvés dans l’application Azure.

    [![Page d’accueil de la demande de tirage (pull request)](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Cliquez sur l’image pour la développer.*

1. (Facultatif) Sur le côté droit de la fenêtre, dans la section **Stratégies**, cliquez sur le message du problème (dans cet exemple : **Échec de la validation de la stratégie**) pour examiner les détails du problème, notamment les fichiers journaux associés. Les erreurs sont généralement affichées en bas des fichiers journaux.
1. Dans le menu sur le côté gauche de la page d’accueil, sélectionnez **Fichiers** pour afficher les fichiers qui composent les ressources techniques pour cette offre. Les réviseurs de Microsoft doivent avoir ajoutés des commentaires décrivant les problèmes critiques détectés. Dans l’exemple suivant, deux problèmes ont été détectés.

    [![Page d’accueil de la demande de tirage (pull request)](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Cliquez sur l’image pour la développer.*

1. Cliquez sur chaque nœud de commentaire dans l’arborescence de gauche pour accéder au commentaire dans le contexte du code environnant. Corrigez votre code source dans le projet de votre équipe pour corriger le problème décrit par le commentaire.

>[!Note]
>Vous ne pouvez pas modifier les ressources techniques de votre offre dans l’environnement Azure DevOps de l’équipe de révision. Pour les éditeurs, il s’agit d’un environnement en lecture seule pour le code source contenu. Toutefois, vous pouvez laisser les réponses aux commentaires au profit de l’équipe de révision de Microsoft.

   Dans l’exemple suivant, l’éditeur a passé en revue, corrigé et répondu au premier problème.

   ![Première correction et réponse au commentaire](./media/first-comment-reply.png)

## <a name="next-steps"></a>Étapes suivantes

Après avoir corrigé les problèmes critiques documentés dans les demandes de tirage de révision, vous devez [republier votre offre d’application Azure](./create-new-azure-apps-offer.md#publish).
