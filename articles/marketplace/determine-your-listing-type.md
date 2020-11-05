---
title: Déterminer votre option de publication – Place de marché commerciale de Microsoft
description: Cet article décrit les critères d’éligibilité et la configuration requise pour la publication d’offres sur Microsoft AppSource et la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 09/04/2020
ms.openlocfilehash: 0b4941d326359a51ff6a75385a74a8bcdabfa075
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128995"
---
# <a name="determine-your-publishing-option"></a>Déterminer votre option de publication

L’option de publication que vous choisissez pour votre offre est directement liée aux conditions d’éligibilité et aux avantages GTM de la place de marché. Qui plus est, la sélection de l’option de publication et du type d’offre définit comment les utilisateurs vont interagir avec votre offre sur la place de marché.

Pour configurer votre offre, vous devez comprendre les concepts clés du marché suivants : les options de publication, la configuration et les types d’offre, ainsi que des options de listing qui définissent comment et où votre offre est présentée dans les magasins en ligne de la place de marché.

Cet article portera sur les éléments suivants :

- Détermination du magasin en ligne approprié pour votre solution.
- Options de publication et de listing disponibles dans chaque magasin en ligne.
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

&#42;&#42; Les offres Microsoft 365 peuvent être installées gratuitement et monétisées via l’offre SaaS en tant que service de licence. Pour plus d’informations, consultez [Monétiser votre complément Microsoft 365 par le biais de la place de marché commerciale de Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="choose-a-listing-option"></a>Choisir une option de listing

Les options de listing disponibles offrent un engagement client différencié tout en vous donnant accès au partage des prospects et aux [avantages de la place de marché commerciale](./gtm-your-marketplace-benefits.md). Notez les options de listing qui correspondent à l’option de publication :

| **Option de publication**    | **Description**  |
| :------------------- | :-------------------|
| **Liste** | Référencement simple de votre application ou service qui permet à un utilisateur de la place de marché de vous demander de le mettre en relation avec le client via les options de listing **Me contacter**. |
| **Version d’évaluation** | Utilisez la place de marché pour augmenter la visibilité et automatiser la configuration de l’expérience d’essai de votre solution. Vous permettez ainsi à des utilisateurs potentiels de découvrir gratuitement votre expérience SaaS, IaaS ou intégrée à Microsoft pendant une période limitée avant de l’acheter. Les options de listing utilisées pour l’option de publication Essai sont : **Essai gratuit** et **Version d’évaluation**. |
| **BYOL** | Utilisez la place de marché pour améliorer la détectabilité et automatiser l’approvisionnement de votre solution, ainsi que pour effectuer la transaction financière séparément. Les types d’offres BYOL sont idéaux pour les migrations site vers cloud. L’option de listing est **Obtenir maintenant**.
| **Transaction** | Les offres Transact sont vendues via la place de marché. Microsoft est responsable de la facturation et des regroupements. L’option de listing est **Obtenir maintenant**.|

> [!Note]
> Quand vous utilisez l’option de publication Transaction, il est important de bien comprendre les prix, la tarification, la facturation et le paiement avant de sélectionner un type d’offre et de créer votre offre. Pour plus d’informations, consultez l’article [Fonctionnalités de transaction de la place de marché commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="selecting-an-online-store"></a>Sélection d’un magasin en ligne

Chaque magasin en ligne répond à différentes exigences des clients pour les solutions informatiques et commerciales. Le type de votre offre, les capacités de transaction et la catégorie déterminent l’emplacement de publication de votre offre. Les catégories et sous-catégories sont mappées à chaque magasin en ligne en fonction du type de solution que vous publiez :

**Microsoft AppSource** offre des solutions d’entreprise, notamment des solutions sectorielles et des services de conseil, pour Dynamics 365, Microsoft 365 et Power Platform.

La **Place de marché Azure** offre des solutions informatiques conçues pour ou sur Azure, ainsi que des services de conseil qui accélèrent l’utilisation d’Azure de vos clients.

Sélectionnez la catégorie et la sous-catégorie qui correspondent le mieux au type de votre solution. Par exemple, un pare-feu d’applications web est une solution informatique qui doit être publiée sur Place de marché Azure, sous la catégorie Sécurité. Une application de gestion des contrats est une solution d’entreprise qui doit être publiée sur AppSource sous la catégorie Ventes. La sélection d’une catégorie ou d’une sous-catégorie incorrecte peut entraîner la publication de votre offre sur le mauvais magasin en ligne.

### <a name="publishing-to-both-online-stores-saas-offers-only"></a>Publication sur les deux magasins en ligne (offres SaaS uniquement)

Les offres SaaS peuvent être publiées sur Place de marché Azure ou AppSource. Si votre offre Saas est *à la fois* une solution informatique (Place de marché Azure) et une solution commerciale (AppSource), sélectionnez une catégorie et/ou une sous-catégorie applicable à chaque magasin en ligne. Les offres publiées sur les deux magasins en ligne doivent avoir une proposition de valeur à la fois en tant que solution informatique *et* solution d’entreprise.

> [!IMPORTANT]
> Les offres SaaS avec [facturation à l’usage](partner-center-portal/saas-metered-billing.md) sont disponibles sur Place de marché Azure et le portail Azure. Les offres SaaS avec des plans privés uniquement sont disponibles sur le portail Azure.

| Facturation à l’usage | Plan public | Plan privé | Disponible sur : |
|---|---|---|---|
| Oui             | Oui         | Non           | Place de marché Azure et Portail Azure |
| Oui             | Oui         | Oui          | Place de marché Azure et Portail Azure* |
| Oui             | Non          | Oui          | Portail Azure uniquement |
| Non              | Non          | Oui          | Portail Azure uniquement |

&#42; Le plan privé de l’offre est disponible uniquement via le portail Azure.

Par exemple, une offre avec facturation à l’usage et un plan privé uniquement (aucun plan public) est achetée par les clients du portail Azure. En savoir plus sur les [offres privées dans le marketplace commercial de Microsoft](private-offers.md).

### <a name="categories"></a>Catégories

Les catégories et sous-catégories sont mappées à chaque magasin en ligne en fonction du type de votre solution. Sélectionnez les catégories et sous-catégories qui correspondent le mieux à votre solution. Vous pouvez sélectionner :

- une catégorie au minimum et trois au maximum. Vous pouvez choisir une catégorie principale et une catégorie secondaire.
- Jusqu’à deux sous-catégories pour chaque catégorie principale et/ou secondaire. Si vous ne sélectionnez aucune sous-catégorie, votre offre sera tout de même détectable sous la catégorie sélectionnée.

[!INCLUDE [categories and subcategories](./includes/categories.md)]

## <a name="next-steps"></a>Étapes suivantes

- Une fois que vous avez choisi une option de publication, passez en revue le [Guide de publication par Type d’offre](./publisher-guide-by-offer-type.md).