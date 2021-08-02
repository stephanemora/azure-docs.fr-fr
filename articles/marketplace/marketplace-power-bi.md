---
title: Guide de planification destiné aux offres d’applications Power BI dans Microsoft AppSource (Place de marché Azure)
description: Cet article est un guide de planification destiné aux offres d’applications Power BI dans Microsoft AppSource (Place de marché Azure)
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 05/26/2021
ms.openlocfilehash: 7f21e3b7e95bf4d735d394a9dfebe04023218faa
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112061233"
---
# <a name="plan-a-power-bi-app-offer"></a>Planifier une offre d’application Power BI

Cet article met l’accent sur le contenu et les exigences à satisfaire pour publier une application Power BI sur Microsoft [AppSource](https://appsource.microsoft.com/). Une application Power BI regroupe le contenu personnalisable, notamment les jeux de données, les rapports et les tableaux de bord. Vous pouvez ensuite utiliser l’application avec d’autres plateformes Power BI à l’aide d’AppSource, effectuer les ajustements et les personnalisations autorisés par le développeur et la connecter à vos propres données.

Avant de commencer, suivez les liens ci-dessous qui proposent des modèles, des conseils et des exemples :

- [Créer une application dans Power BI](/power-bi/service-template-apps-create)
- [Conseils pour créer une application dans Power BI](/power-bi/service-template-apps-tips)
- [Exemples](/power-bi/service-template-apps-samples)


## <a name="publishing-benefits"></a>Avantages liés à la publication

Avantages de la publication sur le marketplace commercial :

- Promouvez votre entreprise en utilisant la marque Microsoft.
- Touchez potentiellement plus de 100 millions d’utilisateurs Microsoft 365 et Dynamics 365 sur AppSource ainsi que plus de 200 000 organisations via la Place de marché Microsoft Azure.
- Recevez des prospects de grande qualité à partir de ces marketplaces.
- Bénéficiez d’une promotion de vos services assurée par les équipes de terrain et de télévente de Microsoft.

## <a name="overview"></a>Vue d’ensemble

:::image type="content" source="media/power-bi/power-bi-app-publishing-steps.png" alt-text="Vue d’ensemble des étapes de publication d’une offre d’application Power BI." border="false":::

Si vous êtes prêt à créer votre offre, consultez [Étapes suivante](#next-steps) ci-dessous. Dans le cas contraire, poursuivez votre lecture pour vous assurer que vous êtes prêt avant d’entamer le processus de création de l’offre.

Voici les principales étapes de publication abordées dans les rubriques suivantes :

1. Créez votre application dans Power BI. Vous recevrez un lien d’installation de package, qui est la ressource technique principale de l’offre. Envoyez le package de test à l’équipe de préproduction avant de créer l’offre dans l’Espace partenaires. Pour plus d’informations, consultez [En quoi consistent les applications Power BI ?](/power-bi/service-template-apps-overview).
2. Ajoutez les documents marketing, tels que le nom, la description et les logos officiels.
3. Incluez les documents juridiques et de support de l’offre, tels que les conditions d’utilisation, la politique de confidentialité, la politique de support et l’aide de l’utilisateur.
4. Créer l’offre – Utilisez l’Espace partenaires pour modifier les détails, notamment la description de l’offre, les supports marketing, les mentions légales, les informations de support et les spécifications des ressources.
5. Envoyez-la pour publication.
6. Surveillez le processus dans l’Espace partenaires, où l’équipe d’intégration AppSource teste, valide et certifie votre application.
7. Lorsque elle est certifiée, examinez l’application dans son environnement de test et mettez-la en production. Cela la rend visible sur AppSource (elle est « mise en ligne »).
8. Dans Power BI, envoyez le package en production. Pour plus d’informations, consultez [Gérer la mise en production de l’application Power BI](/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="requirements"></a>Spécifications

Pour être publiée sur le marketplace commercial, votre offre d’application Power BI doit répondre aux exigences techniques et commerciales suivantes.

### <a name="technical-requirements"></a>Exigences techniques

La principale ressource technique requise dont vous aurez besoin est une [application Power BI](/power-bi/connect-data/service-template-apps-overview). Il s’agit d’un ensemble de jeux de données principaux, de rapports ou de tableaux de bord. Il comprend également des services connectés facultatifs et des jeux de données incorporés, précédemment appelés [pack de contenu](/power-bi/service-organizational-content-pack-introduction). Pour plus d’informations sur le développement de ce type d’application, consultez [Que sont les applications Power BI ?](/power-bi/connect-data/service-template-apps-overview)

#### <a name="get-an-installation-web-address"></a>Recevoir une URL d’installation

Vous ne pouvez créer une application Power BI que dans l’environnement [Power BI](https://powerbi.microsoft.com/).

1. Connectez-vous avec une [licence Power BI Pro](/power-bi/service-admin-purchasing-power-bi-pro).
2. Créez et testez votre application dans Power BI.
3. Lorsque vous recevez l’URL d’installation de l’application, ajoutez-la à la page **Configuration technique** dans l’Espace partenaires.

Une fois votre application créée et testée dans Power BI, enregistrez l’URL d’installation de l’application, car vous en aurez besoin pour [créer une offre d’application Power BI](power-bi-app-offer-setup.md).

### <a name="business-requirements"></a>Exigences commerciales

Les exigences de l’entreprise incluent des obligations procédurales, contractuelles et légales. Vous devez respecter les consignes suivantes :

- Vous devez être inscrit en tant qu’éditeur du marketplace commercial. Si vous n’êtes pas inscrit, suivez les étapes décrites dans [Créer un compte de marketplace commercial dans Espace partenaires](create-account.md).
- Fournissez un contenu conforme aux critères définis pour que votre offre soit répertoriée sur AppSource. Pour plus d’informations, voir [Vous avez une application à répertorier sur AppSource ? Voici comment faire](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Acceptez et suivez la [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="licensing-options"></a>Options de licence

Il s’agit de la seule option de licence disponible pour les offres d’applications Power BI :

| Option de licence | Processus de transaction |
| --- | --- |
| Obtenez-en une maintenant (gratuit) | Référencez votre offre gratuitement pour les clients. |
|

\* En tant qu’éditeur, vous prenez en charge tous les aspects de la transaction de licence logicielle, notamment la commande, l’exécution de celle-ci, sa mesure, sa comptabilisation et sa facturation, ainsi que son paiement et son encaissement.

## <a name="customer-leads"></a>Prospects

Quand vous publiez une offre sur le marketplace commercial par le biais d’Espace partenaires, connectez-la à votre système de Gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit. La connexion à un CRM est obligatoire si vous souhaitez activer une version d’évaluation ; sinon, la connexion à un CRM est facultative. Espace partenaires prend en charge les tables Azure, Dynamics 365 Customer Engagement, le point de terminaison HTTPS, Marketo et Salesforce.

## <a name="legal-contracts"></a>Contrats juridiques

Vous devrez disposer de Conditions générales que les clients devront accepter avant de pouvoir essayer votre offre ou d’un lien permettant d’y accéder.

## <a name="offer-listing-details"></a>Détails du référencement de l’offre

> [!NOTE]
> Le contenu du référencement de l’offre ne doit pas nécessairement être en anglais si la description de l’offre commence par la phrase « This application is available only in [langue autre que l’anglais] ».

Pour faciliter la création de votre offre, préparez ces éléments à l’avance. Tous sont obligatoires, sauf indication contraire.

- **Nom** : Le nom apparaîtra comme titre de votre offre sur le marketplace commercial. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Résumé des résultats de recherche** : L’objectif ou la fonction de votre offre en une phrase, sans saut de ligne et de 100 caractères maximum. Ce résumé est utilisé dans les résultats de recherche des offres du marketplace commercial.
- **Description** : Cette description s’affiche dans la vue d’ensemble des offres du marketplace commercial. Vous pouvez inclure une proposition de valeur, des avantages clés, une base utilisateur visée, des associations de catégorie ou de secteur, des opportunités d’achats dans l’application, des informations requises et un lien pour en savoir plus. Cette zone de texte contient des contrôles d’éditeur de texte enrichi pour rendre votre description plus attrayante. Si vous le souhaitez, utilisez des balises HTML pour la mise en forme.
- **Mots clés de recherche** (facultatif) : Maximum de trois mots clés que les clients pourront utiliser pour rechercher votre offre. N’incluez pas le **nom** ni la **description** de l’offre : ce texte est inclus automatiquement dans la recherche.
- **Produits compatibles avec votre application** (facultatif) : Le nom d’un maximum de trois produits avec lesquels votre offre fonctionne.
- **Liens Aide/Politique de confidentialité** : URL correspondant à l’aide et la politique de confidentialité de votre entreprise. Il vous incombe de veiller à ce que votre application soit conforme aux lois et règlements relatifs à la protection des données personnelles.
- **Informations de contact**
  - **Coordonnées du support** : Le nom, le numéro de téléphone et l’adresse e-mail des partenaires Microsoft à utiliser quand vos clients ouvrent des tickets. Incluez l’URL du site web de votre support.
  - **Coordonnées de l’équipe d’ingénierie** : Le nom, le numéro de téléphone et l’adresse e-mail de Microsoft à utiliser directement en cas de problème avec votre offre. Ces informations de contact ne sont pas répertoriées dans la place de marché commerciale.
  - **Documents associés** (facultatifs) : Maximum de trois documents destinés aux clients, tels que des livres blancs, des brochures, des listes de contrôle ou des présentations PowerPoint, au format PDF.
- **Média**
    - **Logos** : Fichier PNG pour le logo **Grande taille**. Espace partenaires l’utilisera pour créer d’autres tailles requises du logo. Plus tard, vous pourrez éventuellement les remplacer par d'autres images.
    - **Captures d’écran** : Au moins une et au plus cinq captures d’écran qui illustrent le fonctionnement de votre offre. Les images doivent être de 1280 x 720 pixels, au format PNG, et inclure une légende.
    - **Vidéos** (facultatif) : Jusqu’à quatre vidéos qui illustrent votre offre. Incluez un nom, une URL pour YouTube ou Vimeo et une miniature PNG de 1280 x 720 pixels.

> [!Note]
> Pour être publiées sur la place de marché commerciale, votre offre doit respecter les [stratégies de certification de la place de marché commerciale](/legal/marketplace/certification-policies#100-general).

## <a name="additional-sales-opportunities"></a>Opportunités de ventes supplémentaires

Vous pouvez opter pour des canaux marketing et de vente pris en charge par Microsoft. Lorsque vous créez votre offre dans l’Espace partenaires, deux onglets s’affichent vers la fin du processus :

- **Co-vendre avec Microsoft** : Permettre aux équipes de vente de Microsoft de tenir compte de votre solution éligible de co-vente IP lors de l’évaluation des besoins de leurs clients. Pour plus d’informations sur l’éligibilité à la co-vente, consultez [Prérequis relatifs à l’état de co-vente](/legal/marketplace/certification-policies). Pour plus d’informations sur la préparation de votre offre à des fins d’évaluation, consultez [Option de co-vente sur Espace partenaires](./co-sell-configure.md).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une offre d’application Power BI](power-bi-app-offer-setup.md)
