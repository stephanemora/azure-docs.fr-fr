---
title: Déterminer votre option de publication – Place de marché commerciale de Microsoft
description: Cet article décrit les critères d’éligibilité et la configuration requise pour la publication d’offres sur Microsoft AppSource et la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/30/2020
ms.openlocfilehash: 1b0846c68f6860b5c7dac9e93808088dac4f6a05
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607431"
---
# <a name="determine-your-publishing-option"></a>Déterminer votre option de publication

L’option de publication que vous choisissez pour votre offre est directement liée aux conditions d’éligibilité et aux avantages GTM de la place de marché. Qui plus est, la sélection de l’option de publication et du type d’offre définit comment les utilisateurs vont interagir avec votre offre sur la place de marché.

Pour configurer votre offre, vous devez comprendre les concepts clés du marché suivants : les options de publication, la configuration et les types d’offre, ainsi que les appels à l’action qui définissent comment et où votre offre est présentée dans les magasins en ligne de la place de marché.

Cet article portera sur les éléments suivants :

- Détermination du magasin en ligne approprié pour votre solution.
- Options de publication et appels à l’action disponibles dans chaque magasin en ligne.
- Types d’offre disponibles pour chaque option de publication.

## <a name="commercial-marketplace-publishing-options"></a>Options de publication sur la place de marché commerciale

Le tableau suivant présente les options de publication disponibles pour les types d’offres dans Microsoft AppSource et dans la Place de marché Azure.

|   | **Liste (contact)**  | **Liste (essai)**  | **Gratuit** | **BYOL** | **Transaction**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Machine virtuelle** |  |  |  | Place de marché Azure |  Place de marché Azure |
| **Applications Azure (multimachines virtuelles)** |  |  | Place de marché Azure | Place de marché Azure | Place de marché Azure  |
| **Image conteneur** |  |  | Place de marché Azure | Place de marché Azure |   |
| **Module IoT Edge** |  |  | Place de marché Azure | Place de marché Azure |   |
| **Services managés** |  |  |  | Place de marché Azure |   |
| **Services de conseil** | Les deux magasins en ligne |  |  |  |   |
| **Application SaaS** | Les deux magasins en ligne | Les deux magasins en ligne | Les deux magasins en ligne |  | Les deux magasins en ligne * |
| **Application Microsoft 365** | AppSource | AppSource |  |  | AppSource**  |
| **Complément Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Actuellement, les offres de transaction d’applications SaaS dans Microsoft AppSource incluent uniquement les cartes de crédit.

&#42;&#42; Les offres Microsoft 365 peuvent être installées gratuitement et monétisées via l’offre SaaS en tant que service de licence. Pour plus d’informations, consultez [Monétiser votre complément Office 365 par le biais de la place de marché commerciale de Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-call-to-action"></a>Choisir un appel à l’action

