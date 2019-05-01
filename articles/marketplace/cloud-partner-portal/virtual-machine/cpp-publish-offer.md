---
title: Publier une offre de machine virtuelle dans Azure Marketplace
description: Affiche les étapes nécessaires pour publier une offre de machine virtuelle sur la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 08/17/2018
ms.author: pabutler
ms.openlocfilehash: 6796c2871cf8a5928beed2ab557cefdfe8ecaae9
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938593"
---
# <a name="publish-a-virtual-machine-offer"></a>Publier une offre de machine virtuelle

 La dernière étape, après avoir défini l’offre dans le portail et créé les ressources techniques associées, consiste à envoyer l’offre à la publication. Le schéma suivant montre les principales étapes du processus de publication :

![Étapes de publication d’une offre de machine virtuelle](./media/publishvm_013.png)

Le tableau suivant décrit ces étapes et fournit une estimation de leur durée maximale :
<!-- we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Étape de publication**           | **Time**    | **Description**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Valider les prérequis         | 15 min   | Les informations de l’offre et les paramètres de l’offre sont validés.                        |
| Validation de Test Drive (facultatif) | 2 heures | Si vous avez choisi d’activer Test Drive, Microsoft vérifie la configuration de Test Drive, son déploiement et la réplication sur les régions sélectionnées. |
| Certification                  | 3 jours | L’offre est analysée par l’équipe de certification d’Azure. Cette étape recherche des virus, des programmes malveillants et des problèmes de sécurité, et vérifie la conformité de la sécurité. Si un problème est détecté, des commentaires sont fournis. |
| Approvisionnement                   | 4 jours   | L’offre de machine virtuelle est répliquée dans les systèmes de production de place de marché.               |
| Empaquetage et référencement de génération de prospects | \< 1 heure  | Les ressources techniques de l’offre sont empaquetées pour une utilisation par le client, et les systèmes de prospection sont installés et configurés. |
|  Validation de l’éditeur             |  -        | Révision finale de l’éditeur et confirmation avant la mise en ligne de l’offre. Vous pouvez déployer votre offre dans les abonnements sélectionnés (dans les étapes d’informations de l’offre) pour vérifier qu’elle répond à toutes vos exigences.  |
| Approvisionnement                   | 4 jours | L’offre de machine virtuelle finalisée est répliquée dans les régions et systèmes de production de place de marché. | 
| En direct                           | 4 jours | L’offre de machine virtuelle est publiée, répliquée dans les régions requises et mises à la disposition du public. |
|  |  |

Ce processus peut prendre jusqu’à 16 jours.  Après avoir effectué ces étapes de publication, votre offre de machine virtuelle s’affiche dans la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/). 

