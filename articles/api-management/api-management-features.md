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
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: bc5eab6bb5044e474c386d3e5771d8517e6ba9cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072529"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparaison des fonctionnalités des niveaux de la Gestion des API Azure

>[!IMPORTANT]
> Veuillez noter que le niveau développeur est uniquement pour les cas d’utilisation et les évaluations hors production. Il ne propose pas de SLA. 

Chacun des [niveaux tarifaires](https://aka.ms/apimpricing) de la Gestion des API propose un ensemble distinct de fonctionnalités et une [capacité](api-management-capacity.md) par unité. Le tableau suivant récapitule les principales fonctionnalités disponibles dans chacun d’entre eux. Certaines fonctionnalités fonctionnent différemment ou sont différentes selon les niveaux. Dans ce cas, les différences sont mises en évidence dans les articles de la documentation qui décrivent ces différentes fonctionnalités.

| Fonctionnalité                                                                                      | Consommation | Développeur      | De base          | standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Intégration à Azure AD<sup>1</sup>                                                             | Non                            | OUI            | Non             | OUI            | OUI            |
| Prise en charge du réseau virtuel                                                               | Non                            | OUI            | Non             | Non             | OUI            |
| Déploiement multi-régions                                                                      | Non                            | Non             | Non             | Non             | OUI            |
| Noms de domaine personnalisés multiples                                                                 | Non                            | Non             | Non             | Non             | OUI            |
| Portail des développeurs<sup>2</sup>                                                                 | Non                            | OUI            | OUI            | OUI            | OUI            |
| Cache intégré                                                                               | Non                            | OUI            | OUI            | OUI            | OUI            |
| Analytique intégrée                                                                           | Non                            | OUI            | OUI            | OUI            | OUI            |
| [Paramètres SSL](api-management-howto-manage-protocols-ciphers.md)                             | OUI                            | OUI            | OUI            | OUI            | OUI            |
| [Cache externe](https://aka.ms/apimbyoc)                                                    | OUI                           | OUI            | OUI            | OUI            | OUI            |
| [Authentification par certificat client](api-management-howto-mutual-certificates-for-clients.md) | OUI                | OUI            | OUI            | OUI            | OUI            |
| [Sauvegarde et restauration](api-management-howto-disaster-recovery-backup-restore.md)               | Non                            | OUI            | OUI            | OUI            | OUI            |
| [Gestion sur Git](api-management-configuration-repository-git.md)                        | Non                            | OUI            | OUI            | OUI            | OUI            |
| API de gestion directe                                                                        | Non                            | OUI            | OUI            | OUI            | OUI            |
| Journaux d’activité et métriques Azure Monitor                                                               | Non                | OUI            | OUI            | OUI            | OUI            |
| Adresse IP statique                                                               | Non                | OUI            | OUI            | OUI            | OUI            |

<sup>1</sup> Permet l’utilisation de Microsoft Azure AD (et Azure AD B2C) en tant que fournisseur d’identité pour la connexion utilisateur sur le portail des développeurs.<br/>
<sup>2</sup> Y compris les fonctionnalités liées, par exemple, les utilisateurs, les groupes, les problèmes, les modèles d’application et de courrier électronique et les notifications.<br/>
