---
title: Plans d’hébergement Azure Static Web Apps
description: Comparez les différents plans d’hébergement Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/14/2021
ms.author: cshoe
ms.openlocfilehash: 2fcb9805b752273cb5320114342108ed1f844a5b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110104113"
---
# <a name="azure-static-web-apps-hosting-plans"></a>Plans d’hébergement Azure Static Web Apps

Azure Static Web Apps est disponible par le biais de deux plans différents : Gratuit et Standard. Consultez la [page des tarifs pour connaître les coûts du plan Standard](https://azure.microsoft.com/pricing/details/app-service/static/).

## <a name="features"></a>Fonctionnalités

| Fonctionnalité | Plan Gratuit <br> (Pour les projets personnels) | Plan Standard <br> (Pour les applications de production) |
| --- | --- | --- |
| Hébergement web | ✔ | ✔ |
| Intégration à GitHub | ✔ | ✔ |
| Intégration à Azure DevOps | ✔ | ✔ |
| Contenu statique distribué à l’échelle mondiale | ✔ | ✔ |
| Renouvellement gratuit et automatique des certificats SSL | ✔ | ✔ |
| Environnements intermédiaires | 3 par application | 10 par application |
| Taille maximale de l’application | 250 Mo par application | 500 Mo par application |
| Domaines personnalisés | 2 par application | 5 par application |
| API via Azure Functions | Géré | Managé ou<br>[Amener votre propre application Functions](functions-bring-your-own.md) |
| Intégration du fournisseur d’authentification | [Préconfigurée](authentication-authorization.md)<br>(Définie par le service) | [Inscriptions personnalisées](authentication-custom.md) |
| [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/app-service-static/v1_0/) | Aucun  | ✔ |

## <a name="selecting-a-plan"></a>Sélection d’un plan

Les scénarios suivants peuvent vous aider à déterminer si le plan Standard est le mieux adapté à vos besoins.

- Les volumes de trafic attendus dépassent les limites de bande passante.
- L’application Azure Functions existante que vous souhaitez utiliser a des déclencheurs et des liaisons au-delà des points de terminaison HTTP ou ne peut pas être convertie en application Functions managée.
- Exigences de sécurité qui nécessitent [l’inscription d’un fournisseur personnalisé](authentication-custom.md).
- La taille totale des fichiers des ressources web du site dépasse les limites de stockage maximales.
- Vous avez besoin d’un support technique formel.
- Vous avez besoin de plus de trois [environnements de préproduction](review-publish-pull-requests.md).

Pour plus d’informations sur les limitations, consultez le [guide quotas](quotas.md).

## <a name="changing-plans"></a>Changement de plan

Vous pouvez passer du plan Gratuit au plan Standard, et vice versa, par le biais du portail Azure.

1. Accédez à votre ressource Web Apps statique dans le portail Azure.

1. Dans le menu _Paramètres_, sélectionnez **Plan d’hébergement**.

1. Sélectionnez le plan d’hébergement que vous souhaitez pour votre application web statique.

1. Sélectionnez **Enregistrer**.
