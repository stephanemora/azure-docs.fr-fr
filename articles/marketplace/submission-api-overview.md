---
title: Vue d’ensemble des API de soumission à la Place de marché commerciale dans l’Espace partenaires
description: Obtenez une vue d’ensemble des API de soumission à la Place de marché commerciale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 246f57b09e075baaa17296e0c81c2f756e0f42ac
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056672"
---
# <a name="commercial-marketplace-submission-api-overview"></a>Vue d’ensemble des API de soumission à la Place de marché commerciale

Utilisez l’API pour interroger, soumettre et publier des offres programmatiquement. Cette API est utile si votre compte gère de nombreuses offres, et si vous souhaitez automatiser et optimiser le processus de soumission de ces offres.

## <a name="types-of-apis"></a>Types d’API

Il existe deux ensembles d’API de soumission :

- **API de soumission de l’Espace partenaires** : ensemble usuel d’API qui fonctionnent sur les produits grand public et commerciaux à publier via l’Espace partenaires. De nouvelles fonctionnalités sont sans arrêt ajoutées à cet ensemble d’API. Pour plus d’informations sur l’intégration de cette API, consultez [Intégration de l’API de soumission de l’Espace partenaires](submission-api-onboard.md). 
- **API héritées du portail Cloud Partner** : il s’agit d’API transférées à partir du portail Cloud Partner déprécié. Ces API intégrées continuent de fonctionner dans l’Espace partenaires. Cet ensemble d’API est en mode maintenance uniquement. Les nouvelles fonctionnalités introduites dans l’Espace partenaires risquent de ne pas être prises en charge. Vous devez uniquement les utiliser pour des produits existants, qui ont déjà été intégrés avant la transition vers l’Espace partenaires. Pour plus d’informations sur la façon de continuer à utiliser les API du portail Cloud Partner, consultez la [Référence d’API pour le portail Cloud Partner](cloud-partner-portal-api-overview.md).

Consultez le tableau suivant afin de connaître les API de soumission prises en charge pour chaque type d’offre.

| Type d’offre | Prise en charge des API héritées du portail Cloud Partner | Prise en charge de l’API de soumission de l’Espace partenaires |
| --- | :---: | :---: |
| Azure Application |  | &#x2714; |
| Conteneur Azure | &#x2714; |  |
| Machine virtuelle Azure | &#x2714; |  |
| Service de conseil | &#x2714; |  |
| Dynamics 365 |  | &#x2714; |
| Module IoT Edge | &#x2714; |  |
| Service managé | &#x2714; |  |
| Application Power BI | &#x2714; |  |
| Software as a Service |  | &#x2714; |
|

Les compléments Microsoft 365 Office, les solutions Microsoft 365 SharePoint, les applications Microsoft 365 Teams et les visuels Power BI ne prennent pas en charge les API de soumission.

## <a name="next-steps"></a>Étapes suivantes

- Accédez au lien vers l’API appropriée en fonction du type d’offre, selon les besoins
