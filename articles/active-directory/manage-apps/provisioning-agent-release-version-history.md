---
title: 'Agent de provisionnement Azure AD Connect : Historique de publication des versions | Microsoft Docs'
description: Cet article répertorie toutes les publications de l’agent de provisionnement d’Azure AD Connect et détaille les nouvelles fonctionnalités ainsi que les problèmes corrigés
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ce9549765f6a912b3e95f99d11da20347b82ad8
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326460"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent de provisionnement Azure AD Connect : Historique de publication des versions
Cet article liste les versions et les fonctionnalités de l’agent de provisionnement Azure Active Directory Connect qui ont été publiées. L’équipe Azure AD met régulièrement à jour l’agent d’approvisionnement avec de nouvelles fonctions et fonctionnalités. L’agent de provisionnement est mis à jour automatiquement quand une nouvelle version est publiée. 

Nous vous recommandons d’activer la mise à jour automatique pour vos agents pour vous assurer que vous disposez des dernières fonctionnalités et des corrections de bogues. Microsoft offre une prise en charge directe de la version la plus récente de l’agent et de la version précédente.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>État de la version

9 septembre 2019 : Publié pour la mise à jour automatique

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Possibilité de configurer un traçage et une journalisation supplémentaires pour le débogage des problèmes de l’agent de provisionnement
* Capacité de récupérer uniquement les attributs Azure AD configurés dans le mappage pour améliorer les performances de synchronisation

### <a name="fixed-issues"></a>Problèmes résolus

* Correction d’un bogue à cause duquel l’agent ne répondait pas en cas de problème avec les échecs de connexion Azure AD
* Correction d’un bogue qui entraînait des problèmes lors de la lecture des données binaires à partir d’Azure Active Directory
* Correction d’un bogue à cause duquel l’agent ne pouvait pas renouveler l’approbation avec le service d’identité hybride cloud

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>État de la version

23 janvier 2019 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Optimisation de l’architecture du connecteur et de l’agent de provisionnement à des fins de performances, de stabilité et de fiabilité 
* Simplification de la configuration de l’agent de provisionnement avec l’Assistant Installation géré par l’interface utilisateur 
* Ajout de la prise en charge des mises à jour automatiques de l’agent

