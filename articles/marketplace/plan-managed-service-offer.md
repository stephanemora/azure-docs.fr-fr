---
title: Planifier une offre Service géré pour le marketplace commercial de Microsoft
description: Comment planifier une nouvelle offre Service géré pour Place de marché Azure à l’aide du programme de marketplace commercial dans Espace partenaires Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: f096e53f8054039f361bde1c5f2adffac615c53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371938"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>Comment planifier une offre Service géré pour le marketplace commercial de Microsoft

Cet article présente la configuration requise pour la publication d’une offre Service géré sur le marketplace commercial de Microsoft via Espace partenaires.

Les services gérés sont des offres de Place de marché Azure qui permettent une gestion multi-locataire et interlocataire avec Azure Lighthouse. Pour en savoir plus, consultez [Présentation d’Azure Lighthouse](../lighthouse/overview.md). Lorsqu'un client achète une offre Service géré, il peut déléguer un ou plusieurs abonnements ou groupes de ressources. Vous pouvez ensuite travailler sur ces ressources à l'aide des fonctionnalités de [gestion déléguée des ressources Azure](../lighthouse/concepts/azure-delegated-resource-management.md) d'Azure Lighthouse.

## <a name="eligibility-requirements"></a>Conditions d’éligibilité

Pour publier une offre de service managé, vous devez avoir acquis une compétence Microsoft Gold ou Silver dans la plateforme cloud. Cette compétence démontre votre expertise aux clients. Pour plus d’informations, consultez [Compétences Microsoft Partner Network](https://partner.microsoft.com/membership/competencies).

Les offres doivent satisfaire à toutes les [stratégies de certification du marketplace commercial](/legal/marketplace/certification-policies) en vigueur pour être publiées sur Place de marché Azure.

## <a name="customer-leads"></a>Prospects

Vous devez connecter votre offre à votre système de gestion des relations avec la clientèle (Customer Relationship Management, CRM) pour collecter des informations sur les clients. Le client devra autoriser le partage de ses informations. Ces détails du client, ainsi que le nom de l’offre, son ID et le magasin en ligne dans lequel il a trouvé votre offre, seront envoyés au système CRM que vous avez configuré. Le marketplace commercial prend en charge différents types de systèmes CRM, ainsi que la possibilité d’utiliser une table Azure ou de configurer un point de terminaison HTTPS à l’aide de Power Automate.

Vous pouvez ajouter ou modifier une connexion CRM à tout moment pendant ou après la création de l’offre. Pour obtenir des instructions détaillées, consultez [Prospects de votre offre de marketplace commercial](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="legal-contracts"></a>Contrats juridiques

Dans la page Propriétés d’Espace partenaires, vous êtes invité à fournir des **conditions générales** pour l’utilisation de votre offre. Vous pouvez entrer vos conditions directement dans Espace partenaires ou indiquer l’URL où elles sont accessibles. Les clients seront tenus d’accepter ces conditions générales avant d’acheter votre offre.

## <a name="offer-listing-details"></a>Détails du référencement de l’offre

Lorsque vous créez votre offre Service géré dans Espace partenaires, vous entrez du texte, des images, des documents et d’autres détails relatifs à l’offre. C’est ce que les clients verront lorsqu’ils découvriront votre offre sur Place de marché Azure. Voir l’exemple suivant :

:::image type="content" source="media/example-managed-service.png" alt-text="Illustre la façon dont une offre Service géré apparaît sur Place de marché Azure.":::

**Descriptions de légende**

1. Logo
1. Nom
1. Description courte
1. Catégories
1. Contrats juridiques et politique de confidentialité
1. Description
1. Captures d’écran/vidéos
1. Liens utiles

Voici un exemple de la façon dont l’offre s’affiche dans le portail Azure :

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="Illustre la façon dont cette offre s’affiche dans le portail Azure.":::

**Descriptions de légende**

1. Nom
2. Description
3. Liens utiles
4. Captures d’écran/vidéos

> [!NOTE]
> Si votre offre est dans une langue autre que l’anglais, le référencement de l’offre peut être dans cette langue, mais la description doit commencer ou se terminer par la phrase en anglais « This service is available in &lt;language of your offer content> » (Ce service est disponible en <langue du contenu de votre offre>). Vous pouvez également fournir un complément de documentation dans une langue différente de celle utilisée dans les détails de l’offre.

Pour faciliter la création de votre offre, préparez certains de ces éléments à l’avance. Sauf indication contraire, les éléments suivants sont requis.

**Nom** : il apparaîtra comme titre du référencement de votre offre sur le marketplace commercial. Le nom peut être une marque. Il ne peut pas contenir d’emojis (sauf s’il s’agit du symbole de marque ou de copyright) et ne doit pas dépasser 50 caractères.

**Résumé des résultats de recherche** : décrivez l’objectif ou le but de votre offre en 100 caractères maximum. Ce résumé est utilisé dans les résultats de recherche de la publication sur le marketplace commercial. Il ne doit pas être identique au titre. Pensez à inclure vos principaux mots clés de SEO.

**Description courte** : entrez une courte description de votre offre (256 caractères maximum). Elle sera affichée dans le référencement de votre offre sur le portail Azure.

**Description** : décrivez votre offre en 3 000 caractères maximum. Cette description s’affichera dans la publication sur le marketplace commercial. Pensez à inclure une proposition de valeur, un avantage clé, des associations de catégorie ou de secteur d’activité et toute information obligatoire requise.

Voici quelques conseils pour rédiger votre description :

* Décrivez clairement la valeur de votre offre dès les premières phrases, à savoir :
    * Le type d’utilisateur qui bénéficie de l’offre.
    * Les besoins ou problèmes auxquels l’offre répond.
* Gardez à l’esprit que les premières phrases peuvent être affichées dans les résultats de recherche.
* Utilisez le vocabulaire spécifique au secteur d’activité.

Vous pouvez également utiliser des balises HTML pour mettre en forme votre description. Pour plus d’informations sur la mise en forme HTML, consultez [Balises HTML prises en charge dans les descriptions d’offres de la place de marché commerciale](./supported-html-tags.md).

**Lien vers la politique de confidentialité** : fournissez une URL vers la politique de confidentialité hébergée sur votre site. Il vous incombe de veiller à ce que votre offre respecte les lois et réglementations en matière de confidentialité et à renseigner une politique de confidentialité conforme.

**Liens utiles** (facultatif) : chargez des documents en ligne complémentaires à propos de votre offre.

**Coordonnées** : indiquez le nom, l’adresse e-mail et le numéro de téléphone de deux personnes de votre entreprise (vous pouvez être l’une d’elles), un contact pour l’assistance et l’autre pour l’ingénierie. Nous utiliserons ces informations pour communiquer avec vous concernant votre offre. Ces informations ne sont pas présentées aux clients, mais peuvent être communiquées aux fournisseurs de solutions Cloud (CSP) partenaires.

**URL de support technique** (facultatif) : si vous avez des sites web de support technique pour les clients Azure international et/ou les clients Azure Government, fournissez ces URL.

**Médias de marketplace – logos** : fournissez un fichier PNG pour le logo grand format de votre offre. Espace partenaires l’utilisera pour créer des logos de petite et moyenne taille. Si vous le souhaitez, vous pouvez par la suite remplacer ces logos par une autre image.

* Le grand logo (d’une taille comprise entre 216 x 216 et 350 x 350 pixels) apparaît dans le référencement de votre offre dans la Place de marché Azure.
* Le logo moyen (90 x 90 pixels) s’affiche lors de la création d’une ressource.
* Le petit logo (48 x 48 pixels) s’affiche dans les résultats de la recherche de la Place de marché Azure.

Suivez ces instructions pour vos logos:

* Assurez-vous que l’image n’est pas étirée.
* Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
* Les couleurs du portail Azure sont le blanc et le noir. N’utilisez pas ces couleurs comme arrière-plan de votre logo. Nous recommandons d’utiliser des couleurs primaires simples qui mettent votre logo en évidence.
* Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.
* L’apparence de votre logo doit être « plate ». Évitez les dégradés. Ne placez pas de texte sur le logo, pas même le nom de votre marque ou de votre entreprise.

**Médias de marketplace – captures d’écran** (facultatif) : ajoutez jusqu’à cinq images qui illustrent le fonctionnement de votre offre. Chacune image doit avoir une taille de 1280 x 720 pixels et être au format .PNG.

**Médias de marketplace – vidéos** (facultatif) : chargez jusqu’à cinq vidéos qui illustrent votre offre. Les vidéos doivent être hébergées sur YouTube ou Vimeo et avoir une miniature (fichier PNG 1280 x 720).

## <a name="preview-audience"></a>Public de la version préliminaire

Un public de préversion peut accéder à votre offre avant sa publication sur Place de marché Azure afin de la tester. Dans la page **Public de préversion** d’Espace partenaires, vous pouvez définir un public de préversion limité.

> [!NOTE]
> Le public d’une préversion diffère de celui d’un plan privé. Un plan privé est un plan que vous mettez à la disposition d’un public spécifique que vous choisissez. Cela vous permet de négocier un plan personnalisé avec des clients spécifiques.

Vous pouvez envoyer des invitations à des adresses e-mail de Compte Microsoft (MSA) ou d’Azure Active Directory (Azure AD). Ajoutez jusqu’à 10 adresses e-mail manuellement, ou importez-en jusqu’à 20 à l’aide d’un fichier .csv. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de tester d’éventuels changements ou mises à jour.

## <a name="plans-and-pricing"></a>Plans et tarifs

Les offres Service géré nécessitent au moins un plan. Un plan définit le champ d’application de la solution, les limites et la tarification associée, le cas échéant. Vous pouvez créer plusieurs plans pour votre offre afin d’offrir à vos clients différentes options techniques et de tarification. Pour obtenir des conseils généraux sur les plans, notamment les plans privés, consultez [Plans et tarification pour les offres du marketplace commercial](plans-pricing.md).

Les services gérés ne prennent en charge qu’un seul modèle de tarification : **BYOL (apportez votre propre licence)** . Cela signifie que vous facturerez directement vos clients et que Microsoft ne vous facturera aucun frais.

## <a name="next-steps"></a>Étapes suivantes

* [Créer une offre de service managé](./create-managed-service-offer.md)
* [Bonnes pratiques pour le référencement des offres](./gtm-offer-listing-best-practices.md)