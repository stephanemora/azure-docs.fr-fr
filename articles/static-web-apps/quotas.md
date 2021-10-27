---
title: Quotas dans Azure Static Web Apps
description: Découvrir les quotas associés à Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/13/2021
ms.author: cshoe
ms.openlocfilehash: 4565012ec824961f971f1f1fb34ab330ae11372c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999299"
---
# <a name="quotas-in-azure-static-web-apps"></a>Quotas dans Azure Static Web Apps

Les quotas suivants sont disponibles pour Azure Static Web Apps.

| Fonctionnalité                     | Plan Gratuit        | Plan Standard |
|-----------------------------|------------------|---------------|
| Bande passante incluse          | 100 Go par mois, par abonnement | 100 Go par mois, par abonnement |
| Dépassement de bande passante           | Non disponible      | 0,20 $ par Go |
| Applications par abonnement Azure | 10               | Illimité |
| Taille de l’application                    | 250 Mo           | 500 Mo |
| Taille du plan                   | Taille d’application maximale de 500 Mo pour un déploiement unique et 0,50 Go max pour tous les environnements intermédiaire et de production  | Taille d’application maximale de 500 Mo pour un déploiement unique et 2,00 Go max combiné pour tous les environnements intermédiaire et de production |
| Environnements de pré-production | 3                | 10 |
| Domaines personnalisés              | 2 par application        | 5 par application |
| Autorisation (rôles intégrés) | Nombre illimité d’utilisateurs finaux pouvant s’authentifier avec un rôle `authenticated` intégré | Nombre illimité d’utilisateurs finaux pouvant s’authentifier avec un rôle `authenticated` intégré |
| Autorisation (rôles personnalisés) | Jusqu’à 25 utilisateurs finaux pouvant appartenir à des rôles personnalisés par le biais d’[invitations](authentication-authorization.md?tabs=invitations#role-management) | Jusqu’à 25 utilisateurs finaux pouvant appartenir à des rôles personnalisés par le biais d’[invitations](authentication-authorization.md?tabs=invitations#role-management) ou nombre illimité d’utilisateurs finaux auxquels des rôles personnalisés peuvent être attribués par le biais d’une [fonction serverless](authentication-authorization.md?tabs=function#role-management) |

## <a name="github-storage"></a>Stockage GitHub

Les fonctionnalités GitHub Actions et Packages utilisent le stockage GitHub, qui possède son propre ensemble de quotas. Lorsque vous créez un site Azure Static Web Apps, GitHub stocke les artefacts de votre site, même après le déploiement.

Pour plus d’informations, consultez les ressources suivantes :

- [Gestion de l’espace de stockage de GitHub Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [À propos de la facturation de GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Gestion de votre limite de dépense pour GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Étapes suivantes

- [Vue d'ensemble](overview.md)
