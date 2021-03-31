---
title: Provisionnement d’application en état de quarantaine | Microsoft Docs
description: Une fois que vous avez configuré une application à des fins de provisionnement d’utilisateurs automatique, découvrez ce qu’est un provisionnement en état de quarantaine et comment y mettre fin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 49590c46588ad0d0f1c1b7b095679a3c3fce96eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579499"
---
# <a name="application-provisioning-in-quarantine-status"></a>Provisionnement d’application en état de quarantaine

Le service d’approvisionnement d’Azure AD supervise l’intégrité de votre configuration. Il place également les applications non saines dans un état de « quarantaine ». Si la plupart ou la totalité des appels effectués sur le système cible échouent systématiquement, le travail d’approvisionnement est marqué en quarantaine. Un exemple d’échec est une erreur reçue en raison d’informations d’identification d’administrateur non valides.

Lors de la mise en quarantaine :
- La fréquence des cycles incrémentiels est progressivement réduite à une fois par jour.
- Le travail de provisionnement sort de la quarantaine une fois que toutes les erreurs sont corrigées et que le cycle suivant de synchronisation démarre. 
- Si le travail de provisionnement reste en quarantaine pendant plus de quatre semaines, celui-ci est désactivé (son exécution s’arrête).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Comment puis-je savoir si mon application est en quarantaine ?

Vous avez trois façons de vérifier si une application est en quarantaine :
  
