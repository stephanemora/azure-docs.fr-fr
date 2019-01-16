---
title: Comparaison des fonctionnalités des niveaux de la Gestion des API Azure | Microsoft Docs
description: Cet article compare les niveaux de la Gestion des API en fonction des fonctionnalités qu’ils proposent.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: eae36aa6e60e3da03c59952a1d9e035e6a773d2d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156695"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparaison des fonctionnalités des niveaux de la Gestion des API Azure

Chacun des [niveaux tarifaires](https://aka.ms/apimpricing) de la Gestion des API propose un ensemble distinct de fonctionnalités et une [capacité](api-management-capacity.md) par unité. Le tableau suivant récapitule les principales fonctionnalités disponibles dans chacun d’entre eux. Certaines fonctionnalités fonctionnent différemment ou sont différentes selon les niveaux. Dans ce cas, les différences sont mises en évidence dans les articles de la documentation qui décrivent ces différentes fonctionnalités.

| Fonctionnalité                                                                                      | Consommation<sup>PRÉVERSION</sup> | Développeur      | De base          | standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Intégration à Azure AD<sup>1</sup>                                                             | Non                             | Oui            | Non              | OUI            | Oui            |
| Prise en charge du réseau virtuel                                                               | Non                             | Oui            | Non              | Non              | Oui            |
| Déploiement multi-régions                                                                      | Non                             | Non              | Non              | Non              | Oui            |
| Noms de domaine personnalisés multiples                                                                 | Non                             | Non              | Non              | Non              | Oui            |
| Portail des développeurs<sup>2</sup>                                                                 | Non                             | OUI            | OUI            | OUI            | Oui            |
| Cache intégré                                                                               | Non                             | OUI            | OUI            | OUI            | Oui            |
| Analytique intégrée                                                                           | Non                             | OUI            | OUI            | OUI            | Oui            |
| [Paramètres SSL](api-management-howto-manage-protocols-ciphers.md)                             | Non                             | OUI            | OUI            | OUI            | Oui            |
| [Cache externe](https://aka.ms/apimbyoc)                                                    | Oui                           | Non<sup>3</sup> | Non<sup>3</sup> | Non<sup>3</sup> | Non<sup>3</sup> |
| [Authentification par certificat client](api-management-howto-mutual-certificates-for-clients.md) | Non<sup>4</sup>                | Oui            | OUI            | OUI            | Oui            |
| [Sauvegarde et restauration](api-management-howto-disaster-recovery-backup-restore.md)               | Non                             | OUI            | OUI            | OUI            | Oui            |
| [Gestion sur Git](api-management-configuration-repository-git.md)                        | Non                             | OUI            | OUI            | OUI            | Oui            |
| API de gestion directe                                                                        | Non                             | OUI            | OUI            | OUI            | Oui            |
| Journaux et métriques Azure Monitor                                                               | Non<sup>5</sup>                | Oui            | OUI            | OUI            | Oui            |

<sup>1</sup> Permet l’utilisation d’Azure AD (et d’Azure AD B2C) comme fournisseur d’identité pour la connexion utilisateur sur le portail des développeurs.<br/>
<sup>2</sup> Y compris les fonctionnalités liées, par exemple, les utilisateurs, les groupes, les problèmes, les modèles d’application et de courrier électronique et les notifications.<br/>
<sup>3</sup> La prise en charge du cache externe sera bientôt disponible pour ce niveau.<br/>
<sup>4</sup> L’authentification par certificat client sera ajoutée au niveau de consommation avant sa disponibilité générale.<br/>
<sup>5</sup> La prise en charge complète Azure Monitor sera ajoutée au niveau de consommation.
