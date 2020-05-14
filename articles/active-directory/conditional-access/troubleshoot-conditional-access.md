---
title: Résolution des problèmes de connexion avec l’accès conditionnel - Azure Active Directory
description: Cet article décrit la procédure à suivre lorsque vos stratégies d’accès conditionnel entraînent des résultats inattendus
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9a2ab4b1ebc1c958be9dc4bd07a010f7fef8afc
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610506"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Résolution des problèmes de connexion avec l’accès conditionnel

Les informations contenues dans cet article peuvent être utilisées pour résoudre des problèmes de connexion inattendus liés à l’accès conditionnel à l’aide des messages d’erreur et du journal des connexions Azure AD.

## <a name="conditional-access-sign-in-interrupt"></a>Interruption de connexion avec l’accès conditionnel

Vous devez tout d’abord consulter le message d’erreur qui s’affiche. Pour les problèmes de connexion lors de l’utilisation d’un navigateur web, la page d’erreur elle-même contient des informations détaillées. Ces informations peuvent décrire le problème et suggérer une solution.

![Erreur de connexion - Appareil conforme requis](./media/troubleshoot-conditional-access/image1.png)

Dans l’erreur ci-dessus, le message indique que l’application est accessible uniquement à partir d’appareils ou d’applications clientes qui respectent la stratégie de gestion des appareils mobiles de l’entreprise. Dans le cas présent, l’application et l’appareil ne sont pas conformes à cette stratégie.

## <a name="azure-ad-sign-in-events"></a>Événements de connexion Azure AD

La deuxième méthode permettant d’obtenir des informations détaillées sur l’interruption de connexion consiste à passer en revue les événements de connexion Azure AD pour savoir quelles stratégies d’accès conditionnel ont été appliquées et pourquoi.

Pour plus d’informations sur le problème, cliquez sur **Plus de détails** dans la page d’erreur initiale. Cliquez sur **Plus de détails** pour afficher des informations de dépannage utiles lors de la recherche de l’événement d’échec spécifique que l’utilisateur a vu dans les événements de connexion Azure AD ou lors de l’ouverture d’un incident de support auprès de Microsoft.

![Plus d’informations sur la connexion interrompue d’un navigateur web avec l’accès conditionnel.](./media/troubleshoot-conditional-access/image2.png)

Procédez comme suit pour trouver quelles stratégies d’accès conditionnel ont été appliquées et pourquoi.

1. Connectez-vous au **portail Azure** en tant qu’administrateur général, administrateur de la sécurité ou lecteur général.
1. Accédez à **Azure Active Directory** > **Connexions**.
1. Recherchez l’événement de connexion à vérifier. Ajoutez ou supprimez des filtres et des colonnes pour filtrer les informations inutiles.
   1. Ajoutez des filtres pour limiter l’étendue :
      1. **ID de corrélation** lorsque vous avez un événement spécifique à examiner.
      1. **Accès conditionnel** pour voir l’échec et la réussite de la stratégie. Pour limiter les résultats, restreignez votre filtre afin de n’afficher que les échecs.
      1. **Nom d’utilisateur** pour afficher des informations relatives à des utilisateurs spécifiques.
      1. **Date** limitée au laps de temps en question.

   ![Sélection du filtre d’accès conditionnel dans le journal des connexions](./media/troubleshoot-conditional-access/image3.png)

1. Une fois que l’événement de connexion qui correspond à l’échec de connexion de l’utilisateur a été trouvé, sélectionnez l’onglet **Accès conditionnel**. L’onglet Accès conditionnel affiche la ou les stratégies spécifiques qui ont abouti à l’interruption de la connexion.
   1. Les informations de l’onglet **Dépannage et support** peuvent indiquer clairement pourquoi une connexion a échoué, par exemple un appareil ne respectant pas les exigences de conformité.
   1. Pour approfondir vos recherches, explorez la configuration des stratégies en cliquant sur **Nom de la stratégie**. Cliquez sur **Nom de la stratégie** pour afficher l’interface utilisateur de configuration de la stratégie pour la stratégie sélectionnée à des fins de révision et de modification.
   1. **L’utilisateur client** et les **détails sur l’appareil** qui ont été utilisés pour l’évaluation de la stratégie d’accès conditionnel sont également disponibles dans les onglets **Informations de base**, **Emplacement**, **Informations sur l’appareil**, **Détails d’authentification** et **Détails supplémentaires** de l’événement de connexion.
   1. La sélection des points de suspension à droite de la stratégie permet d’afficher les détails de la stratégie. Les administrateurs peuvent ainsi obtenir des informations supplémentaires sur la raison pour laquelle une stratégie a été correctement appliquée ou non.

   ![Événement de connexion - Onglet Accès conditionnel](./media/troubleshoot-conditional-access/image5.png)

   ![Détails de la stratégie (préversion)](./media/troubleshoot-conditional-access/policy-details.png)

Si les informations de l’événement ne suffisent pas à comprendre les résultats de la connexion ou à ajuster la stratégie pour obtenir les résultats souhaités, il est possible d’ouvrir un incident de support. Accédez à l’onglet **Dépannage et support** de cet événement de connexion, puis sélectionnez **Créer une demande de support**.

![Onglet Dépannage et support de l’événement de connexion](./media/troubleshoot-conditional-access/image6.png)

Lors de l’envoi de l’incident, fournissez l’ID de la demande, ainsi que l’heure et la date de l’événement de connexion dans les détails d’envoi de l’incident. Ces informations permettent au support Microsoft de trouver l’événement qui vous intéresse.

### <a name="conditional-access-error-codes"></a>Codes d’erreur pour l’accès conditionnel

| Code d’erreur de connexion | Chaîne d’erreur |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>Étapes suivantes

- [Rapports d’activité de connexion dans le portail Azure Active Directory](../reports-monitoring/concept-sign-ins.md)
- [Résolution des problèmes d’accès conditionnel à l’aide de l’outil What If](troubleshoot-conditional-access-what-if.md)
- Bonnes pratiques relatives à [l’accès conditionnel dans Azure Active Directory](best-practices.md)