Les options de publication disponibles offrent un engagement client différencié tout en vous donnant accès au partage des prospects et aux [avantages de la place de marché commerciale](https://docs.microsoft.com/azure/marketplace/gtm-your-marketplace-benefits). Notez les appels à l’action qui correspondent à l’option de publication :

| **Option de publication**    | **Description**  |
| :------------------- | :-------------------|
| **Liste** | Référencement simple de votre application ou service qui permet à un utilisateur de la place de marché de vous demander de le mettre en relation avec le client via l’appel à l’action **Me contacter**. |
| **Version d’évaluation** | Utilisez la place de marché pour augmenter la visibilité et automatiser la configuration de l’expérience d’essai de votre solution. Vous permettez ainsi à des utilisateurs potentiels de découvrir gratuitement votre expérience SaaS, IaaS ou intégrée à Microsoft pendant une période limitée avant de l’acheter. Les appels à l’action utilisés pour l’option de publication Essai sont : **Essai gratuit** et **Version d’évaluation**. |
| **BYOL** | Utilisez la place de marché pour améliorer la détectabilité et automatiser l’approvisionnement de votre solution, ainsi que pour effectuer la transaction financière séparément. Les types d’offres BYOL sont idéaux pour les migrations site vers cloud. L’appel à l’action est **Obtenir maintenant**.
| **Transaction** | Les offres Transact sont vendues via la place de marché. Microsoft est responsable de la facturation et des regroupements. L’appel à l’action est **Obtenir maintenant**.|

> [!Note]
> Quand vous utilisez l’option de publication Transaction, il est important de bien comprendre les prix, la tarification, la facturation et le paiement avant de sélectionner un type d’offre et de créer votre offre. Pour plus d’informations, consultez l’article [Fonctionnalités de transaction de la place de marché commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Sélection d’un magasin en ligne

Chaque magasin en ligne répond à des exigences propres aux clients et cible des publics spécifiques. Le type de votre offre, les capacités de transaction et la catégorie déterminent l’emplacement de publication de votre offre. Les catégories et sous-catégories sont mappées à chaque magasin en ligne en fonction du public cible :

**Microsoft AppSource** cible les utilisateurs professionnels à la recherche de solutions sectorielles et de services de conseil pour Dynamics 365, Microsoft 365 et Power Platform.

**Place de marché Azure** cible les professionnels de l’informatique et les développeurs qui cherchent des solutions conçues pour ou sur Azure, ainsi que des services de conseil qui accélèrent leur utilisation d’Azure.

Sélectionnez la catégorie et la sous-catégorie qui correspondent le mieux à votre public cible. Par exemple, un pare-feu d’applications web doit être publié sur Place de marché Azure, sous la catégorie Sécurité, puisque le public concerné est celui des professionnels de l’informatique. Une application de gestion des contrats doit plutôt être publiée sur AppSource sous la catégorie Ventes, étant donné que le public visé est celui des utilisateurs professionnels. La sélection d’une catégorie ou d’une sous-catégorie incorrecte peut entraîner la publication de votre offre sur le mauvais magasin en ligne.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publication sur les deux magasins en ligne (offres SaaS uniquement)

Les offres SaaS peuvent être publiées sur Place de marché Azure ou AppSource. Si votre offre Saas est destinée *à la fois* à un public technique (Place de marché Azure) et à un public commercial (AppSource), sélectionnez une catégorie et/ou une sous-catégorie applicable à chaque magasin en ligne. Les offres publiées dans les deux magasins en ligne doivent avoir une proposition de valeur qui s’étend aux professionnels de l’informatique *et* aux utilisateurs professionnels.

> [!IMPORTANT]
> Les offres SaaS avec facturation à l’usage sont disponibles sur Place de marché Azure et le portail Azure. Les offres SaaS avec des plans privés uniquement sont disponibles sur le portail Azure.

| Facturation à l’usage | Plan public | Plan privé | Disponible sur : |
|---|---|---|---|
| Oui             | Oui         | Non           | Place de marché Azure et Portail Azure |
| Oui             | Oui         | Oui          | Place de marché Azure et Portail Azure* |
| Oui             | Non          | Oui          | Portail Azure uniquement |
| Non              | Non          | Oui          | Portail Azure uniquement |

&#42; Le plan privé de l’offre est disponible uniquement via le portail Azure.

Par exemple, une offre avec facturation à l’usage et un plan privé uniquement (aucun plan public) est achetée par les clients du portail Azure. En savoir plus sur les [offres privées dans le marketplace commercial de Microsoft](private-offers.md).

### <a name="categories"></a>Catégories

Les catégories et sous-catégories sont mappées à chaque magasin en ligne en fonction du public cible. Sélectionnez les catégories et sous-catégories qui correspondent le mieux à votre offre et au public cible. Vous pouvez sélectionner :

- une catégorie au minimum et trois au maximum. Vous avez la possibilité de choisir une catégorie principale et une catégorie secondaire.
- Jusqu’à deux sous-catégories pour chaque catégorie principale et/ou secondaire. Si vous ne sélectionnez aucune sous-catégorie, votre offre sera tout de même détectable sous la catégorie sélectionnée.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Étapes suivantes

- Après avoir choisi une option de publication, vous êtes prêt à [sélectionner le type d’offre](./publisher-guide-by-offer-type.md) qui sera utilisé pour présenter votre offre.
- Passez en revue les conditions d’éligibilité dans la section relative aux options de publication par type d’offre pour finaliser la sélection et la configuration de votre offre.
- Passez en revue les modèles de publication par magasin en ligne pour obtenir des exemples sur la manière dont votre solution correspond à un type d’offre et à une configuration.
