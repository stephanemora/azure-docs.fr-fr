---
title: Présentation de la sécurité Azure Active Directory (Azure AD) Identity Protection | Microsoft Docs
description: Découvrez comment la « vue d’ensemble de la sécurité » vous donne un aperçu de la position de votre organisation en matière de sécurité.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 610668768c7baca13cb60caf1d810cced31ebec3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452935"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Vue d’ensemble de la sécurité Azure Active Directory Identity Protection

La [Vue d'ensemble de la sécurité](https://aka.ms/IdentityProtectionRefresh) vous donne un aperçu de la situation de votre organisation en matière de sécurité. Elle vous aide à identifier les attaques potentielles et à comprendre l’efficacité de vos stratégies.

La « vue d’ensemble de la sécurité » est divisée en deux sections :

- Les tendances, indiquées à gauche, fournissent une chronologie des risques dans votre organisation.

- Les vignettes, figurant à droite, mettent en évidence les principaux problèmes en cours dans votre organisation et indiquent comment prendre rapidement des mesures.


![Présentation de la sécurité](./media/security-overview/01.png)
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Nouveaux utilisateurs à risque détectés

Ce graphique affiche le nombre de nouveaux utilisateurs à risque qui ont été détectés pendant la période choisie. Vous pouvez filtrer l’affichage de ce graphique par niveau de risque utilisateur (faible, moyen, élevé). Pointez sur les incréments de date UTC pour voir le nombre d’utilisateurs à risque détectés pour ce jour donné. Cliquez sur ce graphique pour accéder au rapport « Utilisateurs à risque ». Pour corriger les utilisateurs à risque, pensez à modifier leur mot de passe.

### <a name="new-risky-sign-ins-detected"></a>Nouvelles connexions à risque détectées

Ce graphique affiche le nombre de connexions à risque qui ont été détectées pendant la période choisie. Vous pouvez filtrer l’affichage de ce graphique par type de risque de connexion (temps réel ou agrégat) et par niveau de risque de connexion (faible, moyen, élevé). Les connexions non protégées sont des connexions à risque en temps réel établies qui n’ont pas été soumises à l’authentification multifacteur. (Remarque : les connexions à risque en raison de détections hors connexion ne peuvent pas être protégées en temps réel par les stratégies de risque à la connexion). Pointez sur les incréments de date UTC pour voir le nombre de connexions à risque détectées pour ce jour donné. Cliquez sur ce graphique pour accéder au rapport « Connexions à risque ».

## <a name="tiles"></a>Vignettes
 
### <a name="high-risk-users"></a>Utilisateurs à risque élevé

La vignette « Utilisateurs à risque élevé » affiche le tout dernier nombre d’utilisateurs présentant une probabilité élevée de compromission d’identité. Leur examen doit être une priorité absolue. Cliquez sur la vignette « Utilisateurs à risque élevé » pour accéder à un affichage filtré du rapport « Utilisateurs à risque » présentant uniquement les utilisateurs dont le niveau de risque est élevé. En vous appuyant sur ce rapport, vous pouvez en savoir plus sur ces utilisateurs et les corriger en réinitialisant leur mot de passe.

![Présentation de la sécurité](./media/security-overview/02.png)


### <a name="medium-risk-users"></a>Utilisateurs à risque moyen
La vignette « Utilisateurs à risque moyen » affiche le tout dernier nombre d’utilisateurs présentant une probabilité moyenne de compromission d’identité. Cliquez sur la vignette « Utilisateurs à risque moyen » pour accéder à un affichage filtré du rapport « Utilisateurs à risque » présentant uniquement les utilisateurs dont le niveau de risque est moyen. À l’aide de ce rapport, vous pouvez procéder à un examen plus profond et corriger ces utilisateurs.

### <a name="unprotected-risky-sign-ins"></a>Connexions à risque non protégées

La vignette « Connexions à risque non protégées » présente le nombre de connexions à risque en temps réel établies de la dernière semaine qui ont été bloquées ou qui n’ont pas été soumises à l’authentification multifacteur (stratégie d’accès conditionnel, stratégies des risques Identity Protection ou MFA par utilisateur). Il s’agit des connexions potentiellement compromises qui ont réussi et qui ne sont pas soumises à l’authentification multifacteur. Pour protéger ces connexions à l’avenir, appliquez une stratégie de connexion à risque. Cliquez sur la vignette « Connexions à risque non protégées » pour accéder au panneau de configuration de la stratégie de connexion à risque où vous pouvez configurer la stratégie de connexion à risque pour exiger l’authentification multifacteur d’une connexion présentant un niveau de risque spécifié.


### <a name="legacy-authentication"></a>Authentification héritée

La vignette « Authentification héritée » présente le nombre d’authentifications héritées de la dernière semaine dans votre organisation. Les protocoles d’authentification hérités ne gèrent pas les méthodes de sécurité modernes telles qu’une authentification multifacteur. Pour empêcher l’authentification héritée, vous pouvez appliquer une stratégie d’accès conditionnel. Cliquez sur la vignette « Authentification héritée » pour accéder à « Identité Secure Score ».


### <a name="identity-secure-score"></a>Identity Secure Score

Identity Secure Score mesure et compare votre méthode de sécurité aux modèles du secteur. Cliquez sur la vignette « Identité Secure Score (préversion) » pour accéder au panneau du même nom où vous pouvez en savoir plus sur l’amélioration de votre position en matière de sécurité.


## <a name="next-steps"></a>Étapes suivantes

- [Channel 9 : Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Activer Azure Active Directory Identity Protection](enable.md)

