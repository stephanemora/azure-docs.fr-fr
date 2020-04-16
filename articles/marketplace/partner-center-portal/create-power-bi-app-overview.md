---
title: Vue d’ensemble de la création d’applications Power BI – Place de marché Azure
description: Cet article décrit les étapes principales de la publication d’une application Power BI sur Microsoft AppSource. Les exigences techniques et commerciales auxquelles votre application Power BI doit répondre pour être publiée sur le marketplace commercial sont également fournies.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: c348a172b16e12334d33cf2718609694147fdce3
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674960"
---
# <a name="power-bi-app-creation-overview"></a>Vue d’ensemble de la création d’applications Power BI

> [!IMPORTANT]
> Nous allons déplacer la gestion de vos offres d’application Power BI du Portail Cloud Partner vers l’Espace partenaires. Tant que vos offres ne sont pas migrées, suivez les instructions dans [Offre d’application Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) pour que le Portail Cloud Partner gère vos offres.

Cet article explique comment publier une application Power BI sur [Microsoft AppSource](https://appsource.microsoft.com/). Une application Power BI regroupe le contenu personnalisable, notamment les jeux de données, les rapports et les tableaux de bord. Vous pouvez ensuite utiliser l’application avec d’autres plateformes Power BI à l’aide d’AppSource, effectuer les ajustements et les personnalisations autorisés par le développeur et la connecter à vos propres données.

## <a name="publishing-benefits"></a>Avantages liés à la publication

Avantages de la publication sur le marketplace commercial :

- Promouvez votre entreprise en utilisant la marque Microsoft.
- Touchez potentiellement plus de 100 millions d’utilisateurs Office 365 et Dynamics 365 sur AppSource, ainsi que plus de 200 000 organisations via la Place de marché Microsoft Azure.
- Recevez des prospects de grande qualité à partir de ces marketplaces.
- Bénéficiez d’une promotion de vos services assurée par les équipes de terrain et de télévente de Microsoft.

## <a name="overview"></a>Vue d’ensemble

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="vue d’ensemble des étapes de publication d’une application Power BI" border="false":::

Voici les principales étapes de la publication :

1. Créez votre application dans Power BI. Vous recevrez un lien d’installation de package, qui est la ressource technique principale de l’offre. Envoyez le package de test à l’équipe de préproduction avant de créer l’offre dans l’Espace partenaires. Pour plus d’informations, consultez [Que sont les applications Power BI ?](https://docs.microsoft.com/power-bi/service-template-apps-overview)
2. Ajoutez les documents marketing, tels que le nom, la description et les logos officiels.
3. Incluez les documents juridiques et de support de l’offre, tels que les conditions d’utilisation, la politique de confidentialité, la politique de support et l’aide de l’utilisateur.
4. Créez l’offre : utilisez l’Espace partenaires pour modifier les détails, notamment la description de l’offre, les documents marketing, les mentions légales, les informations de support et les spécifications des ressources.
5. Envoyez-la pour publication.
6. Surveillez le processus dans l’Espace partenaires, où l’équipe d’intégration AppSource teste, valide et certifie votre application.
7. Lorsque elle est certifiée, examinez l’application dans son environnement de test et mettez-la en production. Cela la rend visible sur AppSource (elle est « mise en ligne »).
8. Dans Power BI, envoyez le package en production. Pour plus d’informations, consultez [Gérer la mise en production de l’application Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release).

## <a name="before-you-begin"></a>Avant de commencer

Consultez les liens ci-dessous, ils vous fournissent des modèles, des conseils et des exemples.

- [Créer une application dans Power BI](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Conseils pour créer une application dans Power BI](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [Exemples](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>Spécifications

Pour être publiée sur le marketplace commercial, votre offre d’application Power BI doit répondre aux exigences techniques et commerciales suivantes.

### <a name="technical-requirements"></a>Exigences techniques

La principale ressource technique requise dont vous aurez besoin est une [application Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). Il s’agit d’un ensemble de jeux de données principaux, de rapports ou de tableaux de bord. Il comprend également des services connectés facultatifs et des jeux de données incorporés, précédemment appelés [pack de contenu](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction). Pour plus d’informations sur le développement de ce type d’application, consultez [Que sont les applications Power BI ?](https://go.microsoft.com/fwlink/?linkid=2028636)

#### <a name="get-an-installation-web-address"></a>Recevoir une URL d’installation

Vous ne pouvez créer une application Power BI que dans l’environnement [Power BI](https://powerbi.microsoft.com/).

1. Connectez-vous avec une [licence Power BI Pro](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro).
2. Créez et testez votre application dans Power BI.
3. Lorsque vous recevez l’URL d’installation de l’application, ajoutez-la à la page **Configuration technique** dans l’Espace partenaires.

Une fois votre application créée et testée dans Power BI, enregistrez l’URL d’installation de l’application, car vous en aurez besoin pour [créer une offre d’application Power BI](https://aka.ms/AzureCreatePBIServiceApp).

### <a name="business-requirements"></a>Exigences commerciales

Les exigences de l’entreprise incluent des obligations procédurales, contractuelles et légales. Vous devez respecter les consignes suivantes :

- Vous devez être inscrit en tant qu’éditeur du marketplace commercial. Si vous n’êtes pas inscrit, suivez les étapes décrites dans [Devenir un éditeur sur la Place de marché commerciale](https://docs.microsoft.com/azure/marketplace/become-publisher).
- Fournissez un contenu conforme aux critères définis pour que votre offre soit répertoriée sur AppSource. Pour plus d’informations, voir [Vous avez une application à répertorier sur AppSource ? Voici comment faire](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- Acceptez et suivez la [déclaration de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="next-steps"></a>Étapes suivantes

- [Créer une offre d’application Power BI dans l’Espace partenaires](https://aka.ms/AzureCreatePBIServiceApp)