---
title: Offres de module IoT Edge de la Place de marché Azure
description: En savoir plus sur la publication d’offres de modules IoT Edge dans la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/30/2021
ms.openlocfilehash: 70bb18984f5ed3ca6887fd01ec0e98e6f3d41595
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547588"
---
# <a name="plan-an-iot-edge-modules-offer"></a>Planifier une offre de modules IoT Edge

La plateforme [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) repose sur Microsoft Azure.  Cette plateforme permet aux utilisateurs de déployer des charges de travail cloud en vue de les exécuter directement sur des appareils IoT.  Un module IoT Edge peut exécuter des charges de travail hors connexion et effectuer une analyse de données localement. Ce type d’offre permet d’économiser la bande passante, de protéger les données locales et sensibles et offre des temps de réponse à faible latence.  Vous pouvez désormais tirer parti de ces charges de travail prédéfinies. Jusqu’à présent, seules quelques solutions internes de Microsoft étaient disponibles.  Vous deviez investir du temps et des ressources dans la création de vos propres solutions IoT personnalisées.

Avec les [modules IoT Edge sur la Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1), nous mettons à la disposition des éditeurs une destination unique pour exposer et vendre leurs solutions à l’audience IoT. Les développeurs IoT peuvent finalement trouver et acheter des fonctionnalités pour accélérer le développement de leurs solutions.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Principaux avantages des modules IoT Edge sur la Place de marché Microsoft Azure

| **Pour les éditeurs**    | **Pour les clients (développeurs IoT)**  |
| :------------------- | :-------------------|
| Atteindre des millions de développeurs qui cherchent à créer et à déployer des solutions IoT Edge.  | Composer une solution IoT Edge avec la confiance de pouvoir utiliser des composants sécurisés et testés. |
| Publier une seule fois et exécuter sur tout matériel IoT Edge qui prend en charge les conteneurs. | Réduire les délais de commercialisation en recherchant et déployant des modules IoT Edge internes et tiers pour des besoins spécifiques. |
| Monétiser avec les options de facturation flexibles. <ul><li>Licence gratuite et BYOL (apportez votre propre licence).</li></ul> | Effectuer des achats avec votre choix de modèles de facturation.<ul><li>Licence gratuite et BYOL (apportez votre propre licence).</li></ul> |

## <a name="what-is-an-iot-edge-module"></a>Qu’est-ce qu’un module IoT Edge ?

Azure IoT Edge vous permet de déployer et de gérer la logique métier sur la périphérie sous la forme de modules. Les modules Azure IoT Edge sont les plus petites unités de calcul gérées par IoT Edge. Ils peuvent contenir des services Microsoft (par exemple, Azure Stream Analytics), des services tiers ou votre propre code de solution. Pour en savoir plus sur les modules IoT Edge, consultez [Présentation des modules Azure IoT Edge](../iot-edge/iot-edge-modules.md).

### <a name="what-is-the-difference-between-a-container-offer-type-and-an-iot-edge-module-offer-type"></a>Quelle est la différence entre un type d’offre Conteneur et un type d’offre Module IoT Edge ?

Le type d’offre Module IoT Edge est un type spécifique de conteneur s’exécutant sur un appareil IoT Edge. Il est fourni avec des paramètres de configuration par défaut pour s’exécuter dans le contexte d’IoT Edge et utilise éventuellement le SDK du module IoT Edge pour être intégré au runtime IoT Edge.

## <a name="select-the-right-online-store"></a>Sélectionner le magasin en ligne approprié

Les modules IoT Edge sont uniquement publiés sur la Place de marché Microsoft Azure. AppSource ne s’applique pas. Pour plus d’informations sur les différences entre les magasins en ligne, consultez [Déterminer votre option de publication](determine-your-listing-type.md).

## <a name="technical-requirements"></a>Exigences techniques

Les principales exigences techniques pour qu’un module IoT Edge soit certifié et publié sur la Place de marché Azure sont détaillées dans [Préparer vos ressources techniques de module IoT Edge](iot-edge-technical-asset.md).

## <a name="eligibility-prerequisites"></a>Conditions d'éligibilité

Toutes les conditions des contrats et politiques de la Place de marché Microsoft Azure s’appliquent aux offres Module IoT Edge.  En outre, il existe des prérequis et des exigences techniques pour les modules IoT Edge.  

Pour publier un module IoT Edge sur la Place de marché Microsoft Azure, vous devez satisfaire aux prérequis suivants :

- Accès à l’Espace partenaires. Pour plus d’informations, consultez [Créer un compte de marketplace commercial dans Espace partenaires](create-account.md).
- Héberger votre module IoT Edge dans un registre de conteneurs Azure.
- Disposer des métadonnées de votre module IoT Edge suivantes, (liste non exhaustive) :
    - Un titre
    - Une description (au format HTML)
    - Une image de logo (d’une taille de 48 x 48 (facultatif), 90 x 90 (facultatif) et de 216 x 216 à 350 x 350 px, toutes au format PNG)
    - Conditions d’utilisation et politique de confidentialité
    - Configuration du module par défaut (route, propriétés souhaitées du jumeau, createOptions, variables d’environnement)
    - Documentation
    - Contacts du support

## <a name="licensing-options"></a>Options de licence

Voici les options de licence disponibles pour les offres Azure Container :

| Option de licence | Processus de transaction |
| --- | --- |
| Gratuit | Référencez votre offre gratuitement pour les clients. |
| BYOL | L’option BYOL (avec apport de sa propre licence) permet à vos clients d’intégrer des licences logicielles existantes à Azure.\* |
|

