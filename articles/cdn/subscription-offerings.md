---
title: Offres d’abonnement Azure CDN et limitation de bande passante
description: En savoir plus sur les offres Azure CDN disponibles pour un type d’abonnement spécifique.
services: cdn
author: duongau
manager: kumud
ms.service: azure-cdn
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: duau
ms.openlocfilehash: 9c8cf3576075b2854b14d83d126e9f790d47b74d
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112008141"
---
# <a name="azure-cdn-subscription-offers-and-bandwidth-throttling"></a>Offres d’abonnement Azure CDN et limitation de bande passante

Certaines offres Azure CDN sont uniquement disponibles pour certains types d’abonnements. La limitation de bande passante peut également s’appliquer en fonction de votre type d’abonnement.

## <a name="free-and-trial-subscription"></a>Abonnement gratuit et d’évaluation

Azure CDN de Microsoft est la seule offre de CDN disponible pour les abonnements d’évaluation. La limitation de bande passante est appliquée pour ce type d’abonnement.  
 
## <a name="pay-as-you-go"></a>Paiement à l’utilisation

Azure CDN d’Akamai n’est pas disponible pour les abonnements Paiement à l’utilisation.   
 
La bande passante pour Azure CDN de Microsoft et de Verizon sera limitée jusqu’à ce que l’abonnement soit en règle et que l’historique de paiement soit suffisant. Le processus de détermination de l’état de l’abonnement et de suppression de la limitation se produit automatiquement après la réception du premier paiement.   
 
Si vous avez effectué un paiement et que la limitation n’a pas été supprimée, vous pouvez en faire la demande en [contactant le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
  
## <a name="enterprise-agreements"></a>Contrats Entreprise

Toutes les offres Azure CDN sont disponibles pour les abonnements sous Contrat Entreprise. Les abonnements sous Contrats Entreprise n’ont aucune restriction de bande passante.  
 
## <a name="other-offer-types"></a>Autres types d’offres
 
Les mêmes fonctionnalités que pour Paiement à l’utilisation s’appliquent à ces types de contrats :

* Visual Studio 
* MSDN
* Étudiants
* CSP

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer un profil Azure CDN](cdn-create-new-endpoint.md).
