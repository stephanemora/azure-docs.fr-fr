---
title: Gestion des commentaires de révision sur les applications Azure | Place de marché Azure
description: Explique comment utiliser Azure DevOps pour gérer les commentaires de révision pour les offres d’application Azure pour la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/05/2019
ms.author: dsindona
ms.openlocfilehash: b5f290bae908ac753801eef2c9b8394ca1bb7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285297"
---
# <a name="handling-review-feedback"></a>Gestion des commentaires de révision

Cet article explique comment accéder à l’environnement Azure DevOps utilisé par l’équipe de révision de la Place de marché Microsoft Azure.  Si vous trouvez des problèmes critiques dans votre offre d’application Azure pendant l’étape de **révision Microsoft**, vous pouvez vous connecter à ce système pour afficher des informations détaillées sur ces problèmes (commentaires de révision).  Après avoir résolu tous ces problèmes, vous devez renvoyer votre offre pour continuer à la publier sur la Place de marché Azure.  Le diagramme suivant illustre le lien entre ce processus de commentaires et le processus de publication.

![Étapes de publication avec commentaires Azure DevOps](./media/pub-flow-vsts-access.png)

En règle générale, les problèmes de révision sont référencés sous forme de demande de tirage (pull request).  Chaque demande de tirage est liée à un élément [Azure DevOps](https://azure.microsoft.com/services/devops/) (précédemment nommé Visual Studio Team Services (VSTS)) en ligne, qui contient des détails sur le problème.  L’image suivante affiche un exemple de référence de demande de tirage de révision.  Pour les cas complexes, les équipes de support et de révision peuvent aussi vous envoyer un e-mail. 

![Onglet Status affichant les commentaires de révision](./media/status-tab-ms-review.png)


## <a name="azure-devops-access"></a>Accès à Azure DevOps

Pour voir les éléments de demande de tirage référencés dans les commentaires de révision, les éditeurs doivent d’abord se voir accorder l’autorisation appropriée.  Sinon, les nouveaux éditeurs reçoivent une page de réponse `401 - Not Authorized` lorsqu’ils tentent d’afficher les demandes de tirage.  Pour demander l’accès à ce référentiel Azure DevOps, procédez comme suit :

1. Collectez les informations suivantes :
    - Vos nom et ID d’éditeur
    - Type d’offre (application Azure), nom et ID de référence SKU de l’offre
    - Lien de la demande de tirage, par exemple : `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>` Cette URL peut être récupérée à partir du message de notification ou de l’adresse de la page de réponse 401.
    - Les adresses e-mail des personnes de votre organisation de publication auxquelles vous souhaitez accorder l’accès.  Cette liste doit inclure les adresses de propriétaire que vous avez fournies lors de votre inscription en tant qu’éditeur sur le Portail Cloud Partner.
2. Créez un incident de support.  Dans la barre de titre du Portail Cloud Partner, sélectionnez le bouton **Help**, puis choisissez **Support** dans le menu.  Votre navigateur web par défaut doit se lancer et accéder à la page de nouvel incident de support Microsoft.  (Vous devrez peut-être vous connecter préalablement.)
3. Spécifiez **marketplace onboarding** (Intégration à la place de marché) pour **Problem type** et **Access problem** pour **Category**, puis sélectionnez **Start request** (Démarrer la demande).

    ![Catégorie de ticket de support](./media/support-incident1.png)

4. Dans la page **Step 1 of 2**, fournissez vos informations de contact et sélectionnez **Continue**.
5. Dans la page **Step 2 of 2**, spécifiez un titre d’incident (par exemple `Request Azure DevOps access`) et indiquez les informations que vous avez recueillies à la première étape (ci-dessus).  Lisez et acceptez le contrat, puis sélectionnez **Submit**.

Si la création de l’incident a réussi, une page de confirmation s’affiche.  Enregistrez les informations de confirmation sur cette page à titre de référence.  L’équipe de support Microsoft doit répondre à votre demande d’accès sous quelques jours ouvrés.


## <a name="reviewing-the-pull-request"></a>Révision de la demande de tirage 

Utilisez la procédure suivante pour passer en revue les problèmes décrits dans la demande de tirage.

1. Dans la section **Révision Microsoft** du formulaire **Étapes de publication**, cliquez sur le lien d’une demande de tirage pour lancer votre navigateur et accéder à la page **Vue d’ensemble** (accueil) pour cette demande de tirage.  L’image suivante illustre un exemple de page d’accueil de problème critique pour l’exemple d’offre d’application Contoso.  Cette page contient des informations récapitulatives utiles sur les problèmes de révision trouvés dans l’application Azure.  

    [![Page d’accueil de la demande de tirage (pull request)](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Cliquez sur l’image pour la développer.*
    
2. (Facultatif) Sur le côté droit de la fenêtre, dans la section **Stratégies**, cliquez sur le message du problème (dans cet exemple : **Échec de la validation de la stratégie**) pour examiner les détails du problème, notamment les fichiers journaux associés.  Les erreurs sont généralement affichées en bas des fichiers journaux.

3. Dans le menu sur le côté gauche de la page d’accueil, sélectionnez **Fichiers** pour afficher les fichiers qui composent les ressources techniques pour cette offre.  Les réviseurs de Microsoft doivent avoir ajoutés des commentaires décrivant les problèmes critiques détectés.  Dans l’exemple suivant, deux problèmes ont été détectés. 

    [![Page d’accueil de la demande de tirage (pull request)](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Cliquez sur l’image pour la développer.*

4. Cliquez sur chaque nœud de commentaire dans l’arborescence de gauche pour accéder au commentaire dans le contexte du code environnant.  Corrigez votre code source dans le projet de votre équipe pour corriger le problème décrit par le commentaire.

> [!Note]
> Vous ne pouvez pas modifier les ressources techniques de votre offre dans l’environnement Azure DevOps de l’équipe de révision.  Pour les éditeurs, il s’agit d’un environnement en lecture seule pour le code source contenu.  Toutefois, vous pouvez laisser les réponses aux commentaires au profit de l’équipe de révision de Microsoft.

   Dans l’exemple suivant, l’éditeur a passé en revue, corrigé et répondu au premier problème.

   ![Première correction et réponse au commentaire](./media/first-comment-reply.png)


## <a name="next-steps"></a>Étapes suivantes

Après avoir corrigé les problèmes critiques documentés dans les demandes de tirage de révision, vous devez [republier votre offre d’application Azure](./cpp-publish-offer.md).
