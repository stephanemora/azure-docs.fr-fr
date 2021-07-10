---
title: Quotas dans Azure Static Web Apps
description: Découvrir les quotas associés à Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 12666d69f6dcab043e909dbb2b49276644a70d76
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110069559"
---
# <a name="quotas-in-azure-static-web-apps"></a>Quotas dans Azure Static Web Apps

Les quotas suivants sont disponibles pour Azure Static Web Apps.

| Fonctionnalité                     | Plan Gratuit        | Plan Standard |
|-----------------------------|------------------|---------------|
| Bande passante incluse          | 100 Go par mois, par abonnement | 100 Go par mois, par abonnement |
| Dépassement de bande passante           | Non disponible      | 0,20 $ par Go |
| Applications par abonnement Azure | 10               | Illimité |
| Taille de l’application                    | 250 Mo           | 500 Mo |
| Environnements de pré-production | 3                | 10 |
| Domaines personnalisés              | 2 par application        | 5 par application |
| Autorisation (avec des rôles personnalisés et des règles de routage) | Jusqu’à 25 utilisateurs finaux pouvant appartenir à des rôles personnalisés | Jusqu’à 25 utilisateurs finaux pouvant appartenir à des rôles personnalisés |

## <a name="github-storage"></a>Stockage GitHub

Les fonctionnalités GitHub Actions et Packages utilisent le stockage GitHub, qui possède son propre ensemble de quotas. Lorsque vous créez un site Azure Static Web Apps, GitHub stocke les artefacts de votre site, même après le déploiement.

Pour plus d’informations, consultez les ressources suivantes :

- [Gestion de l’espace de stockage de GitHub Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [À propos de la facturation de GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Gestion de votre limite de dépense pour GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Étapes suivantes

- [Vue d'ensemble](overview.md)
