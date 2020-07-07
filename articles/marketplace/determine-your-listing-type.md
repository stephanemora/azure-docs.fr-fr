---
title: Déterminer votre option de publication – Place de marché commerciale de Microsoft
description: Cet article décrit les critères d’éligibilité et la configuration requise pour la publication d’offres sur Microsoft AppSource et la Place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 7c8a8bcef548ae752b6ddeb7663dd82b3f245349
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957883"
---
# <a name="determine-your-publishing-option"></a>Déterminer votre option de publication

L’option de publication que vous choisissez pour votre offre est directement liée aux conditions d’éligibilité et aux avantages GTM de la place de marché. Qui plus est, la sélection de l’option de publication et du type d’offre définit comment les utilisateurs vont interagir avec votre offre sur la place de marché.

Pour configurer votre offre, vous devez comprendre les concepts clés du marché suivants : les options de publication, la configuration et les types d’offre, ainsi que les appels à l’action qui définissent comment et où votre offre est présentée dans les vitrines de la place de marché.

Cet article portera sur les éléments suivants :

- Détermination de la vitrine appropriée pour votre solution
- Options de publication et appels à l’action disponibles dans chaque vitrine
- Types d’offre disponibles pour chaque option de publication

## <a name="commercial-marketplace-publishing-options"></a>Options de publication sur la place de marché commerciale

Le tableau suivant présente les options de publication disponibles pour les types d’offres dans Microsoft AppSource et dans la Place de marché Azure.

|   | **Liste (contact)**  | **Liste (essai)**  | **Gratuit** | **BYOL** | **Transaction**|
| :--------- | :----------- | :------------ | :----------- | :---------- |:---------- |
| **Machine virtuelle** |  |  |  | Place de marché Azure |  Place de marché Azure |
| **Applications Azure (multimachines virtuelles)** |  |  | Place de marché Azure | Place de marché Azure | Place de marché Azure  |
| **Image conteneur** |  |  | Place de marché Azure | Place de marché Azure |   |
| **Module IoT Edge** |  |  | Place de marché Azure | Place de marché Azure |   |
| **Services managés** |  |  |  | Place de marché Azure |   |
| **Services de conseil** | Les deux vitrines |  |  |  |   |
| **Application SaaS** | Les deux vitrines | Les deux vitrines | Les deux vitrines |  | Les deux vitrines* |
| **Application Microsoft 365** | AppSource | AppSource |  |  | AppSource**  |
| **Complément Dynamics 365** |  AppSource | AppSource |  |  |   |
| **PowerApps** | AppSource |AppSource  |  |  |   |

&#42; Actuellement, les offres de transaction d’applications SaaS dans Microsoft AppSource incluent uniquement les cartes de crédit.

&#42;&#42; Les offres Microsoft 365 peuvent être installées gratuitement et monétisées via l’offre SaaS en tant que service de licence. Pour plus d’informations, consultez [Monétiser votre complément Office 365 par le biais de la place de marché commerciale de Microsoft](/office/dev/store/monetize-addins-through-microsoft-commercial-marketplace).

## <a name="selecting-a-storefront"></a>Sélection d’une vitrine

Avant de sélectionner une option de publication, il est important de comprendre les conditions d’éligibilité des vitrines pour les solutions, les applications et les services de la place de marché commerciale. Chaque vitrine répond à des exigences propres aux clients et cible des publics spécifiques. Le type de votre offre, les fonctionnalités de transaction et la catégorie ou le secteur déterminent où publier votre offre.

Les applications **Microsoft AppSource** sont des solutions métier qui peuvent être créées sur Azure ou créées pour : Dynamics 365, Office 365, Power BI ou Power Apps. Les services de conseil AppSource sont des offres de services professionnels qui aident les clients à commencer à utiliser Dynamics 365 et Power BI ou à les utiliser plus.

Les applications de la **Place de marché Azure** sont des solutions techniques de type « module » créées sur ou pour Azure. Elles sont destinées à un public d’informaticiens généralistes ou spécialisés (développeurs). Les services de conseil de la Place de marché Azure sont des offres de services professionnels qui aident les clients à commencer à utiliser Azure ou à l’utiliser plus.

>[!Note]
>Listes croisées (pour applications SaaS uniquement) : quand une offre basée sur une liste ou un essai répond aux critères d’un public d’utilisateurs professionnels aussi bien que d’un public technique, elle est publiée dans les deux vitrines. Découvrez plus en détail les options de publication ci-dessous.

## <a name="choose-a-publishing-option"></a>Choisir une option de publication

Les options de publication disponibles offrent un engagement client différencié tout en vous donnant accès au partage des prospects et aux [avantages de la place de marché commerciale](gtm-your-marketplace-benefits.md). Notez les appels à l’action qui correspondent à l’option de publication :

| **Option de publication**    | **Description**  |
| :------------------- | :-------------------|
| **Liste** | Référencement simple de votre application ou service qui permet à un utilisateur de la place de marché de vous demander de le mettre en relation avec le client via l’appel à l’action **Me contacter**. |
| **Version d’évaluation** | Utilisez la place de marché pour augmenter la visibilité et automatiser la configuration de l’expérience d’essai de votre solution. Vous permettez ainsi à des utilisateurs potentiels de découvrir gratuitement votre expérience SaaS, IaaS ou intégrée à Microsoft pendant une période limitée avant de l’acheter. Les appels à l’action utilisés pour l’option de publication Essai sont : **Essai gratuit** et **Version d’évaluation**. |
| **BYOL** | Utilisez la place de marché pour améliorer la détectabilité et automatiser l’approvisionnement de votre solution, ainsi que pour effectuer la transaction financière séparément. Les types d’offres BYOL sont idéaux pour les migrations site vers cloud. L’appel à l’action est **Obtenir maintenant**.
| **Transaction** | Les offres Transact sont vendues via la place de marché. Microsoft est responsable de la facturation et des regroupements. L’appel à l’action est **Obtenir maintenant**.|

> [!Note]
> Quand vous utilisez l’option de publication Transaction, il est important de bien comprendre les prix, la tarification, la facturation et le paiement avant de sélectionner un type d’offre et de créer votre offre. Pour plus d’informations, consultez l’article [Fonctionnalités de transaction de la place de marché commerciale](./marketplace-commercial-transaction-capabilities-and-considerations.md).

## <a name="next-steps"></a>Étapes suivantes

- Après avoir choisi une option de publication, vous êtes prêt à [sélectionner le type d’offre](./publisher-guide-by-offer-type.md) qui sera utilisé pour présenter votre offre.
- Passez en revue les conditions d’éligibilité dans la section relative aux options de publication par type d’offre pour finaliser la sélection et la configuration de votre offre.
- Passez en revue les modèles de publication par vitrine pour obtenir des exemples sur la manière dont votre solution correspond à un type d’offre et à une configuration.
