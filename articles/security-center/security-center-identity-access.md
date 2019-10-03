---
title: Surveiller l’identité et l’accès dans Azure Security Center | Microsoft Docs
description: Découvrez comment utiliser les fonctionnalités d’identité et d’accès dans Azure Security Center pour surveiller les problèmes liés à l’activité d’accès et à l’identité de vos utilisateurs.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: memildin
ms.openlocfilehash: 8b069bedd3c36f27828e54a1110443ae6dfcdf3f
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268860"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Surveiller l’identité et l’accès dans Azure Security Center (Préversion)
Cet article vous aide à utiliser Azure Security Center pour surveiller l’identité et l’activité d’accès d’un utilisateur.

> [!NOTE]
> La surveillance de l’identité et de l’accès est uniquement disponible en préversion, au niveau Standard du Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>

L’identité doit être le plan de contrôle de votre entreprise. La protection de votre identité doit être votre priorité absolue. Le périmètre de sécurité a évolué d’un périmètre de réseau vers un périmètre d’identité. La sécurité se résume moins à la protection de votre réseau et plus à la défense de vos données, ainsi qu'à la gestion de la sécurité de vos applications et de vos utilisateurs. Désormais, comme de plus en plus d’applications et de données sont déplacées vers le cloud, l’identité devient le nouveau périmètre.

Le fait de surveiller vos activités d’identité vous permet de prendre des mesures avant qu’un événement ne survienne, ou des mesures réactives, pour contrer une tentative d’attaque. Le tableau de bord Identité et accès vous fournit des recommandations, telles que :

- Activer l’authentification multi-facteur pour les comptes privilégiés sur votre abonnement
- Supprimer les comptes externes disposant d’autorisations d’écriture de votre abonnement
- Supprimer des comptes externes disposant de privilèges de votre abonnement

> [!NOTE]
> Si votre abonnement comporte plus de 600 comptes, Security Center n’est pas en mesure d’exécuter les recommandations d’identité dans votre abonnement. Les recommandations qui ne sont pas exécutées sont listées sous « Évaluations non disponibles », comme indiqué ci-dessous.
Security Center ne peut pas exécuter les recommandations d’identité sur des agents d’administration d’un partenaire fournisseur de solutions Cloud.
>

Consultez [Recommandations](security-center-identity-access.md#recommendations) pour obtenir la liste des recommandations Identité et Accès fournie par Security Center.

## <a name="monitoring-security-health"></a>Surveillance de l’intégrité de la sécurité
Vous pouvez surveiller l’état de sécurité de vos ressources dans le tableau de bord **Security Center - Vue d’ensemble**. La section **Ressources** est un indicateur d’intégrité affichant les niveaux de gravité pour chaque type de ressource.

Vous pouvez consulter une liste de tous les problèmes en sélectionnant **Recommandations**. Sous **Ressources**, vous pouvez voir une liste de problèmes propres au calcul et aux applications, à la sécurité des données, aux réseaux ou encore à l’identité et à l’accès. Pour plus d’informations sur la façon d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md).

