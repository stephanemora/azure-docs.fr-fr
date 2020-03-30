---
title: Afficher l’état des offres de la Place de marché | Microsoft Azure
description: Voir l’état des offres des Places de marché Microsoft Azure et AppSource à l’aide du Portail Cloud Partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 0cbe6a45ba205f32a764bdadb021dc0dcf5bf0cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275966"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Voir l’état de la publication des offres de la Place de marché Microsoft Azure et d’AppSource

Après avoir créé une offre et en particulier pendant le processus de publication, vous pouvez voir l’état de votre offre dans le Portail Cloud Partner.  L’état global des publications est disponible dans les pages [**All Offers**](../portal-tour/cpp-all-offers-page.md)et [**Approvals**](../portal-tour/cpp-approvals-page.md) (Approbations )du portail.  Un des indicateurs d’état suivants doit être affiché pour chaque offre.  

|            Statut              |   Description                                                           |
|            ------              |   -----------                                                           |
| **-**                          | L’offre a été créée mais le processus de publication n’a pas commencé.            |
| **Publish in progress** (Publication en cours)        | Le processus de publication de l’offre est en cours.   |
| **Publish failed** (Échec de la publication)             | Un problème critique a été découvert pendant la validation ou la révision par Microsoft. |
| **Publish canceled** (Publication annulée)           | L’éditeur a annulé le processus de publication de l’offre.  Cet état ne retire pas une offre existante de la place de marché. | 
| **Awaiting publisher sign out** (En attente de la validation par l’éditeur) | L’offre a été revue par Microsoft et doit maintenant faire l’objet d’une vérification finale par l’éditeur. |
| **Delisted** (Retirée)                   | Une offre publiée sur la place de marché a été supprimée.      | 
|  |  |


## <a name="publishing-status-details"></a>Détails du statut de la publication 

L’onglet **Status** de la page **New Offer** fournit plus de détails sur l’état d’une offre au fil du processus de publication.  Cette page liste toutes les étapes de publication pour ce type d’offre.  *Notez que le nombre d’étapes et les étapes proprement dites diffèrent d’un type d’offre à l’autre.*  Cette page indique également les problèmes non résolus relevés par Microsoft au cours des étapes de validation et de révision, qui nécessitent souvent une action de la part de l’éditeur pour que le processus de publication puisse se poursuivre.  Par exemple, l’illustration suivante montre l’onglet **Status** pour une nouvelle offre de machine virtuelle. 

![Onglet Status de l’offre de machine virtuelle](./media/vm-offer-pub-steps1.png)

L’exemple d’onglet **Status** suivant pour un service de conseil affiche une erreur signalée dans les paramètres de gestion des prospects.  La gestion des prospects étant nécessaire pour les services de conseil, cette erreur doit être corrigée pour que le processus de publication puisse se poursuivre.

![Onglet Status pour le service de conseil affichant une erreur](./media/consulting-service-error.png)

Le dernier exemple d’état d’une application Azure montre un problème de révision Microsoft critique.  Il comprend un lien hypertexte vers l’élément Azure DevOps qui contient des informations détaillées sur ce problème de révision.  Pour plus d’informations, consultez [Publier une offre d’application Azure](cpp-publish-offer.md).

![Onglet Status de l’application Azure montrant un problème de révision](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Étapes suivantes

Pour corriger les problèmes non résolus ou mettre à jour les paramètres d’une offre, vous devez [mettre à jour une offre](./cpp-update-offer.md). 