\* En tant qu’éditeur, vous prenez en charge tous les aspects de la transaction de licence logicielle, notamment la commande, l’exécution de celle-ci, sa mesure, sa comptabilisation et sa facturation, ainsi que son paiement et son encaissement.

## <a name="publishing-options"></a>Options de publication

Dans tous les cas, les modules IoT Edge doivent sélectionner l’option de publication **Transact**.  Consultez [Choisir une option de publication](determine-your-listing-type.md) pour plus d’informations sur les options de publication.  

## <a name="customer-leads"></a>Prospects

Quand vous publiez une offre sur le marketplace commercial par le biais d’Espace partenaires, connectez-la à votre système de Gestion des relations avec la clientèle (CRM). Cela vous permet de recevoir les coordonnées des clients dès qu’une personne exprime son intérêt ou utilise votre produit. La connexion à un CRM est obligatoire si vous souhaitez activer une version d’évaluation ; sinon, la connexion à un CRM est facultative. Espace partenaires prend en charge les tables Azure, Dynamics 365 Customer Engagement, le point de terminaison HTTPS, Marketo et Salesforce.

## <a name="legal-contracts"></a>Contrats juridiques

Pour simplifier le processus d’approvisionnement pour les clients et réduire la complexité juridique pour les éditeurs de logiciels, Microsoft propose un contrat Standard que vous pouvez utiliser pour vos offres dans la place de marché commerciale. Lorsque vous proposez votre logiciel dans le cadre du contrat standard, les clients ne doivent lire et accepter ce contrat qu’une seule fois, et vous n’avez pas besoin d’élaborer des conditions générales personnalisées.

Vous pouvez également utiliser vos propres conditions générales au lieu du contrat standard. Les clients doivent accepter ces conditions avant de pouvoir essayer votre application.

## <a name="offer-listing-details"></a>Détails du référencement de l’offre

> [!NOTE]
> Le contenu du référencement de l’offre ne doit pas nécessairement être en anglais si la description de l’offre commence par la phrase « This application is available only in [langue autre que l’anglais] ».

Pour faciliter la création de votre offre, préparez ces éléments à l’avance. Tous sont obligatoires, sauf indication contraire.

- **Nom** : Le nom apparaîtra comme titre de votre offre sur le marketplace commercial. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.
- **Résumé des résultats de recherche** : L’objectif ou la fonction de votre offre en une phrase, sans saut de ligne et de 100 caractères maximum. Ce résumé est utilisé dans les résultats de recherche des offres du marketplace commercial.
- **Brève description** : Détails de l’objectif ou de la fonction de l’offre, écrits en texte brut sans saut de ligne. Elle apparaîtra sur la page de détails de votre offre.
- **Description** : Cette description s’affiche dans la vue d’ensemble des offres du marketplace commercial. Vous pouvez inclure une proposition de valeur, des avantages clés, une base utilisateur visée, des associations de catégorie ou de secteur, des opportunités d’achats dans l’application, des informations requises et un lien pour en savoir plus. Cette zone de texte contient des contrôles d’éditeur de texte enrichi pour rendre votre description plus attrayante. Si vous le souhaitez, utilisez des balises HTML pour la mise en forme.
- **Lien vers la politique de confidentialité** : URL de la politique de confidentialité de votre entreprise. Il vous incombe de veiller à ce que votre application soit conforme aux lois et règlements relatifs à la protection des données personnelles.
- **Liens utiles** (facultatif) : Liens vers diverses ressources destinées aux utilisateurs de votre offre. Par exemple, forums, FAQ et notes de publication.
- **Informations de contact**
  - **Coordonnées du support** : Le nom, le numéro de téléphone et l’adresse e-mail des partenaires Microsoft à utiliser quand vos clients ouvrent des tickets. Incluez l’URL du site web de votre support.
  - **Coordonnées de l’équipe d’ingénierie** : Le nom, le numéro de téléphone et l’adresse e-mail de Microsoft à utiliser directement en cas de problème avec votre offre. Ces informations de contact ne sont pas répertoriées dans la place de marché commerciale.
  - **Contact du programme du fournisseur de solutions Cloud** (facultatif) : indiquez le nom, le numéro de téléphone et l'adresse e-mail si vous optez pour le programme du fournisseur de solutions Cloud (CSP), afin que ces partenaires puissent vous contacter pour toute question. Vous pouvez également inclure une URL pour vos documents marketing.
- **Média**
    - **Logos** : Fichier PNG pour le logo **Grande taille**. Espace partenaires l’utilisera pour créer d’autres tailles requises du logo. Plus tard, vous pourrez éventuellement les remplacer par d'autres images.
    - **Captures d’écran** : Au moins une et au plus cinq captures d’écran qui illustrent le fonctionnement de votre offre. Les images doivent être de 1280 x 720 pixels, au format PNG, et inclure une légende.
    - **Vidéos** (facultatif) : Jusqu’à quatre vidéos qui illustrent votre offre. Incluez un nom, une URL pour YouTube ou Vimeo et une miniature PNG de 1280 x 720 pixels.

> [!Note]
> Pour être publiées sur la place de marché commerciale, votre offre doit respecter les [stratégies de certification de la place de marché commerciale](/legal/marketplace/certification-policies#100-general).

## <a name="next-steps"></a>Étapes suivantes

- Connectez-vous à l’[Espace partenaires](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) pour créer ou terminer votre offre.
- [Créez une offre de module IoT Edge](./iot-edge-offer-setup.md) dans l’Espace partenaires.
