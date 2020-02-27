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
ms.date: 02/04/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 164d156be030aa4a177ea77ed61782780338abb0
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522371"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent de provisionnement Azure AD Connect : Historique de publication des versions
Cet article liste les versions et les fonctionnalités de l’agent de provisionnement Azure Active Directory Connect qui ont été publiées. L’équipe Azure AD met régulièrement à jour l’agent d’approvisionnement avec de nouvelles fonctions et fonctionnalités. L’agent de provisionnement est mis à jour automatiquement quand une nouvelle version est publiée. 

Nous vous recommandons d’activer la mise à jour automatique pour vos agents pour vous assurer que vous disposez des dernières fonctionnalités et des corrections de bogues. Microsoft offre une prise en charge directe de la version la plus récente de l’agent et de la version précédente.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>État de la version

4 décembre 2019 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Prend en charge le [provisionnement cloud Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) pour synchroniser les données sur les utilisateurs, contacts et groupes de l’instance Active Directory locale avec Azure AD


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

