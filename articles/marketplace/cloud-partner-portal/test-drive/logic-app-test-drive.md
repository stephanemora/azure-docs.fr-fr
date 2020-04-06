---
title: Version d’évaluation de l’application logique | Place de marché Azure
description: Explique comment créer sa version d’évaluation qui se connectera à une instance Dynamics AX/CRM ou aux autres ressources qui ne se limitent pas à celles d’Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278380"
---
<a name="logic-app-test-drive"></a>Version d’évaluation d’une application logique
====================

Cet article est destiné aux éditeurs dont l’offre figure sur AppSource et qui souhaitent créer sa version d’évaluation qui se connectera à une instance Dynamics AX/CRM ou aux autres ressources qui ne se limitent pas à celles d’Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Comment générer une version d’évaluation d’une application logique
-----------------------------------

La documentation relative au [fonctionnement](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) et à [l’engagement client](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) des versions d’évaluation d’application logique se trouve toujours sur GitHub. Pour en savoir plus, consultez-la.

<a name="how-to-publish-a-test-drive"></a>Publication d’une version d’évaluation
---------------------------

Maintenant que vous avez créé votre version d’évaluation, cette section décrit en détail chacun des champs obligatoires pour pouvoir la publier.

![Activation de la fonctionnalité Version d’évaluation](./media/azure-resource-manager-test-drive/howtopub1.png)

Dans le premier champ qui est aussi le plus important, vous devez indiquer si vous souhaitez ou non procéder au test du formulaire avec l’ensemble des champs obligatoires présentés pour vous à remplir. Lorsque vous sélectionnez **Non**, le formulaire est désactivé, et si vous procédez à une republication avec la version d’évaluation désactivée, votre version d’évaluation est supprimée de la production.

*Remarque* : si des versions d’évaluation sont activement utilisées, elles continuent à être exécutées jusqu’à ce que leur session expire.

### <a name="details"></a>Détails

La prochaine section à remplir concerne les détails de votre version d’évaluation.

![Détails d’une version d’évaluation](./media/azure-resource-manager-test-drive/howtopub2.png)

**Description :** *[champ obligatoire]* c’est ici que vous rédigez la description principale du contenu de votre version d’évaluation. Le client consulte ici les scénarios qui sont couverts par la version d’évaluation de votre produit. 

**Manuel de l’utilisateur :** *[champ obligatoire]* il s’agit de la présentation approfondie de l’expérience de votre version d’évaluation. Le client l’ouvre et peut savoir exactement ce que vous voulez qu’il fasse dans sa version d’évaluation. Il est important que ce contenu soit facile à comprendre et à suivre ! (fichier PDF uniquement)

**Vidéo de démonstration de la version d’évaluation :** \[recommandée\] Comme le manuel de l’utilisateur, il est préférable d’inclure un didacticiel vidéo sur l’expérience de votre version d’évaluation. Le client la visionne avant et pendant la version d’évaluation et peut savoir exactement ce que vous voulez qu’il fasse dans sa version d’évaluation. Il est important que ce contenu soit facile à comprendre et à suivre !

- **Nom** : titre de votre vidéo.
- **Lien** : il doit s’agir d’une URL incorporée à partir de YouTube ou Vimeo. Un exemple illustrant l’obtention de l’URL incorporée figure ci-dessous :
- **Miniature** : ce doit être une image de haute qualité (533 x 324 pixels). Il est recommandé de prendre ici une capture d’écran de certaines parties de votre version d’évaluation.

Voici comment ces champs s’affichent pour votre client lors de son expérience de version d’évaluation.

