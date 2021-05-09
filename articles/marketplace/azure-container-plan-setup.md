---
title: Configurer des plans pour une offre Azure Container dans Microsoft AppSource
description: Configurer des plans pour une offre Azure Container dans Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 19eff4aad427cb9227c4167a919629354486282f
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892337"
---
# <a name="set-up-plans-for-an-azure-container-offer"></a>Configurer des plans pour une offre Azure Container

La page **Configuration de plan** vous permet de configurer les clouds dans lesquels le plan est disponible. Les choix effectués sous cet onglet déterminent les champs affichés sous les autres onglets.

## <a name="azure-regions"></a>Régions Azure

### <a name="azure-global"></a>Azure Global

Toutes les offres Azure Container sont automatiquement disponibles dans **Azure Global**.  Votre plan peut être utilisé par les clients de toutes les régions Azure du monde qui recourent à la Place de marché. Pour plus d’informations, consultez [Prise en charge de la disponibilité géographique et des devises](marketplace-geo-availability-currencies.md).

### <a name="azure-government"></a>Azure Government

Sélectionnez **[Azure Government](../azure-government/documentation-government-welcome.md)** pour que votre offre y apparaisse. Il s’agit d’un cloud communautaire pour le secteur public, accessible à des clients d’agences gouvernementales fédérales, étatiques, locales ou tribales des États-Unis ainsi qu’à des partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires pour le cloud de cette communauté. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement). Avant d’effectuer une [publication](../azure-government/documentation-government-manage-marketplace-partners.md) sur Azure Government, testez et vérifiez votre solution dans cette zone, car les résultats peuvent être différents. Pour tester votre solution dans un environnement de préproduction, demandez un compte d’essai depuis la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Une fois votre plan publié et disponible dans une région spécifique, vous ne pouvez pas supprimer cette région.

#### <a name="azure-government-certifications"></a>Certifications Azure Government

Si vous sélectionnez **Azure Government**, ajoutez vos **certifications**. Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS. Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens vers les détails de vos certifications. Il peut s’agir de liens vers vos annonces sur le programme directement ou vers votre propre site web. Ces liens sont visibles uniquement par les clients Azure Government.

Sélectionnez **Enregistrer le brouillon** avant de passer à l’onglet suivant du menu de navigation de gauche **Vue d’ensemble du plan**, **Annonce du plan**.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer l’annonce du plan](azure-container-plan-listing.md)
