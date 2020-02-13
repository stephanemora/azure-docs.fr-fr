---
title: Provisionnement d’application en état de quarantaine | Microsoft Docs
description: Une fois que vous avez configuré une application à des fins de provisionnement d’utilisateurs automatique, découvrez ce qu’est un provisionnement en état de quarantaine et comment y mettre fin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00196d87b02eddb3b9cbc9f13d033ef8558c1ad8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77065916"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionnement d’application en état de quarantaine

Le service de provisionnement Azure AD supervise l’intégrité de votre configuration et place les applications qui ne sont pas saines en état de « quarantaine ». Si la plupart ou la totalité des appels effectués sur le système cible échouent systématiquement à cause d’une erreur, par exemple quand les informations d’identification ne sont pas valides, le travail de provisionnement est mis en quarantaine.

Lors d’une quarantaine, la fréquence des cycles incrémentiels est progressivement réduite à une occurrence par jour. Le travail de provisionnement sort de la quarantaine une fois que toutes les erreurs sont corrigées et que le cycle suivant de synchronisation démarre. Si le travail de provisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé (son exécution s’arrête).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Comment puis-je savoir si mon application est en quarantaine ?

Vous avez trois façons de vérifier si une application est en quarantaine :
  
- Dans le Portail Azure, accédez à **Azure Active Directory** > **Applications d’entreprise** > &lt;*nom de l’application*&gt; > **Provisionnement** et accédez à la barre de progression tout en bas.  

  ![Barre d’état de provisionnement présentant un état de quarantaine](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Utilisez la requête Microsoft Graph [Get synchronizationJob](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-get?view=graph-rest-beta&tabs=http) pour obtenir par programmation l’état du travail de provisionnement :

        `GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/`

- Consultez vos e-mails. Quand une application est mise en quarantaine, un e-mail de notification ponctuel est envoyé. Si la raison de la quarantaine change, un e-mail mis à jour est envoyé pour la stipuler. Si vous ne voyez aucun e-mail :

  - Vérifiez que vous avez spécifié un **E-mail de notification** valide dans la configuration du provisionnement de l’application.
  - Vérifiez qu’aucun filtre de courrier indésirable ne s’applique à la boîte de réception des e-mails de notification.
  - Vérifiez que vous ne vous êtes pas désabonné des e-mails.

## <a name="why-is-my-application-in-quarantine"></a>Pourquoi mon application est-elle en quarantaine ?

Une requête Microsoft Graph visant à obtenir l’état du travail de provisionnement indique la raison suivante pour la quarantaine :

- `EncounteredQuarantineException` indique que des informations d’identification non valides ont été fournies. Le service de provisionnement n’est pas en mesure d’établir une connexion entre le système source et le système cible.

- `EncounteredEscrowProportionThreshold` indique que le provisionnement a dépassé le seuil d’entiercement. Cette condition se produit quand plus de 60 % des événements de provisionnement ont échoué.

- `QuarantineOnDemand` signifie que nous avons détecté un problème avec votre application et que nous l’avons manuellement mise en quarantaine.

## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Comment puis-je sortir mon application de quarantaine ?

Tout d’abord, résolvez le problème qui a provoqué la mise en quarantaine de l’application.

- Vérifiez les paramètres de provisionnement de l’application pour vous assurer d’avoir [entré des informations d’identification d’administrateur valides](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD doit être capable d’établir une relation de confiance avec l’application cible. Vérifiez que vous avez entré des informations d’identification valides et que votre compte dispose des autorisations nécessaires.

- Passez en revue les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md) pour examiner de plus près les erreurs à l’origine de la quarantaine afin de les corriger. Accédez aux journaux d’approvisionnement dans le portail Microsoft Azure en accédant à **Azure Active Directory** &gt; **Applications d’entreprise** &gt; **Journaux d’approvisionnement (préversion)** dans la section **Activité**.

Une fois que vous avez résolu le problème, redémarrez le travail de provisionnement. Certaines modifications apportées aux paramètres de provisionnement de l’application, comme des mappages d’attributs ou des filtres d’étendue, redémarrent automatiquement le provisionnement. La barre de progression dans la page **Provisionnement** de l’application indique à quel moment le dernier provisionnement a démarré. Si vous avez besoin de redémarrer manuellement le travail de provisionnement, utilisez l’une des méthodes suivantes :  

- Utilisez le portail Azure pour redémarrer le travail de provisionnement. Dans la page **Provisionnement** de l’application, sous **Paramètres**, sélectionnez l’État **Effacer l’état en cours et redémarrer la synchronisation**, puis définissez **État du provisionnement** sur **Activé**. Cette action redémarre complètement le service de provisionnement, ce qui peut prendre un certain temps. Un cycle initial complet se réexécute, ce qui permet de supprimer les entiercements, de sortir l’application de quarantaine et d’effacer tous les filigranes.

- Utilisez Microsoft Graph pour [redémarrer le travail de provisionnement](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Vous bénéficiez d’un contrôle total sur ce que vous redémarrez. Vous pouvez choisir d’effacer les entiercements (pour redémarrer le compteur d’entiercements qui augmente jusqu’à l’état de quarantaine), de supprimer la quarantaine (pour sortir l’application de quarantaine) ou d’effacer les filigranes. Utilisez la requête suivante :
 
       `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`