---
title: Quotas dans Azure Static Web Apps (préversion)
description: En savoir plus sur les quotas associés à Azure Static Web Apps (préversion)
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: e3538e90a6dea69c703f56871fde86a18557a022
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106095167"
---
# <a name="quotas-in-azure-static-web-apps-preview"></a>Quotas dans Azure Static Web Apps (préversion)

Les quotas suivants sont disponibles pour Azure Static Web Apps (préversion).

> [!IMPORTANT]
> Azure Static Web Apps est en préversion publique et n’est pas destiné à une utilisation en environnement de production.

| Fonctionnalité                     | Plan Gratuit        |
|-----------------------------|------------------|
| Bande passante incluse          | 100 Go par mois |
| Dépassement de bande passante           | Non disponible      |
| Applications par abonnement Azure | 10               |
| Taille de l’application                    | 250 Mo           |
| Environnements de pré-production | 3                |
| Domaines personnalisés              | 1                |
| Autorisation (avec des rôles personnalisés et des règles de routage) | Jusqu’à 25 utilisateurs finaux pouvant appartenir à des rôles personnalisés |
| Azure Functions             | Disponible        |
| Contrat SLA                         | None             |

## <a name="github-storage"></a>Stockage GitHub

Les fonctionnalités GitHub Actions et Packages utilisent le stockage GitHub, qui possède son propre ensemble de quotas. Lorsque vous créez un site Azure Static Web Apps, GitHub stocke les artefacts de votre site, même après le déploiement.

Pour plus d’informations, consultez les ressources suivantes :

- [Gestion de l’espace de stockage de GitHub Actions](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [À propos de la facturation de GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Gestion de votre limite de dépense pour GitHub Actions](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Étapes suivantes

- [Vue d'ensemble](overview.md)
