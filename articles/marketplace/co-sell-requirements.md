---
title: Exigences en matière de co-vente | Place de marché Azure
description: Découvrez les conditions qu’une offre de la Place de marché commerciale Microsoft doit remplir pour être éligible au statut « Prête pour la co-vente » ou « Co-vente avec incentives ».
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/17/2021
ms.openlocfilehash: 28fdd8b10c7106380c296c8131ade909ee88d0f8
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107089"
---
# <a name="co-sell-requirements"></a>Exigences en matière de co-vente

Cet article présente les conditions requises pour les différents niveaux de statut de co-vente. Pour obtenir la liste la plus récente des types d’offres qui prennent en charge la co-vente, consultez [Configurer la co-vente pour une offre de la Place de marché commerciale](co-sell-configure.md). Pour obtenir une vue d’ensemble de la co-vente, consultez [Présentation de la co-vente avec les équipes de vente et les partenaires Microsoft](co-sell-overview.md).

Ce tableau indique tous les statuts de co-vente possibles :

| Statut | Commentaire |
| ------------ | ------------- |
| Pas de co-vente prête | Les [conditions minimales requises pour le statut Prêt pour la co-vente](#requirements-for-co-sell-ready-status) ne sont pas remplies. |
| Prêt pour la co-vente | Toutes les [conditions requises pour le statut Prêt pour la co-vente](#requirements-for-co-sell-ready-status) sont remplies. |
| Co-vente Azure IP avec incentives | Les exigences en matière de co-vente sont satisfaites, en plus de [ces exigences supplémentaires](#requirements-for-azure-ip-co-sell-incentivized-status). |
| Co-vente d'applications métier avec incentives | Ce statut s’applique aux offres Dynamics 365 et Power Apps dans le [Programme Business Applications ISV Connect](business-applications-isv-program.md). Il indique que toutes les [conditions requises pour ce statut](#requirements-for-business-applications-co-sell-incentivized-status) sont remplies. |
|||

## <a name="requirements-for-co-sell-ready-status"></a>Conditions requises pour le statut Prêt pour la co-vente

Pour obtenir le statut Prêt pour la co-vente, vous devez remplir les conditions suivantes :

**Tous les partenaires** :

- Avoir un ID MPN et un [compte de Place de marché commerciale actif dans l’Espace partenaires](create-account.md).
- Vérifier que vous disposez d’un [profil métier](/partner-center/create-a-marketing-profile) complet dans l’Espace partenaires. En tant que partenaire Microsoft qualifié, votre profil métier vous aide à présenter votre activité aux clients qui recherchent vos solutions et votre expertise uniques afin de répondre à leurs besoins métier, et donc à générer des [références](/partner-center/referrals).
- Compléter l’onglet **Co-vente avec Microsoft** et publier l’offre sur la Place de marché commerciale.
- Indiquez un contact commercial pour chaque zone géographique éligible à la co-vente ainsi que la nomenclature requise.

**Partenaires de services** :

- Pour les offres de type _Solution de service_, vous devez disposer d’une compétence Gold active dans n’importe quel domaine de compétence.

**Éditeurs de logiciels indépendants d’applications métier** :

- Les solutions Dynamics 365 & PowerApps (à l’exception de Dynamics 365 Business Central) requièrent une inscription ISV Connect.

### <a name="complete-the-co-sell-with-microsoft-tab"></a>Compléter l’onglet Co-vente avec Microsoft.

Lors de la publication ou de la mise à jour de votre offre, fournissez toutes les informations nécessaires sous l’onglet **Co-vente avec Microsoft**, comme indiqué dans [Configurer la co-vente pour une offre de la Place de marché commerciale](commercial-marketplace-co-sell.md). Cela comprend la fourniture des documents suivants :

- Page de la solution/offre
- Jeu de diapositives de la solution/offre

Nous mettons des modèles à votre disposition pour vous aider à créer ces documents. Pour plus d’informations sur les informations obligatoires et facultatives sous l’onglet Co-vente avec Microsoft, consultez [Configurer la co-vente pour une offre de la Place de marché commerciale](commercial-marketplace-co-sell.md).

### <a name="publish-your-offer-live"></a>Publier votre offre en ligne

Pour être éligible au statut Prêt pour la co-vente, votre offre ou solution doit être publiée et active dans au moins l’un des magasins en ligne de la Place de marché commerciale : Place de marché Microsoft Azure ou Microsoft AppSource. Pour plus d’informations sur la publication d’offres sur la Place de marché commerciale, consultez [Guide de publication par type d’offre](publisher-guide-by-offer-type.md). Si vous n’avez encore jamais publié d’offre sur la Place de marché commerciale, vérifiez que vous disposez d’un [compte de Place de marché commerciale](create-account.md).

## <a name="requirements-for-azure-ip-co-sell-incentivized-status"></a>Conditions requises pour le statut Co-vente Azure IP avec incentives

Le statut Co-vente Azure IP avec incentives s’applique aux types d’offres suivants :

- Azure Application
- Conteneur Azure
- Machine virtuelle Azure
- Module IoT Edge
- SaaS (software as a service)

Une fois le statut Prêt pour la co-vente atteint, trois conditions supplémentaires sont requises pour atteindre le statut Co-vente Azure IP avec incentives :

Condition 1 - accomplir ce qui suit :

- Au _niveau de l'organisation_, avoir généré au moins 100 000 USD de Revenu consommé Azure au cours des 12 derniers mois. Cet objectif peut être atteint à l’aide d’une combinaison de solutions Azure. Si l’offre est facturable sur la Place de marché commerciale, vous pouvez satisfaire à cette exigence en atteignant un seuil de revenu facturé de 100 000 USD.

Condition 2 - passer la validation technique Microsoft pour une solution basée sur Azure :
- La validation technique doit confirmer que plus de 50 % de l’infrastructure de votre offre utilise du code IP (propriété intellectuelle) reproductible sur Azure. Notez que les solutions Machines virtuelles Azure et Azure Application facturables sur la Place de marché commerciale satisfont à cette exigence par défaut.

Condition 3 - fournir un diagramme d’architecture de référence :
- Chargez un diagramme d’architecture de référence avec vos documents de co-vente dans l’Espace partenaires afin qu’il soit examiné. Pour obtenir des conseils sur la création de ce diagramme, consultez [Diagramme d’architecture de référence](reference-architecture-diagram.md). Pour plus d’informations sur le chargement du diagramme, consultez [Configurer la co-vente pour une offre de la Place de marché commerciale](commercial-marketplace-co-sell.md).

## <a name="requirements-for-business-applications-co-sell-incentivized-status"></a>Conditions requises pour le statut d’incentives de co-vente Business Applications

Ce statut s’applique aux solutions IP reposant sur Dynamics 365 ou Power Apps (à l’exception de Dynamics 365 Business Central) qui sont inscrits au programme ISV Connect. Toutefois, les offres doivent également remplir les conditions requises pour le statut prêt pour la co-vente (décrit ci-dessus), afin que les vendeurs Microsoft puissent co-vendre l’offre avec vous.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer la co-vente pour une offre de la Place de marché commerciale](commercial-marketplace-co-sell.md)
