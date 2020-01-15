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
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552982"
---
# <a name="monitor-identity-and-access-preview"></a>Superviser l’identité et l’accès (préversion)
Lorsque Security Center identifie des failles de sécurité potentielles, il crée des suggestions qui vous guident tout au long du processus de configuration des contrôles nécessaires afin de renforcer et protéger vos ressources.

Cet article explique la page **Identité et accès** de la section de sécurité des ressources d’Azure Security Center.

Pour obtenir la liste complète des recommandations que vous pouvez voir sur cette page, consultez [Recommandations relatives à l’identité et à l’accès](recommendations-reference.md#recs-identity).

> [!NOTE]
> La surveillance de l’identité et de l’accès est uniquement disponible en préversion, au niveau Standard du Security Center. Consultez [Tarification](security-center-pricing.md) pour en savoir plus sur les niveaux tarifaires de Security Center.
>

L’identité doit être le plan de contrôle de votre entreprise. La protection des identités doit être votre priorité absolue. Le périmètre de sécurité a évolué d’un périmètre de réseau vers un périmètre d’identité. La sécurité se résume moins à la protection de votre réseau et plus à la défense de vos données, ainsi qu'à la gestion de la sécurité de vos applications et de vos utilisateurs. Désormais, comme de plus en plus d’applications et de données sont déplacées vers le cloud, l’identité devient le nouveau périmètre.

Le fait de surveiller vos activités d’identité vous permet de prendre des mesures avant qu’un événement ne survienne, ou des mesures réactives, pour contrer une tentative d’attaque. Le tableau de bord Identité et accès vous fournit des recommandations, telles que :

- Activer l’authentification multi-facteur pour les comptes privilégiés sur votre abonnement
- Supprimer les comptes externes disposant d’autorisations d’écriture de votre abonnement
- Supprimer des comptes externes disposant de privilèges de votre abonnement

> [!NOTE]
> Si votre abonnement comporte plus de 600 comptes, Security Center n’est pas en mesure d’exécuter les recommandations d’identité dans votre abonnement. Les recommandations qui ne sont pas exécutées sont listées sous « Évaluations non disponibles » ci-dessous.
Security Center ne peut pas exécuter les recommandations d’identité sur des agents d’administration d’un partenaire fournisseur de solutions Cloud.
>

## <a name="monitor-identity-and-access"></a>Surveiller l’identité et l’accès

Ouvrez la liste des problèmes identifiés liés à l’identité et à l’accès en sélectionnant **Identité et accès** dans la barre latérale Security Center (sous **Ressources**) ou à partir de la page de vue d’ensemble. 

Sous **Identité et accès**, se trouvent deux onglets :

- **Vue d’ensemble** : recommandations identifiées par Security Center.
- **Abonnements** : liste de vos abonnements et l’état de sécurité actuel de chacun d’eux.

[![Identité et accès](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>Section Vue d’ensemble
Sous **Vue d’ensemble**, se trouve une liste de recommandations. La première colonne indique la recommandation. La deuxième colonne précise le nombre total d’abonnements qui sont concernés par cette recommandation. La troisième colonne renseigne sur la gravité du problème.

1. Sélectionnez une recommandation. La fenêtre des recommandations s’ouvre pour afficher les informations suivantes :

   - Description de la recommandation
   - Liste des abonnements intègres et non intègres
   - Liste des ressources non analysées en raison d’une évaluation ayant échoué, ou de la présence de la ressource sous un abonnement en cours d’exécution dans le niveau Gratuit et qui n’est pas évaluée

    [![Fenêtre Recommandations](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. Sélectionnez un abonnement dans la liste pour afficher des précisions supplémentaires.

### <a name="subscriptions-section"></a>Section Abonnements
Sous **Abonnements**, se trouve une liste d’abonnements. La première colonne liste les abonnements. La deuxième colonne précise le nombre total de recommandations pour chaque abonnement. La troisième colonne renseigne sur le niveau de gravité des problèmes.

[![Onglet Abonnements](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. Sélectionnez un abonnement. Une vue récapitulative s’ouvre avec trois onglets :

   - **Recommandations** : basées sur les évaluations effectuées par Security Center et qui ont échoué.
   - **Évaluations passées** : liste des évaluations effectuées par Security Center et qui ont réussi.
   - **Évaluations non disponibles** : liste des évaluations qui n’ont pas pu s’exécuter en raison d’une erreur, ou de la présence de plus de 600 comptes dans l’abonnement.

   Sous **Recommandations** se trouve une liste de recommandations pour l’abonnement sélectionné, avec le niveau de gravité pour chaque recommandation.

   [![Recommandations pour un abonnement sélectionné](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. Sélectionnez une recommandation pour obtenir une description de la recommandation, une liste d’abonnements intègres et non intègres, et une liste de ressources non analysées.

   [![Description d’une recommandation](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   Sous **Évaluations passées** se trouve une liste d’évaluations réussies.  L’état de gravité de ces évaluations est toujours vert.

   [![Évaluations réussies](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. Sélectionnez une évaluation réussie dans la liste pour obtenir une description de l’évaluation et une liste d’abonnements intègres. Il existe un onglet pour les abonnements non intègres, celui-ci liste tous les abonnements qui ont échoué.

   [![Évaluations réussies](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> Si vous avez créé une stratégie d’accès conditionnel impliquant l’authentification MFA mais comportant des exclusions, l’évaluation faite par la recommandation MFA de Security Center considère la stratégie comme non conforme, car elle permet à certains utilisateurs de se connecter à Azure sans authentification MFA.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les articles suivants :

- [Protection de vos machines et de vos applications dans Azure Security Center](security-center-virtual-machine-protection.md)
- [Protection de votre réseau dans Azure Security Center](security-center-network-recommendations.md)
- [Protection de vos données et du service Azure SQL Database dans Azure Security Center](security-center-sql-service-recommendations.md)