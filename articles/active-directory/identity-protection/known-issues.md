---
title: FAQ et problèmes connus liés à Identity Protection (version actualisée) dans Azure Active Directory | Microsoft Docs
description: FAQ et problèmes connus liés à Identity Protection (version actualisée) dans Azure Active Directory.
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
ms.date: 01/24/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 392b7a08d9422658c5620f60e9c1caca074bc85e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60452675"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>FAQ et problèmes connus liés à Identity Protection (version actualisée) dans Azure Active Directory


## <a name="dismiss-user-risk-known-issues"></a>Faire disparaître les problèmes connus liés aux risques de l’utilisateur

**Ignorer le risque lié à l’utilisateur** dans la version classique d’Identity Protection définit l’acteur dans l’historique des risques utilisateur d’Identity Protection (version actualisée) sur **Azure AD**.


**Ignorer le risque lié à l’utilisateur** dans Identity Protection (version actualisée) définit l’acteur dans l’historique des risques utilisateur d’Identity Protection (version actualisée) sur le **\<nom de l’administrateur avec un lien hypertexte pointant vers le panneau de l’utilisateur\>** .

Un problème connu provoque actuellement des latences dans le flux de rejet du risque utilisateur. Si vous disposez d'une « Stratégie de risque utilisateur », celle-ci cessera de s'appliquer aux utilisateurs ignorés quelques minutes après un clic sur « Ignorer le risque lié à l'utilisateur ». Cela dit, des retards d'actualisation de l'« état de risque » des utilisateurs ignorés ont été identifiés. Pour contourner ce problème, actualisez la page au niveau du navigateur afin d'afficher le dernier « État de risque » de l'utilisateur.


## <a name="risky-users-report-known-issues"></a>Les utilisateurs à risque signalent les problèmes connus

Les requêtes sur le champ **Nom d’utilisateur** respectent la casse, alors que les requêtes sur le champ **Nom** ne sont pas sensibles à la casse.

Désactivez **Show dates as** (Afficher les dates en tant que) pour masquer la colonne **RISK LAST UPDATED** (Dernière mise à jour du risque). Pour lire la colonne, cliquez sur **Colonnes** en haut du panneau des utilisateurs à risque.

**Ignorer tous les événements** dans la version classique d’Identity Protection définit l’état des événements à risque sur **Closed (resolved)** (Fermé (résolu)).


## <a name="risky-sign-ins-report-known-issues"></a>Problèmes connus du rapport sur les connexions à risque

**Résoudre** sur un événement à risque définit l’état sur **Users passed MFA driven by risk-based policy** (Les utilisateurs ont passé la MFA pilotée par une stratégie basée sur les risques).


## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Pourquoi ne puis-je pas définir mes propres niveaux de risque pour chaque événement à risque ?

Les niveaux de risque dans Identity Protection sont basés sur la précision de la détection et développés par notre Machine Learning supervisé. Pour personnaliser l’expérience présentée aux utilisateurs, l’administrateur peut inclure/exclure certains utilisateurs/groupes des stratégies de risque de l’utilisateur et de risque de connexion.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Pourquoi l’emplacement d’une connexion ne correspond pas à l’emplacement à partir duquel l’utilisateur s’est réellement connecté ?

Le mappage de géolocalisation IP est un défi à l’échelle du secteur. Si vous pensez que l’emplacement indiqué dans le rapport de connexions ne correspond pas à l’emplacement réel, veuillez contacter le support. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Comment les mécanismes de commentaires fonctionnent-ils dans Identity Protection ?

**Confirmer comme étant compromise** (sur une connexion) : informe Azure AD Identity Protection que la connexion n’a pas été effectuée par le propriétaire de l’identité et indique un compromis.

- Lors de la réception de ces commentaires, nous définissons le risque de l’utilisateur et de connexion à l’état **État compromis confirmé** et le niveau de risques sur **Élevé**.

- Nous fournissons également les informations à nos systèmes de Machine Learning dans le but d’améliorations futures de l’évaluation des risques.

    > [!NOTE]
    > Si l’utilisateur est déjà corrigé, ne cliquez pas sur **Confirmer comme étant compromise**, car ceci définit l’état du risque de connexion et de l’utilisateur sur **État compromis confirmé** et le niveau de risque sur **Élevé**.

**Confirmer comme étant sécurisée** (sur une connexion) : informe Azure AD Identity Protection que la connexion a été effectuée par le propriétaire de l’identité et n’indique pas de compromis.

- Lors de la réception de ces commentaires, nous définissons le risque de connexion (et non de l’utilisateur) à l’état **Sécurité confirmée** et le niveau de risques sur **-** .

- Nous fournissons également les informations à nos systèmes de Machine Learning dans le but d’améliorations futures de l’évaluation des risques.

    > [!NOTE]
    > Si vous pensez que l’utilisateur n’est pas compromis, utilisez **Ignorer le risque lié à l’utilisateur** au niveau de l’utilisateur plutôt que d’utiliser **Sécurité confirmée** au niveau de la connexion. **Ignorer le risque lié à l’utilisateur** au niveau de l’utilisateur ferme le risque de l’utilisateur, ainsi que toutes les connexions à risque et tous les événements à risque antérieurs.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Pourquoi un utilisateur avec un faible indice de risque (ou supérieur) s’affiche-t-il même si aucune connexion à risque ni aucun événement à risque n’apparaît dans Identity Protection ?

Étant donné que le risque de l’utilisateur est cumulatif par nature et n’expire pas, un utilisateur peut avoir un risque utilisateur faible ou supérieur même si aucune connexion à risque ni aucun événement à risque récent n’apparaît dans Identity Protection. Cela peut se produire si une activité malveillante sur un utilisateur a eu lieu en dehors de la plage de temps pour laquelle nous stockons les détails des connexions à risque et des événements à risque. Nous ne faisons pas expirer le risque de l’utilisateur car les acteurs indélicats sont connus pour rester dans l’environnement des clients plus de 140 jours après une compromission d’identité avant d’attaquer. Les clients peuvent consulter la chronologie du risque de l’utilisateur pour comprendre pourquoi un utilisateur est exposé à un risque en accédant à : `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Pourquoi une connexion a-t-elle un indice de « risque de connexion (agrégat) » élevé lorsque les détections qui lui sont associées ont un risque faible ou moyen ?

L’indice de risque d’agrégat élevé peut être basé sur d’autres fonctionnalités de la connexion, ou sur le fait que plusieurs détections ont été déclenchées pour cette connexion. À l’inverse, une connexion peut avoir un risque de connexion (agrégat) moyen même si les détections associées à la connexion sont de risque élevé. 
