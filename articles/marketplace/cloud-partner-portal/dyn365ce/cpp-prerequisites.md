---
title: Prérequis pour l’offre Dynamics 365 for Customer Engagement - Place de marché Azure | Microsoft Docs
description: Conditions préalables à la publication d’une offre Azure Application sur la Place de marché Microsoft Azure.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 4b4859c41e7a3903de68b62e8587f1c85805a782
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081849"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Prérequis pour Dynamics 365 for Customer Engagement

Cet article décrit les prérequis techniques et métier pour la publication d’une offre d’application Dynamics 365 for Customer Engagement sur la Place de marché AppSource.


## <a name="technical-requirements"></a>Exigences techniques

Votre application Dynamics 365 for Customer Engagement doit être conforme aux [directives de révision d’application Microsoft AppSource](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), qui comprend les exigences suivantes :


|              Prérequis             |        Description           |
|            ---------------           |      ---------------         |
| Intégration d’Azure Active Directory   | Votre application doit permettre l’authentification unique fédérée Azure Active Directory avec activation du consentement. Pour plus d’informations, consultez [Guide pratique pour obtenir une certification AppSource pour Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Intégration à Microsoft Cloud Services (facultatif) | Là où cette fonctionnalité est nécessaire, votre application doit s’intégrer avec d’autres services Microsoft Cloud, tels que Microsoft Power BI, Microsoft Flow, ou des services Microsoft Azure comme Machine Learning ou Cognitives Services. |
| Application métier            |  Votre application doit se concentrer sur un processus ou un problème métier bien défini, cibler principalement les entreprises, et permettre aux utilisateurs de se connecter avec leurs informations d’identification professionnelles (nom d’utilisateur et mot de passe).  |
| Période d’essai gratuite et évaluation |  Un client doit pouvoir utiliser votre application gratuitement pendant une durée limitée : « Télécharger maintenant » pour les applications, « essai gratuit » pour une période spécifiée, « version d’évaluation » ou option de demande « Me contacter ».  |
| Configuration faible/nulle                 | Votre application doit être facile et rapide à configurer (aucun développement ou personnalisation nécessaire).  |
| Service client                     | La prise en charge de votre application doit inclure un lien de support où les clients peuvent trouver de l’aide.  |
| Disponibilité/Durée de fonctionnement                  | Votre application doit avoir une disponibilité d’au moins 99,9 %. |
|  |  |


## <a name="business-requirements"></a>Exigences commerciales

Les exigences de l’entreprise incluent des obligations procédurales, contractuelles et légales suivantes :

* Vous devez être inscrit au réseau [MPN (Microsoft Partner Network)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) ou être un éditeur sur la Place de marché dans le cloud inscrit. Si vous n’êtes pas inscrit, suivez les étapes décrites dans [Devenir un éditeur sur la Place de marché dans le cloud](../../become-publisher.md).  (Pour la troisième étape, utilisez plutôt le [formulaire de candidature pour partenaire AppSource](https://appsource.microsoft.com/partners/signup)). 

    >[!NOTE]
    >Vous devez utiliser le même compte d’inscription au Centre de développement Microsoft pour vous connecter au Portail Cloud Partner. Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Ce compte ne doit pas être propre à des services ou des offres spécifiques.

* Étant donné qu’AppSource n’offre pas d’option de publication prenant en charge le commerce, vous devez utiliser votre infrastructure de facturation et de commande actuelle sans investissement ni modification supplémentaire.
* Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
* Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
* Vous devez avoir créé les supports marketing associés, par exemple un nom d’application officiel, une description (au format HTML), des images de logo au format PNG (40 x 40, 90 x 90, 115 x 115 et 255 x 115 pixels), ainsi que des Conditions d’utilisation et une Politique de confidentialité.  


## <a name="next-steps"></a>Étapes suivantes

Une fois que ces conditions sont réunies, vous pouvez [créer une offre Dynamics 365 Customer Engagement](./cpp-create-offer.md) 
