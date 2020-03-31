---
title: Contrôle de sécurité Azure - Réponse aux incidents
description: Réponse aux incidents du contrôle de sécurité
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: fb3560aa2d3fbf48ab63c4da4d3a8d69cb677209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934415"
---
# <a name="security-control-incident-response"></a>Contrôle de sécurité : Réponse aux incidents

Protégez les informations de l’organisation, ainsi que sa réputation, en développant et en implémentant une infrastructure de réponse aux incidents (par exemple, des plans, des rôles définis, une formation, des communications, une supervision de gestion) pour repérer rapidement une attaque et en contenir les dommages, en éliminant la présence de l’attaquant et en restaurant l’intégrité du réseau et des systèmes.

## <a name="101-create-an-incident-response-guide"></a>10.1 : Créer un guide de réponse aux incidents

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 10.1 | 19.1, 19.2, 19.3 | Customer |

Créez un guide de réponse aux incidents pour votre organisation. Assurez-vous qu’il existe des plans de réponse aux incidents écrits qui définissent tous les rôles du personnel, ainsi que les phases de gestion des incidents, depuis la détection jusqu’à la revue une fois l’incident terminé.

Comment configurer des automations de flux de travail dans Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Aide sur la création de votre propre processus de réponse aux incidents de sécurité :

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Anatomie d’un incident dans le centre de réponse aux incidents de sécurité Microsoft :

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Le client peut également tirer parti du guide de gestion des incidents de sécurité informatique du NIST pour faciliter la création de son propre plan de réponse aux incidents :

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 : Créer une procédure de notation et de classement des incidents

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 10.2 | 19,8 | Customer |

Security Center attribue un niveau de gravité à chaque alerte pour vous aider à hiérarchiser celles devant être examinées en premier. La gravité dépend du niveau de confiance que Security Center accorde à la recherche ou aux données analytiques utilisées pour émettre l’alerte, mais aussi de l’intention malveillante estimée de l’activité à l’origine de l’alerte.

En outre, marquez clairement les abonnements (par ex. production, non production) et créez un système d’attribution de noms pour identifier et classer les ressources Azure de façon claire.

## <a name="103-test-security-response-procedures"></a>10.3 : Tester les procédures de réponse de sécurité

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 10.3 | 19 | Customer |

Exécutez des exercices pour tester les fonctionnalités de réponse aux incidents de vos systèmes de façon régulière. Identifiez les points faibles et les lacunes, et révisez le plan en fonction des besoins.

Reportez-vous à la publication du NIST : Guide de test, de formation et programmes d’exercice pour les plans informatiques et les fonctionnalités :

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 : Fournir des informations de contact pour les incidents de sécurité et configurer des notifications d’alerte pour les incidents de sécurité

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 10,4 | 19.5 | Customer |

Les informations de contact d’incident de sécurité seront utilisées par Microsoft pour vous contacter si Microsoft Security Response Center (MSRC) découvre que les données du client ont été utilisées par un tiers illégal ou non autorisé.  Examinez les incidents après les faits pour vous assurer que les problèmes sont résolus.

Comment définir le contact de sécurité Azure Security Center :

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

## <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5 : Intégrer des alertes de sécurité à votre système de réponse aux incidents

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 10.5 | 19.6 | Customer |

Exportez vos alertes et recommandations d’Azure Security Center à l’aide de la fonctionnalité d’exportation continue. L’exportation continue vous permet d’exporter les alertes et les recommandations manuellement, ou automatiquement de manière continue. Vous pouvez utiliser le connecteur de données Azure Security Center pour diffuser la sentinelle des alertes.

Comment configurer l’exportation continue :

https://docs.microsoft.com/azure/security-center/continuous-export

Comment diffuser des alertes dans Azure Sentinel :

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

## <a name="106-automate-the-response-to-security-alerts"></a>10.6 : Automatiser la réponse aux alertes de sécurité

| Identifiant Azure | Identifiants CIS | Responsabilité |
|--|--|--|
| 10.6 | 19 | Customer |

Utilisez la fonctionnalité d’automatisation de flux de travail dans Azure Security Center pour déclencher automatiquement des réponses via &quot;Logic Apps&quot; sur les alertes de sécurité et les recommandations.

Comment configurer l’automatisation du flux de travail et Logic Apps :

https://docs.microsoft.com/azure/security-center/workflow-automation

## <a name="next-steps"></a>Étapes suivantes

Reportez-vous au contrôle de sécurité suivant : [Tests d’intrusion et exercices Red Team](security-control-penetration-tests-red-team-exercises.md)