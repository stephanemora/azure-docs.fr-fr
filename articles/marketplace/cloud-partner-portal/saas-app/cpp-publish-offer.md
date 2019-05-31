---
title: Publier l’offre d’application SaaS Azure | Place de marché Azure
description: Processus et étapes de publication d’une offre d’application SaaS sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 5574a7ba216eed42896d6fcb1890585f76561834
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258085"
---
# <a name="publish-a-saas-application-offer"></a>Publier une offre d’application SaaS

Une fois que vous avez créé une offre en fournissant les informations correspondantes sur la page **Nouvelle offre**, vous pouvez publier cette offre. Sélectionnez **Publier** pour démarrer le processus de publication.

> [!IMPORTANT] 
> SaaS offre des fonctionnalités sont migrée vers le [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Tous les nouveaux éditeurs doivent utiliser le centre de partenaires pour la création de nouvelles offres de SaaS et de gestion des offres existants.  Éditeurs actuels avec les offres SaaS sont batchwise migrées à partir du portail Cloud Partner pour l’espace partenaires.  Le portail Cloud Partner affiche des messages d’état pour indiquer quand les offres spécifiques ont été migrés.
> Pour plus d’informations, consultez [créer une offre SaaS](../../partner-center-portal/create-new-saas-offer.md).


Le schéma suivant décrit le principal processus utilisé pour la publication d’une nouvelle offre d’application SaaS.

![Étapes de publication d’une offre](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Description détaillée des étapes de publication

Le tableau ci-après décrit chacune des étapes de publication, avec une estimation de la durée (maximale) nécessaire à leur exécution.

|     **Étape**       |     **Time**      |  **Description**  |
|  ---------------   |  ---------------  |  ---------------  |
|         Certification           |       2 semaines            |          L’offre est analysée par l’équipe de certification d’Azure. Cette étape recherche des virus, des programmes malveillants ainsi que des problèmes de sécurité, et vérifie la conformité aux normes de sécurité. Elle vérifie également que cette offre répond à tous les critères d’éligibilité (voir les [conditions préalables](./cpp-prerequisites.md)). Si un problème est détecté, des commentaires sont fournis.         |
|           Packaging         |       1 heure            |       Les ressources techniques de l’offre sont empaquetées à des fins d’utilisation par le client, et les systèmes de prospection sont installés et configurés.            |
|        Validation de l’éditeur            |         -          |        Révision finale de l’éditeur et confirmation avant la mise en ligne de l’offre. Vous pouvez déployer votre offre dans les abonnements sélectionnés (dans les étapes d’informations de l’offre) pour vérifier qu’elle répond à toutes vos exigences. Sélectionnez **Démarrer** pour faire passer votre offre à l’étape suivante.           |
|        Packaging            |        1 heure           |        L’offre finalisée est répliquée dans les régions et systèmes de production de place de marché.           |
|        En direct            |       4 jours            |         L’offre est publiée, répliquée dans les régions requises et mise à la disposition du public.          |

Allouez jusqu’à 10 jours ouvrables aux phases d’achèvement du processus de publication et de mise en production de l’offre. Une fois que vous avez terminé le processus de publication, votre offre SaaS est répertoriée dans la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Étapes suivantes

[Mettre à jour une offre existante](./cpp-update-existing-offer.md)
