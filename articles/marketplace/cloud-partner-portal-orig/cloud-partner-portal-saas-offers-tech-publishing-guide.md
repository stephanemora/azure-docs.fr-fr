---
title: Guide de publication technique des applications SaaS | Microsoft Docs
description: Explique comment publier des offres d’applications SaaS sur la place de marché appropriée.
services: Azure, Marketplace, Cloud Partner Portal,
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 7430e57ceaec8a280c17f1276d503919a9703182
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228893"
---
<a name="saas-application-technical-publishing-guide"></a>Guide de publication technique des applications SaaS
===========================================

Bienvenue dans le Guide de publication technique des applications SaaS. Ce guide a été conçu pour aider les éditeurs candidats et existants à répertorier leurs applications et services dans AppSource ou sur la Place de marché Microsoft Azure en utilisant l’offre d'applications SaaS.

Pour obtenir une vue d’ensemble de toutes les autres offres de la Place de marché, reportez-vous au [Guide de publication relatif à la Place de marché](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Conditions préalables à la publication d’une application SaaS
-------------------------------------------------

Le portail de publication fournit un accès basé sur le rôle qui permet à plusieurs personnes de collaborer à la publication d’une offre. Pour plus d’informations, consultez la rubrique [Gérer les utilisateurs](./cloud-partner-portal-manage-users.md). 

Pour publier une offre au nom d’un compte de publication, une personne dotée du rôle de *Propriétaire* doit accepter les [Conditions d’utilisation](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [Déclaration de confidentialité Microsoft](https://www.microsoft.com/privacystatement/default.aspx) et l'[Accord du programme Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Création d'une offre
-----------------

Cette section décrit le processus de création d’une offre d’application SaaS.

![Vue d’ensemble de l’offre SaaS](media/cpp-creating-saas-offers/saas-offer-overview.png)

L’offre d'application SaaS se décompose en cinq sections, décrites dans le tableau suivant :

| **Section Offre**  | **Description**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Paramètres de l’offre     | Permet de définir un nom et un ID uniques pour l’application SaaS                                                                         |
| Informations techniques     | Permet de configurer le type de solution SaaS, et fournit les détails de connexion de votre application                            |
| Version d’évaluation         | Section facultative qui vous permet de définir un service grâce auquel les clients pourront tester votre offre avant de l’acheter. |
| Informations sur les vitrines | Contient les sections Marketing, Informations légales, Gestion des prospects et Listing :   <br/> - La section Marketing vous permet d’entrer la description et les logos requis pour que l’offre apparaisse correctement sur le portail des utilisateurs de la Place de marché.  <br/> - La section Gestion des prospects vous permet de définir l'emplacement de redirection des prospects générés sur le portail des utilisateurs finaux de la Place de marché Microsoft Azure.  <br/> - La section Informations légales vous permet d’entrer la Politique de confidentialité et les Conditions d’utilisation.  |
| Contact            | Vous permet d’entrer les coordonnées du service de support associé à l’offre.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Création d'une offre

Après vous être connecté au portail Cloud Partner, sélectionnez **Nouvelle offre** sur la barre de menus de gauche. Le menu des offres disponibles s'affiche. L’illustration suivante propose un exemple de ces offres :

![Nouvelle offre SaaS](media/cpp-creating-saas-offers/saas-new-offer.png)

Sélectionnez l’offre pour laquelle vous avez été approuvé dans la liste. Un nouveau formulaire d’offre s’ouvre.

![Formulaire de nouvelle offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-2.png)

Le tableau suivant décrit les champs de l'offre :

| **Champs de l’offre** | **Description**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| ID de l’offre         | Identificateur unique de l’offre au sein du profil d’éditeur. Cet ID est visible dans les URL des produits et sur les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Notez que ce champ est verrouillé une fois l’offre publiée. Par exemple, si l’éditeur Contoso publie une offre avec l’ID d’offre sample-vm, celle-ci apparaît sur la Place de marché Microsoft Azure sous la forme suivante : [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| ID de l’éditeur     | L'ID d'éditeur est votre identificateur unique sur la Place de marché. Votre ID d'éditeur doit être associé à toutes vos offres. Une fois l’offre enregistrée, l'ID d'éditeur n'est pas modifiable.                                                                                       |
| NOM             | Ceci est le nom d’affichage de votre offre. Il s'agit du nom qui apparaît sur la Place de marché Microsoft Azure et sur le portail Azure. Il ne peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N'incluez pas ici le nom de votre entreprise, sauf s'il s'agit du nom sous lequel l'offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.             |
|  |  |

Cliquez sur **Enregistrer** pour enregistrer votre progression. La section suivante explique comment ajouter des plans à votre offre.


### <a name="technical-information"></a>Informations techniques

La section Informations techniques vous permet d’entrer les informations suivantes :

![Informations techniques sur l'offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

La décision la plus importante consiste à déterminer si vous disposerez d'un listing SaaS ou d'un commerce activé. Si vous disposez d'un listing SaaS, vous devrez faire un choix parmi les options suivantes :

-   Gratuit : fournissez l'URL à partir de laquelle vos clients auront accès à votre application SaaS.
-   Version d’évaluation gratuite : fournissez l'URL à partir de laquelle vos clients pourront exécuter la version d’évaluation de votre application SaaS avant de l'acheter.
-   Me contacter : cette option, uniquement disponible si vous disposez d'un système de gestion des prospects connecté, permet aux clients de demander à être contactés. Le prospect est alors partagé avec vous.

Si vous disposez d'une application SaaS sur la Place de marché Microsoft Azure et souhaitez activer le commerce par le biais de transactions Microsoft, sélectionnez **Vendre via Azure**.  
Pour plus d’informations sur la connexion de votre application SaaS, consultez la rubrique [SaaS - Vendre via Azure](./cloud-partner-portal-saas-offer-subscriptions.md).


### <a name="test-drive"></a>Version d’évaluation

Nous vous recommandons de proposer une option d’évaluation à vos clients pour leur permettre d'acheter votre application en toute confiance. Parmi les options d’évaluation disponibles, la version d’évaluation est la plus efficace pour générer des prospects pertinents et les transformer en clients.

La version d’évaluation permet aux clients d'essayer les principales fonctionnalités de votre produit et d'en apprécier les avantages grâce à un scénario d’implémentation réel.

![Version d'évaluation d'une offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>Fonctionnement d'une version d'évaluation

Un client potentiel accède à votre application après avoir lancé une recherche sur la Place de marché. Le client se connecte et accepte les conditions d’utilisation. À ce stade, le client reçoit votre environnement préconfiguré afin de l'essayer pendant un nombre d’heures défini, et vous disposez d'un prospect pertinent auquel vous pouvez donner suite.

![Version d'évaluation d'une offre SaaS 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Quelle que soit la complexité votre application, votre version d'évaluation Microsoft vous permet de lui donner vie pour la présenter au client. Trois types de versions d’évaluation sont disponibles en fonction du type de produit, du scénario et de la place de marché où vous vous trouvez.

-   **Azure Resource Manager** : une version d'évaluation Azure ARM est un modèle de déploiement contenant toutes les ressources Azure qui constituent une solution générée par l'éditeur. Les produits adaptés à ce type de version d’évaluation sont ceux qui utilisent uniquement des ressources Azure.
-   **Application logique** : une version d'évaluation d'application logique est un modèle de déploiement conçu pour englober toutes les architectures de solutions complexes. Les applications Dynamics et les produits personnalisés doivent tous utiliser ce type de version d’évaluation.
-   **Power BI** : une version d'évaluation Power BI est composée d'un lien incorporé vers un tableau de bord personnalisé. Tous les produits dotés d'un visuel Power BI interactif doivent utiliser ce type de version d’évaluation. Il vous suffit de charger votre URL Power BI incorporée.

Les principales étapes de publication à suivre pour ajouter une version d'évaluation sont les suivantes :

1.  Définir votre scénario de version d'évaluation
2.  Créer et/ou modifier votre modèle Azure Resource Manager
3.  Créer le manuel pas à pas de votre version d’évaluation
4.  Republier votre offre

Pour plus d'informations, consultez la rubrique [Version d'évaluation](./what-is-test-drive.md).


### <a name="storefront-details"></a>Informations sur les vitrines

Les deux premières sections d’un résumé et d'une description de votre application SaaS doivent être fournies.

![Détails de la vitrine d'une offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

Le tableau suivant décrit les détails de la vitrine de l'offre :

| **Champs de l’offre**        | **Description**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Résumé de l’offre           | Résumé de la proposition de valeur de votre offre. Celui-ci apparaîtra sur la page de recherche de votre offre. Elle ne doit pas dépasser 100 caractères.   |
| Description de l’offre       | Description qui apparaîtra sur la page des détails de votre application. Celle-ci ne doit pas dépasser les 1 300 caractères. *Remarque :* pour un contenu plus présentable, ce champ accepte les contenus HTML avec des balises telles que &ltp\>, &lth1\>, &lth2\>, &ltli\>, etc. L'équipe du portail de publication ajoutera prochainement une fonctionnalité qui permettra à l'utilisateur d'afficher un aperçu des détails de sa vitrine de manière itérative, pour un contenu plus présentable. En attendant, vous pouvez utiliser un outil HTML en ligne et en temps réel tel que http://htmledit.squarefree.com pour afficher un aperçu de votre description. |
| Secteurs d’activité              | Sélectionnez les secteurs d'activité le mieux adaptés à votre offre. Si votre application convient à plusieurs secteurs d’activité, vous pouvez en sélectionner deux. |
| Catégories suggérées    | Sélectionnez les catégories les mieux adaptées à votre offre. Vous pouvez en sélectionner trois.     |
| Version de l’application     | Entrez le numéro de version de votre application                                                                |
| Mots clés de recherche (3 max.) | Entrez un maximum de trois mots clés que les clients pourront utiliser pour rechercher votre application sur le site web de la vitrine de la Place de marché. |
|  |  |


### <a name="marketing-artifacts"></a>Artefacts marketing

La section Artefacts marketing vous permet de définir les ressources marketing de la Place de marché Microsoft Azure, comme les logos, les vidéos, les captures d’écran et les documents :

![Artefacts marketing d'une offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

Le tableau suivant décrit les champs Marketing :

| **Champs de l’offre** | **Description**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Logos            | Si vous disposez d'une application SaaS **Vendre via Azure**, vous devez fournir tous les logos. Si vous disposez simplement d'un listing, seuls 2 logos sont requis. Pour tous les logos chargés sur le portail Microsoft Cloud Partner, suivez les instructions ci-dessous :     <br/> - Limitez le nombre de couleurs primaires et secondaires sur votre logo. Le design Azure a une palette de couleurs simple.     <br/> - Évitez d’utiliser du noir ou du blanc comme couleur d’arrière-plan pour votre logo. Les couleurs de thème du portail Azure sont le noir et le blanc. Utilisez plutôt des couleurs qui mettent vos logos en évidence sur le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples. Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont ni noirs ni blancs ni bleus.     <br/> - N'utilisez pas d’arrière-plan dégradé sur le logo.     <br/> - Évitez de placer du texte, même s’il s’agit du nom de votre société ou de votre marque, sur le logo. L'apparence de votre logo doit être « plate » et éviter les dégradés.    <br/> - Le logo ne doit pas être étiré.                   |
| vidéos           | Vous permet d’ajouter des liens d'accès à des vidéos relatives à votre offre. Vous pouvez utiliser des liens de vidéos YouTube et/ou Vimeo, qui seront présentées aux clients en même temps que votre offre. Vous devez également entrer une miniature de la vidéo, avec une image PNG de 1280 x 720 pixels. Vous pouvez ajouter un maximum de quatre vidéos par offre. |
| Documents        | Vous permet d’ajouter des documents marketing à votre offre. Tous les documents doivent être au format PDF, et vous pouvez ajouter un maximum de trois documents par offre.                                                                                                                                                      |
| Captures d’écran.      | Vous permet d’ajouter des captures d’écran de votre offre. Vous pouvez ajouter un maximum de cinq captures d’écran par offre. La taille maximum est de 1280 x 720 pixels par image.                                                                                                                                             |
| Liens utiles     | Vous permet d'ajouter des URL externes pour que votre offre pointe vers des diagrammes d'architecture ou d'autres sites susceptibles d'intéresser les clients.                                                                                                                                                              |
|  |  |

L’illustration suivante montre comment les informations apparaissent dans les résultats de la recherche de la Place de marché :

![Informations marketing sur l’éditeur d'une offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

L’illustration suivante montre comment l’offre apparaît sur la Place de marché lorsqu'un client clique sur la plus petite vignette de l'offre :

![Informations marketing sur l’éditeur d'une offre SaaS 2](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Gestion des prospects et Informations légales

La section Informations légales vous permet de définir la documentation juridique de l’offre.
Deux documents juridiques sont requis pour chaque offre d’application SaaS : la Politique de confidentialité et les Conditions d’utilisation. Pour plus d'informations, consultez la rubrique  
[Configurer des prospects](./cloud-partner-portal-get-customer-leads.md).

![Informations légales marketing sur l'offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

Le tableau suivant décrit les propriétés de la section Informations légales :

| **Champs de l’offre**   | **Description**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| URL de la politique de confidentialité | URL de la politique de confidentialité de votre société.                                                       |
| Conditions d’utilisation       | Conditions d’utilisation de votre offre. Tapez ou copiez-collez les conditions d’utilisation ici. L’utilisation du format HTML de base est autorisée. Par conséquent, ce champ accepte les contenus HTML avec des balises telles que &ltp\>, &lth1\>, &lth2\>, &ltli\>, etc. *Important* : nous vous recommandons vivement de relire et d’afficher un aperçu du contenu HTML que vous avez créé dans un navigateur avant de soumettre l’offre. |
|  |  |


### <a name="contact-information"></a>Informations de contact

Dans cette section, vous entrerez les coordonnées du service de support qui prendra en charge les clients utilisant cette offre.
Cette section se décompose en trois zones principales : Contact ingénierie, Contact support et URL de support :

![Informations de contact marketing de l'offre SaaS](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Contact**         | **Description**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Contact ingénierie | Indiquez le nom, l'adresse e-mail et le numéro de téléphone d’un ingénieur que Microsoft pourra contacter pour toute question de support ou tout problème métier. |
| Contact support     | Indiquez le nom, l'adresse e-mail, le numéro de téléphone et l'URL de support que vos clients pourront utiliser pour leurs demandes de support.                  |
|  |  |


Lorsque votre offre est prête et que vous appuyez sur Publier, elle suit le processus de certification. Nous testons votre application SaaS par le biais d'une vérification manuelle en testant votre URL si vous disposez d'un listing, ou les points de terminaison si Vendre via Azure est sélectionné. Au cours de cette approbation manuelle, nous décidons également de la vitrine qui convient le mieux à votre application (AppSource, Place de marché Microsoft Azure ou les deux).

<a name="updating-the-offer"></a>Mise à jour de l’offre
------------------

Différents types de mises à jour peuvent s'appliquer à votre offre à l'issue de la publication et de la mise en ligne de celle-ci. Toute modification apportée à la nouvelle version de votre offre doit être enregistrée et republiée pour apparaître sur la Place de marché.

<a name="deleting-an-existing-offer"></a>Suppression d’une offre existante
--------------------------

Vous pouvez décider de supprimer votre offre de la Place de marché. La suppression de l’offre permet de faire en sorte que les nouveaux clients ne puissent plus acheter ou déployer votre offre, mais elle n’a aucun impact sur les clients existants. L’arrêt de l’offre est le processus d’arrêt de l’accord de service et/ou de licence entre vos clients actuels et vous.

L’aide et les stratégies relatives à l’arrêt et à la suppression des offres sont régies par le Contrat d’éditeur de la Place de marché Microsoft (voir section 7) et les Stratégies de participation (voir section 6.2). Cette section fournit des informations sur les différents scénarios de suppression pris en charge et les étapes à suivre correspondantes.

### <a name="delete-the-live-offer"></a>Supprimer une offre en direct

Différents aspects doivent être pris en compte en cas de demande de suppression d’une offre en direct. Suivez les étapes ci-dessous afin de bénéficier de l’aide de l’équipe de support pour supprimer une offre en ligne de la Place de marché Azure :

1.  Ouvrez un ticket de support en utilisant ce lien
2.  Dans la liste Type de problème, sélectionnez **Gestion des offres** puis, dans la liste Catégories, sélectionnez **Modification d’une offre et/ou d’une référence SKU déjà en production**.
3.  Envoyez la demande.

L’équipe de support vous guidera tout au long du processus de suppression de l’offre.

> [!NOTE] 
> La suppression d’une offre n’a aucune répercussion sur les acquisitions actuelles de cette offre. Ces acquisitions clients continueront à fonctionner comme avant.
Toutefois, aucune nouvelle acquisition de l’offre ne sera possible après la suppression.