- Dans le Portail Azure, accédez à **Azure Active Directory** > **Applications d’entreprise** > &lt;*nom de l’application*&gt; > **Provisionnement** et vérifiez le message de mise en quarantaine dans la barre de progression.   

  ![Barre d’état de provisionnement présentant un état de quarantaine](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Dans le portail Azure, accédez à **Azure Active Directory** > **Journaux d’audit** > filtrez sur **Activité : quarantaine** et examinez l’historique de quarantaine. L’affichage de la barre de progression, comme décrit ci-dessus, indique si l’approvisionnement est actuellement en quarantaine. Les journaux d’audit affichent l’historique des quarantaines pour une application. 

- Utilisez la requête Microsoft Graph [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) pour obtenir par programmation l’état du travail de provisionnement :

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Consultez vos e-mails. Quand une application est mise en quarantaine, un e-mail de notification ponctuel est envoyé. Si la raison de la quarantaine change, un e-mail mis à jour est envoyé pour la stipuler. Si vous ne voyez aucun e-mail :

  - Vérifiez que vous avez spécifié un **e-mail de notification** valide dans la configuration de l’approvisionnement de l’application.
  - Vérifiez qu’aucun filtre de courrier indésirable ne s’applique à la boîte de réception des e-mails de notification.
  - Vérifiez que vous ne vous êtes pas désabonné des e-mails.
  - Rechercher les e-mails provenant de `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Pourquoi mon application est-elle en quarantaine ?

|Description|Action recommandée|
|---|---|
|**Problème de conformité SCIM :** Une réponse HTTP/404 introuvable a été retournée au lieu de la réponse HTTP/200 OK attendue. Dans ce cas, le service d’approvisionnement d’Azure AD a fait une demande à l’application cible et reçu une réponse inattendue.|Vérifiez la section Informations d’identification de l’administrateur. Voyez si l’application nécessite de spécifier l’URL du locataire et si l’URL est correcte. Si vous ne voyez pas de problème, contactez le développeur de l’application pour vous assurer que son service est conforme à la norme SCIM. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Informations d’identification non valides :** Lors d’une tentative d’autorisation d’accès à l’application cible, nous avons reçu une réponse de l’application cible qui indique que les informations d’identification fournies ne sont pas valides.|Accédez à la section Informations d’identification de l’administrateur de l’interface utilisateur de configuration de l’approvisionnement et autorisez à nouveau l’accès avec des informations d’identification valides. Si l’application se trouve dans la galerie, consultez le tutoriel sur la configuration de l’application pour connaître les autres étapes nécessaires.|
|**Rôles dupliqués :** Les rôles importés à partir de certaines applications, comme Salesforce et Zendesk, doivent être uniques. |Accédez au [manifeste](../develop/reference-app-manifest.md) de l’application dans le portail Azure et supprimez le rôle dupliqué.|

 Une requête Microsoft Graph visant à obtenir l’état du travail de provisionnement indique la raison suivante pour la quarantaine :
- `EncounteredQuarantineException` indique que des informations d’identification non valides ont été fournies. Le service de provisionnement n’est pas en mesure d’établir une connexion entre le système source et le système cible.
- `EncounteredEscrowProportionThreshold` indique que le provisionnement a dépassé le seuil d’entiercement. Cette condition se produit quand plus de 40 % des événements d’approvisionnement ont échoué. Pour plus d’informations, consultez les détails sur les seuils d’entiercement ci-dessous.
- `QuarantineOnDemand` signifie que nous avons détecté un problème avec votre application et que nous l’avons manuellement mise en quarantaine.

**Seuils d’entiercement**

Si le seuil d’entiercement proportionnel est atteint, le travail d’approvisionnement est mis en quarantaine. Cette logique est sujette à modification, mais fonctionne approximativement comme décrit ci-dessous : 

Un travail peut être mis en quarantaine, quel que soit le nombre d’échecs, pour des problèmes tels que les informations d’identification d’administrateur ou la conformité SCIM. Toutefois, en général, un minimum de 5 000 échecs est nécessaire pour commencer à envisager une mise en quarantaine en raison d’un trop grand nombre d’échecs. Par exemple, un travail ayant 4 000 échecs ne serait pas mis en quarantaine. Toutefois, un travail ayant 5 000 échecs déclencherait une évaluation. Une évaluation utilise les critères suivants :  
- Si plus de 40 % des événements d’approvisionnement échouent ou qu’il y a plus de 40 000 échecs, le travail d’approvisionnement est mis en quarantaine. Les échecs de référence ne sont pas comptabilisés dans le seuil de 40 % ou de 40 000 échecs. Par exemple, l’échec de la mise à jour d’un manager ou du membre d’un groupe est un échec de référence.
- Un travail où 45 000 utilisateurs n’ont pas été approvisionnés entraînerait une mise en quarantaine, car il dépasse le seuil de 40 000 échecs.
- Un travail où 30 000 utilisateurs n’ont pas été approvisionnés et où 5 000 autres ont été approvisionnés entraînerait une mise en quarantaine, car il dépasse le seuil de 40 % et le minimum de 5 000 échecs.
- Un travail avec 20 000 échecs et 100 000 réussites n’entraînerait pas de mise en quarantaine, car il n’a pas dépassé le seuil de 40 % d’échecs ni le nombre maximal de 40 000 échecs.  
- Il existe un seuil absolu de 60 000 échecs qui tient compte des échecs de référence et autres. Par exemple, 40 000 utilisateurs n’ont pas pu être approvisionnés et la mise à jour de 21 000 managers a échoué. Le total est de 61 000 échecs et dépasse la limite de 60 000 échecs.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Comment puis-je sortir mon application de quarantaine ?

Tout d’abord, résolvez le problème qui a provoqué la mise en quarantaine de l’application.

- Vérifiez les paramètres de provisionnement de l’application pour vous assurer d’avoir [entré des informations d’identification d’administrateur valides](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning). Azure AD doit établir une relation de confiance avec l’application cible. Vérifiez que vous avez entré des informations d’identification valides et que votre compte dispose des autorisations nécessaires.

- Passez en revue les [journaux de provisionnement](../reports-monitoring/concept-provisioning-logs.md) pour examiner de plus près les erreurs à l’origine de la quarantaine afin de les corriger. Accédez à **Azure Active Directory** &gt; **Applications d’entreprise** &gt; **Journaux d’approvisionnement (préversion)** dans la section **Activité**.

Une fois que vous avez résolu le problème, redémarrez le travail de provisionnement. Certaines modifications apportées aux paramètres de provisionnement de l’application, comme des mappages d’attributs ou des filtres d’étendue, redémarrent automatiquement le provisionnement. La barre de progression dans la page **Provisionnement** de l’application indique à quel moment le dernier provisionnement a démarré. Si vous avez besoin de redémarrer manuellement le travail de provisionnement, utilisez l’une des méthodes suivantes :  

- Utilisez le portail Azure pour redémarrer le travail de provisionnement. Dans la page **Approvisionnement** de l’application, sélectionnez **Redémarrer l’approvisionnement**. Cette action redémarre complètement le service de provisionnement, ce qui peut prendre un certain temps. Un cycle initial complet se réexécute, ce qui permet de supprimer les entiercements, de sortir l’application de quarantaine et d’effacer tous les filigranes. Le service réévalue ensuite tous les utilisateurs du système source et détermine s’ils sont dans l’étendue de provisionnement. Cela peut être utile quand votre application est placée sous contrôle, tel qu’indiqué dans cet article, ou que vous devez apporter un changement à vos mappages d’attributs. Notez que le cycle initial met plus de temps à s’effectuer que le cycle incrémentiel classique en raison du nombre d’objets à évaluer. Pour en savoir plus sur les performances des cycles initiaux et incrémentiels, cliquez [ici](application-provisioning-when-will-provisioning-finish-specific-user.md).

- Utilisez Microsoft Graph pour [redémarrer le travail de provisionnement](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Vous bénéficiez d’un contrôle total sur ce que vous redémarrez. Vous pouvez choisir d’effacer les entiercements (pour redémarrer le compteur d’entiercements qui augmente jusqu’à l’état de quarantaine), de supprimer la quarantaine (pour sortir l’application de quarantaine) ou d’effacer les filigranes. Utilisez la requête suivante :
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Remplacez « {ID} » par la valeur de l’ID de l’application et « {jobId} » par l’[ID du travail de synchronisation](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
