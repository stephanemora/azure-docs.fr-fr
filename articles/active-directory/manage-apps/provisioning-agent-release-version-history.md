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
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861988"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent de provisionnement Azure AD Connect : Historique de publication des versions
Cet article répertorie les versions et les fonctionnalités de l’agent d’approvisionnement Azure AD Connect qui ont été publiées. L’équipe Azure AD met régulièrement à jour l’agent d’approvisionnement avec de nouvelles fonctions et fonctionnalités. Les agents d’approvisionnement sont mis à jour automatiquement lorsqu’une nouvelle version est publiée. 

Nous vous recommandons d’activer la mise à jour automatique pour vos agents pour vous assurer que vous disposez des dernières fonctionnalités et des corrections de bogues. Microsoft offre une prise en charge directe de la version la plus récente de l’agent et de la version précédente.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>État de la version

9 septembre 2019 : Publié pour la mise à jour automatique

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Possibilité de configurer un traçage et une journalisation supplémentaires pour le débogage des problèmes de l’agent d’approvisionnement
* Capacité de récupérer uniquement les attributs AD configurés dans le mappage pour améliorer les performances de synchronisation

### <a name="fixed-issues"></a>Problèmes résolus

* Correction d’un bogue à cause duquel l’agent entrait en état de non-réponse en cas de problème avec les échecs de connexion d’AD
* Correction d’un bogue qui entraînait des problèmes lors de la lecture des données binaires à partir d’Active Directory
* Correction d’un bogue à cause duquel l’agent ne pouvait pas renouveler l’approbation avec le service d’identité hybride cloud

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>État de la version

23 janvier 2019 : publiée pour téléchargement

### <a name="new-features-and-improvements"></a>Améliorations et nouvelles fonctionnalités

* Amélioration de l’architecture du connecteur et de l’agent de provisionnement à des fins de performances, de stabilité et de fiabilité 
* Simplification de la configuration de l’agent d’approvisionnement avec l’interface utilisateur de l’assistant installation 
* Ajout de la prise en charge des mises à jour automatiques de l’agent

