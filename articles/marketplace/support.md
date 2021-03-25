---
title: Obtenir de l’aide pour le programme du marketplace commercial dans Espace partenaires
description: Découvrez les options de support pour le programme du marketplace commercial dans Espace partenaires, y compris la manière de déposer une demande de support.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879239"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Support technique pour le programme Place de marché commerciale dans l’Espace partenaires

Microsoft prend en charge un large éventail de produits et services. Le fait de trouver l’équipe de support appropriée est primordial pour garantir une réponse appropriée et en temps voulu. Parmi les scénarios suivants, lequel devrait vous aider à acheminer votre demande vers l’équipe appropriée ?

- En tant qu’éditeur, si un client vous pose une question, demandez-lui de faire appel au support via les liens de support dans le  [portail Azure](https://portal.azure.com/).
- Si vous êtes un éditeur et que vous avez détecté un problème de sécurité sur une application exécutée sur Azure, consultez [Enregistrer un ticket de support d’événement de sécurité](../security/fundamentals/event-support-ticket.md). Les éditeurs doivent signaler tout événement de sécurité suspect, y compris les incidents et failles de sécurité, en relation avec leurs logiciels et offres de services proposés dans la Place de marché Azure et ce, dans les meilleurs délais.
- Si vous êtes un éditeur et que vous avez une question relative à votre application ou votre service, examinez les options de support suivantes.

## <a name="get-help-or-open-a-support-ticket"></a>Obtenir de l’aide ou ouvrir un ticket de support

1. Connectez-vous avec votre compte professionnel. Si vous ne l’avez pas encore fait, vous devez [créer un compte Espace partenaires](partner-center-portal/create-account.md).

1. Dans le menu en haut à droite de la page, sélectionnez l’icône **Support**. Le volet **Aide et support** s’affiche sur le côté droit de la page.

1. Pour obtenir de l’aide concernant le marketplace commercial, sélectionnez **Marketplace commercial**.

   ![Menu déroulant Support](./media/support/commercial-marketplace-support-pane.png)

1. Dans la zone **Résumé du problème**, entrez une brève description du problème.

1. Dans la zone **Type de problème**, effectuez l’une des actions suivantes :

    - **Option 1** : Entrer des mots clés tels que : marketplace, application Azure, offre SaaS, gestion des comptes, gestion des prospects, problème de déploiement, paiement ou migration de l’offre de co-vente. Sélectionnez ensuite un type de problème dans la liste recommandée qui s’affiche.

    - **Option 2** : Sélectionnez **Parcourir les rubriques** dans la liste **Catégorie**, puis sélectionnez **Marketplace commercial**. Sélectionnez ensuite la **rubrique** et la **sous-rubrique** appropriées.

1. Une fois que vous avez trouvé la rubrique de votre choix, sélectionnez **Examiner les solutions**.

    ![Étape suivante](./media/support/next-step.png)

Les options suivantes s’affichent :

- Pour sélectionner une autre rubrique, cliquez sur **Sélectionner un autre problème**.
- Pour vous aider à résoudre le problème, examinez les étapes et les documents recommandés, le cas échéant.

    ![Solutions recommandées](./media/support/recommended-solutions.png)

Si vous ne trouvez pas votre réponse dans l’aide autonome, sélectionnez **Fournir les détails relatifs au problème**. Remplissez tous les champs obligatoires pour accélérer le processus de résolution, puis sélectionnez **Envoyer**.

>[!Note]
>Si vous n’êtes pas connecté à Espace partenaires, vous devrez peut-être vous connecter avant de pouvoir créer un ticket.

## <a name="track-your-existing-support-requests"></a>Suivre vos demandes de support existantes

Pour passer en revue vos tickets ouverts et fermés, dans le menu de navigation de gauche, sélectionnez **Marketplace commercial** > **Support**.

## <a name="record-issue-details-with-a-har-file"></a>Enregistrer les détails d’un problème avec un fichier HAR

Pour aider les agents à résoudre votre problème, envisagez de joindre un fichier au format d’archive HTTP (HAR) à votre ticket de support. Les fichiers HAR sont des journaux de requêtes réseau dans un navigateur web.

> [!WARNING]
> Les fichiers HAR peuvent enregistrer des données sensibles sur votre compte Espace partenaires.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge ou Google Chrome

Pour générer un fichier HAR à l’aide de **Microsoft Edge** ou de **Google Chrome** :

1. Accédez à la page web dans laquelle vous êtes confronté au problème.
2. Dans l’angle supérieur droit de la fenêtre, sélectionnez l’icône représentant des points de suspension, puis **Autres outils** > **Outils de développement**. Vous pouvez appuyer sur F12 comme raccourci.
3. Dans le volet Outils de développement, sélectionnez l’onglet **Réseau**.
4. Sélectionnez **Stop recording network log** (Arrêter l’enregistrement du journal réseau) et **Clear** (Effacer) pour supprimer les journaux existants. L’icône d’enregistrement devient grise.

    ![Comment supprimer des journaux existants dans Microsoft Edge ou Google Chrome](media/support/chromium-stop-clear-session.png)

5. Sélectionnez **Record network log** (Enregistrer le journal réseau) pour démarrer l’enregistrement. Lorsque vous démarrez l’enregistrement, l’icône d’enregistrement devient rouge.

    ![Comment démarrer l’enregistrement dans Microsoft Edge ou Google Chrome](media/support/chromium-start-session.png)

6. Reproduisez le problème que vous souhaitez résoudre.
7. Une fois que vous avez reproduit le problème, sélectionnez **Stop recording network log** (Arrêter l’enregistrement du journal réseau).
8. Sélectionnez l’option **Export HAR** (Exporter en tant que HAR), marquée d’une icône représentant une flèche vers le bas, puis enregistrez le fichier.

    ![Comment exporter un fichier HAR dans Microsoft Edge ou Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Pour générer un fichier HAR à l’aide de **Mozilla Firefox** :

1. Accédez à la page web dans laquelle vous êtes confronté au problème.
1. Dans l’angle supérieur droit de la fenêtre, sélectionnez l’icône représentant des points de suspension, puis **Web Developer** > **Changer les outils**. Vous pouvez appuyer sur F12 comme raccourci.
1. Sélectionnez l’onglet **Réseau**, puis **Effacer** pour supprimer les journaux existants.

    ![Comment supprimer des journaux existants dans Mozilla Firefox](media/support/firefox-clear-session.png)

1. Reproduisez le problème que vous souhaitez résoudre.
1. Une fois que vous avez reproduit le problème, sélectionnez **Exporter/Importer HAR** > **Tout enregistrer sous HAR**.

    ![Comment exporter un fichier HAR dans Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Pour générer un fichier HAR à l’aide de **Safari** :

1. Activez les outils de développement dans Safari : sélectionnez **Safari** > **Préférences**. Sélectionnez l’onglet **Avancé**, puis **Afficher le menu Développement dans la barre des menus**.
1. Accédez à la page web dans laquelle vous êtes confronté au problème.
1. Sélectionnez **Développer**, puis sélectionnez **Afficher Web Inspector**.
1. Sélectionnez l’onglet **Network** (Réseau), puis **Clear Network Items** (Effacer les éléments réseau).

    ![Comment supprimer des journaux existants dans Safari](media/support/safari-clear-session.png)

1. Reproduisez le problème que vous souhaitez résoudre.
1. Une fois que vous avez reproduit le problème, sélectionnez **Exporter** et enregistrez le fichier.

    ![Comment exporter un fichier HAR dans Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour une offre existante dans la Place de marché commerciale](partner-center-portal/update-existing-offer.md)