Pour obtenir une liste complète des recommandations d’identité et d’accès, consultez [Recommandations](security-center-identity-access.md#recommendations).

Pour continuer, sélectionnez **Identité et accès** sous **Ressources**, ou dans le menu principal de Security Center.

![Tableau de bord de Security Center][1]

## <a name="monitor-identity-and-access"></a>Surveiller l’identité et l’accès
Sous **Identité et accès**, se trouvent deux onglets :

- **Vue d’ensemble** : recommandations identifiées par Security Center.
- **Abonnements** : liste de vos abonnements et l’état de sécurité actuel de chacun d’eux.

![Identité et accès][2]

### <a name="overview-section"></a>Section Vue d’ensemble
Sous **Vue d’ensemble**, se trouve une liste de recommandations. La première colonne indique la recommandation. La deuxième colonne précise le nombre total d’abonnements qui sont concernés par cette recommandation. La troisième colonne renseigne sur la gravité du problème.

1. Sélectionnez une recommandation. La fenêtre des recommandations s’ouvre pour afficher les informations suivantes :

   - Description de la recommandation
   - Liste des abonnements intègres et non intègres
   - Liste des ressources non analysées en raison d’une évaluation ayant échoué, ou de la présence de la ressource sous un abonnement en cours d’exécution dans le niveau Gratuit et qui n’est pas évaluée

   ![Fenêtre de la recommandation][3]

1. Sélectionnez un abonnement dans la liste pour afficher des précisions supplémentaires.

### <a name="subscriptions-section"></a>Section Abonnements
Sous **Abonnements**, se trouve une liste d’abonnements. La première colonne liste les abonnements. La deuxième colonne précise le nombre total de recommandations pour chaque abonnement. La troisième colonne renseigne sur le niveau de gravité des problèmes.

![Onglet Abonnements][4]

1. Sélectionnez un abonnement. Une vue récapitulative s’ouvre avec trois onglets :

   - **Recommandations** : basées sur les évaluations effectuées par Security Center et qui ont échoué.
   - **Évaluations passées** : liste des évaluations effectuées par Security Center et qui ont réussi.
   - **Évaluations non disponibles** : liste des évaluations qui n’ont pas pu s’exécuter en raison d’une erreur, ou de la présence de plus de 600 comptes dans l’abonnement.

   Sous **Recommandations** se trouve une liste de recommandations pour l’abonnement sélectionné, avec le niveau de gravité pour chaque recommandation.

   ![Recommandations pour un abonnement sélectionné][5]

1. Sélectionnez une recommandation pour obtenir une description de la recommandation, une liste d’abonnements intègres et non intègres, et une liste de ressources non analysées.

   ![Description d’une recommandation][6]

   Sous **Évaluations passées** se trouve une liste d’évaluations réussies.  L’état de gravité de ces évaluations est toujours vert.

   ![Évaluations réussies][7]

1. Sélectionnez une évaluation réussie dans la liste pour obtenir une description de l’évaluation et une liste d’abonnements intègres. Il existe un onglet pour les abonnements non intègres, celui-ci liste tous les abonnements qui ont échoué.

   ![Évaluations réussies][8]

## <a name="recommendations"></a>Recommandations
Utilisez le tableau ci-dessous pour mieux comprendre les recommandations Identité et accès disponibles, et leurs effets.

|Type de ressource|Degré de sécurisation|Recommandation|Description|
|----|----|----|----|
|Subscription|50|L’authentification multifacteur doit être activée sur les comptes disposant d’autorisations de propriétaire sur votre abonnement|Activez Multi-Factor Authentication (MFA) pour tous les comptes de l’abonnement disposant de privilèges Administrateur, afin d’éviter toute violation de comptes ou de ressources.|
|Subscription|40|L’authentification multifacteur doit être activée sur les comptes d’abonnement disposant d’autorisations d’écriture|Activez Multi-Factor Authentication (MFA) pour tous les comptes de l’abonnement disposant de privilèges d’accès en écriture, afin d’éviter toute violation de comptes ou de ressources.|
|Subscription|30|Les comptes externes disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Supprimez de votre abonnement les comptes externes disposant d’autorisations de type propriétaire afin d’empêcher tout accès non contrôlé.|
|Subscription|30|L’authentification multifacteur doit être activée sur les comptes d’abonnement disposant d’autorisations de lecture|Activez Multi-Factor Authentication (MFA) pour tous les comptes de l’abonnement disposant de privilèges d’accès en lecture, afin d’éviter toute violation de comptes ou de ressources.|
|Subscription|25|Les comptes externes disposant d’autorisations d’écriture doivent être supprimés de votre abonnement|Supprimez de votre abonnement les comptes externes disposant d’autorisations d’accès en écriture afin d’empêcher tout accès non contrôlé. |
|Subscription|20|Les comptes déconseillés disposant d’autorisations de propriétaire doivent être supprimés de votre abonnement|Supprimez de vos abonnements les comptes déconseillés disposant d’autorisations de type propriétaire.|
|Subscription|5\.|Les comptes déconseillés doivent être supprimés de votre abonnement|Supprimez de vos abonnements les comptes déconseillés afin de restreindre l’accès aux utilisateurs actuels. |
|Subscription|5\.|Plusieurs propriétaires doivent être attribués à votre abonnement|Désignez plusieurs propriétaires d’abonnement pour assurer une redondance de l’accès administrateur.|
|Subscription|5\.|Trois propriétaires maximum doivent être désignés pour votre abonnement|Désignez moins de trois propriétaires d’abonnement afin de réduire le risque de violation par un propriétaire compromis.|
|Coffre de clés|5\.|Les journaux de diagnostic doivent être activés dans Key Vault|Activez les journaux d’activité et conservez-les un an maximum. Permet de recréer les pistes d’activité à des fins d’investigation en cas d’incident de sécurité ou de compromission du réseau. |
|Subscription|15|Les comptes externes disposant d’autorisations de lecture doivent être supprimés de votre abonnement|Supprimez de votre abonnement les comptes externes disposant de privilèges d’accès en lecture afin d’empêcher tout accès non contrôlé.| 

> [!NOTE]
> Si vous avez créé une stratégie d’accès conditionnel impliquant l’authentification MFA mais comportant des exclusions, l’évaluation faite par la recommandation MFA de Security Center considère la stratégie comme non conforme, car elle permet à certains utilisateurs de se connecter à Azure sans authentification MFA.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les articles suivants :

- [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
- [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
- [Protection de vos données et du service Azure SQL Database dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur Security Center, consultez les articles suivants :
* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Comprendre les alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [FAQ du Centre de sécurité Azure](security-center-faq.md). Cherchez des réponses aux questions fréquentes concernant l’utilisation de Security Center.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
