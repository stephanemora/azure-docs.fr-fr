---
title: Commentaires de révision sur les offres d’applications Azure - Place de marché commerciale Microsoft
description: Gérez les commentaires relatifs à votre offre d’application Azure émanant de l’équipe de révision de la Place de marché Microsoft Azure. Vous pouvez accéder aux commentaires dans Azure DevOps à l’aide de vos informations d’identification de l’Espace partenaires.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 06127505c992c842bde27508b69132b62c145cf0
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542473"
---
# <a name="handle-review-feedback-for-azure-application-offers"></a>Gérer les commentaires de révision des offres d’applications Azure

Cet article explique comment accéder aux commentaires de l’équipe de révision de la Place de marché Microsoft Azure dans [Azure DevOps](https://azure.microsoft.com/services/devops/). Si vous trouvez des problèmes critiques dans votre offre d’application Azure pendant l’étape de **révision Microsoft**, vous pouvez vous connecter à ce système pour afficher des informations détaillées sur ces problèmes (commentaires de révision). Après avoir résolu tous les problèmes, vous devez resoumettre votre offre pour continuer à la publier sur la Place de marché Azure. Le diagramme suivant illustre le lien entre ce processus de commentaires et le processus de publication.

![Processus de révision des commentaires](media/azure-app/review-feedback-process.png)

En règle générale, les problèmes de révision sont référencés sous forme de demande de tirage (PR, pull request). Chaque PR est liée à un élément Azure DevOps en ligne, qui contient des détails sur le problème. L’illustration suivante montre un exemple d’expérience de l’espace partenaires si des problèmes sont détectés pendant les révisions. 

![État de publication](media/azure-app/publishing-status.png)

La demande de tirage contenant des détails spécifiques sur la soumission sera mentionnée dans le lien « Afficher le rapport de certification ». Pour les cas complexes, les équipes de support et de révision peuvent aussi vous envoyer un e-mail.

## <a name="azure-devops-access"></a>Accès à Azure DevOps

Tous les utilisateurs ayant accès au rôle « développeur » dans l’espace partenaires auront accès à l’affichage des éléments PR référencés dans les commentaires de révision.

## <a name="reviewing-the-pull-request"></a>Révision de la demande de tirage

Utilisez la procédure suivante pour passer en revue les problèmes décrits dans la demande de tirage.

1. Dans la section **Révision Microsoft** du formulaire Étapes de publication, sélectionnez le lien d’une demande de tirage pour lancer votre navigateur et accéder à la page **Vue d’ensemble** (accueil) pour cette PR. L’image suivante illustre un exemple de page d’accueil de problème critique pour l’exemple d’offre d’application Contoso. Cette page contient des informations récapitulatives utiles sur les problèmes de révision trouvés dans l’application Azure.

    [![Page d’accueil de la demande de tirage (pull request)](media/azure-app/pr-home-page-thumb.png)](media/azure-app/pr-home-page.png)
    <br/> *Cliquez sur cette image pour la développer.*

1. (Facultatif) Sur le côté droit de la fenêtre, dans la section **Stratégies**, sélectionnez le message du problème (dans cet exemple : **Échec de la validation de la stratégie**) pour examiner les détails du problème, notamment les fichiers journaux associés. Les erreurs sont généralement affichées en bas des fichiers journaux.

1. Dans le menu sur le côté gauche de la page d’accueil, sélectionnez **Fichiers** pour afficher les fichiers qui composent les ressources techniques pour cette offre. Les réviseurs de Microsoft doivent avoir ajoutés des commentaires décrivant les problèmes critiques détectés. Dans l’exemple suivant, deux problèmes ont été détectés.

    [![Capture d’écran mettant en évidence Fichiers et les deux problèmes qui ont été découverts.](media/azure-app/pr-files-page-thumb.png)](media/azure-app/pr-files-page.png)
    <br/> *Cliquez sur cette image pour la développer.*

1. Sélectionnez chaque nœud de commentaire dans l’arborescence de gauche pour accéder au commentaire dans le contexte du code environnant. Corrigez votre code source dans le projet de votre équipe pour corriger le problème décrit par le commentaire.

>[!Note]
>Vous ne pouvez pas modifier les ressources techniques de votre offre dans l’environnement Azure DevOps de l’équipe de révision. Pour les éditeurs, il s’agit d’un environnement en lecture seule pour le code source contenu. Toutefois, vous pouvez laisser les réponses aux commentaires au profit de l’équipe de révision de Microsoft.

   Dans l’exemple suivant, l’éditeur a passé en revue, corrigé et répondu au premier problème.

   ![Première correction et réponse au commentaire](media/azure-app/first-comment-reply.png)

## <a name="next-steps"></a>Étapes suivantes

- Après avoir corrigé les problèmes critiques documentés dans les demandes de tirage de révision, vous devez [republier votre offre d’application Azure](azure-app-offer-setup.md).