![Apparence des champs d’une version d’évaluation](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configuration technique

La section suivante à remplir correspond à l’emplacement où vous configurez votre version d’évaluation d’application logique et où vous définissez comment les instances de votre version d’évaluation fonctionnent spécifiquement.

![Configuration technique de la version d’évaluation](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Région** -  *[champ obligatoire]* La région que vous sélectionnez correspond à la région dans laquelle vous souhaitez déployer la version d’évaluation de l’application logique.

    *Remarque :* si votre application logique compte des ressources personnalisées qui sont stockées dans une région, assurez-vous que celle-ci est sélectionnée ici. La meilleure façon de procéder consiste à **déployer entièrement votre application logique localement vers votre abonnement Azure dans le portail et à vérifier qu’elle fonctionne** avant de l’écrire ici.

- **Maximum Concurrent Test Drives (Nombre maximal de versions d’évaluation simultanées)**  -  *[champ obligatoire]* Nombre d’instances de version d’évaluation déjà déployées et en attente d’accès par région sélectionnée. Les clients peuvent accéder instantanément à ces versions d’évaluation au lieu d’attendre un déploiement.

    *Remarque :* si vous exécutez un webinaire/une classe où vous voulez que l’ensemble de vos étudiants (nombre N) prennent une version d’évaluation, il est recommandé de publier avec le nombre N d’instances à chaud, puis une fois la classe terminée, de republier vers le nombre normal d’instances à chaud.

- **Durée de la version d’évaluation (heures) :** *[champ obligatoire]* durée pendant laquelle la version d’évaluation reste active, exprimée en nombre d’heures. La version d’évaluation se termine automatiquement à la fin de cette période.

- **Nom du groupe de ressources Azure :** *[champ obligatoire]* écrivez le nom du groupe de ressources dans lequel vos versions d’évaluation d’application logique sont enregistrées.

- **Assign Logic App Name (Attribuer un nom d’application logique) :** *[champ obligatoire]* écrivez l’application logique qui est utilisée pour affecter un utilisateur dans la version d’évaluation avant que le client l’obtienne ; écrivez ici le nom de cette application logique. Assurez-vous que ce fichier est enregistré dans le groupe de ressources indiqué ci-dessus.

- **Deprovision Logic App Name (Déprovisionner le nom de l’application logique) :** *[champ obligatoire]* écrivez le nom de l’application logique pour déprovisionner toutes les ressources créées dans la version d’évaluation. Assurez-vous que ce fichier est enregistré dans le groupe de ressources indiqué ci-dessus.

- **Informations d’accès :** *[champ obligatoire]* lorsqu’un client obtient sa version d’évaluation, les informations d’accès lui sont présentées. Ces instructions sont destinées à partager les paramètres de sortie utiles à partir du modèle Resource Manager de votre version d’évaluation. Pour inclure les paramètres de sortie, utilisez des accolades doubles (par exemple, **{{nom_sortie}}** ) afin de les insérer correctement à l’emplacement. (Une mise en forme de chaîne HTML est recommandée ici pour l’affichage dans le serveur frontal).

### <a name="test-drive-deployment-subscription-details"></a>Détails d’abonnement du déploiement de la version d’évaluation

La dernière section à remplir vise à permettre le déploiement automatique de versions d’évaluation en connectant votre abonnement Azure et Azure Active Directory (AD).

![Détails d’abonnement du déploiement de la version d’évaluation](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID d’abonnement Azure :** *[champ obligatoire]* ce paramètre accorde l’accès aux services et au portail Azure. C’est dans l’abonnement que l’utilisation des ressources est signalée et que les services sont facturés. Si vous ne possédez pas encore un abonnement Azure **distinct** dédié aux versions d’évaluation, continuez et créez-en un. Pour trouver les ID d’abonnement Azure, connectez-vous au portail Azure et accédez aux abonnements dans le menu de gauche.
(Exemple : « a83645ac-1234-5ab6-6789-1h234g764ghty »)

![Abonnements Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID de locataire Azure AD :** *[champ obligatoire]* si vous disposez déjà d’un ID de locataire, vous pouvez le trouver ci-dessous dans Propriétés -\> ID de répertoire.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

Dans le cas contraire, créez un locataire dans Azure Active Directory.

![Écran des propriétés Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

!Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Locataires Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID d’application Azure AD :** *[champ obligatoire]* l’étape suivante consiste à créer et inscrire une nouvelle application. Nous allons utiliser cette application pour effectuer des opérations sur l’instance de votre version d’évaluation.

1. Accédez au répertoire que vous venez de créer ou au répertoire déjà existant et sélectionnez le répertoire Azure Active Directory dans le volet de filtre.
2. Recherchez « Inscriptions d’applications » et cliquez sur « Ajouter »
3. Entrez un nom d’application.
4. Sélectionnez le type « Application/API web ».
5. Indiquez une valeur quelconque dans le champ URL de connexion ; nous n’utiliserons pas ce champ.
6. Cliquez sur Créer.
7. Une fois l’application créée, accédez à Propriétés -\> Définir l’application comme étant mutualisée, puis appuyez sur Enregistrer.

Cliquez sur Enregistrer. La dernière étape consiste à récupérer l’ID de cette application inscrite et à le coller dans le champ de la version d’évaluation ici.

![ID d’application Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Étant donné que nous utilisons l’application à déployer vers l’abonnement, nous devons ajouter cette application en tant que contributeur dans l’abonnement. Voici les instructions pour le faire :

1. Accédez au panneau Abonnements et sélectionnez l’abonnement approprié que vous utilisez uniquement pour la version d’évaluation.
1. Cliquez sur **Contrôle d’accès (IAM)** .
1. Cliquez sur l’onglet **Attributions de rôles**.  ![Azure Active Directory, ajout d’un nouveau principal de contrôle d’accès](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Cliquez sur **Ajouter une attribution de rôle**.
1. Définissez le rôle **Contributeur**.
1. Tapez le nom de l’application Azure AD et sélectionnez cette dernière pour assigner le rôle.
    ![Autorisations Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Cliquez sur **Enregistrer**.

**Clé d’application Azure AD :** *[champ obligatoire]* le champ final consiste à générer une clé d’authentification. Sous Clés, ajoutez une description de la clé, définissez la durée afin qu’elle n’expire jamais, puis sélectionnez Enregistrer. Il est **important** d’éviter d’avoir une clé expirée, car cela arrête votre version d’évaluation en production. Copiez cette valeur et collez-la dans le champ obligatoire de votre version d’évaluation.

![Section Clés d’Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Vous ne pouvez pas utiliser Azure App Registration (Préversion), car elle ne génère pas de clé encodée en base64 pour le moment.


<a name="next-steps"></a>Étapes suivantes
----------

Maintenant que tous les champs de la version d’évaluation sont remplis, parcourez et **republiez** votre offre. Une fois que la version d’évaluation a réussi la certification, vous devez tester avec soin l’expérience utilisateur dans la **préversion** de votre offre. Lancez une version d’évaluation dans l’interface utilisateur et vérifiez que vos versions d’évaluation sont déployées correctement.

Il est important de noter que vous ne supprimez aucune partie de la version d’évaluation, car ces versions d’évaluation sont approvisionnées pour vos clients. Le service de version d’évaluation nettoie donc automatiquement ces groupes de ressources une fois qu’un client a terminé de l’utiliser.

Une fois que vous vous sentez à l’aise avec votre offre en préversion, il est temps de la **mettre en service**. Une fois que l’offre a été publiée, un processus de vérification finale de Microsoft permet de vérifier l’expérience de bout en bout complète. Si l’offre est rejetée pour une raison quelconque, nous envoyons une notification au support technique de votre offre, en expliquant les éléments à corriger.

Si vous avez d’autres questions, si vous cherchez des conseils de résolution des problèmes ou si vous souhaitez améliorer votre version d’évaluation, consultez l’article [FAQ,Troubleshooting, & Best Practices](./marketing-and-best-practices.md) (Forum aux questions, résolution des problèmes et meilleures pratiques).
