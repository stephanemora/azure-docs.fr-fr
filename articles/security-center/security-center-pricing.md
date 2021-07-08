---
title: Azure Security Center gratuit et Azure Defender activé
description: Découvrir les avantages de l’activation d’Azure Defender pour la protection des charges de travail cloud dans Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 06/10/2021
ms.openlocfilehash: 13f5bb8245ecc57c01d4236dfea7c9e4a2f7ae75
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111986315"
---
# <a name="azure-security-center-free-vs-azure-defender-enabled"></a>Azure Security Center gratuit et Azure Defender activé
Azure Defender est gratuit pendant les 30 premiers jours. Une fois les 30 jours écoulés, si vous décidez de continuer à utiliser le service, votre utilisation est automatiquement facturée.

Vous pouvez effectuer la mise à niveau à partir de la page **Tarification et paramètres**, comme décrit dans [Démarrage rapide : Activer Azure Defender](enable-azure-defender.md). Pour plus d’informations sur les prix dans la devise de votre choix et en fonction de votre région, consultez la page [Tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-are-the-benefits-of-enabling-azure-defender"></a>Quels sont les avantages de l’activation d’Azure Defender ?

Security Center est proposé en deux modes :

- **Azure Defender désactivé** (gratuit) : Security Center sans Azure Defender est activé gratuitement sur tous vos abonnements Azure quand vous consultez pour la première fois le tableau de bord Azure Security Center dans le portail Azure. Il peut aussi être activé par programmation par le biais d’une API. Ce mode vous donne accès à une stratégie de sécurité, à une fonctionnalité d'évaluation en continu de la sécurité et à des recommandations de sécurité exploitables pour vous aider à protéger vos ressources Azure.

- **Azure Defender activé** : Activer Azure Defender étend les fonctionnalités du mode Gratuit aux charges de travail exécutées dans des clouds privés et dans d’autres clouds publics, pour une gestion unifiée de la sécurité et une protection contre les menaces sur l’ensemble des charges de travail de cloud hybride. Voici quelques-unes des principales fonctionnalités d’Azure Defender :

    - **Microsoft Defender pour point de terminaison** : Azure Defender pour les serveurs comprend [Microsoft Defender pour point de terminaison](https://www.microsoft.com/microsoft-365/security/endpoint-defender) pour une totale protection évolutive des points de terminaison (EDR). Découvrez-en plus sur les avantages de l’utilisation de Microsoft Defender pour point de terminaison avec Azure Defender dans [Utiliser la solution de détection de point de terminaison et réponse (EDR) de Security Center](security-center-wdatp.md).
    - **Analyse des vulnérabilités pour les machines virtuelles et registres de conteneurs** : déployez facilement un scanneur sur toutes vos machines virtuelles qui offre la solution de gestion des vulnérabilités la plus avancée du secteur. Affichez, examinez et corrigez les résultats directement dans Security Center. 
    - **Sécurité hybride** : obtenez une vue unifiée de la sécurité sur l’ensemble de vos charges de travail cloud et locales. Appliquez des stratégies de sécurité et évaluez en continu la sécurité de vos charges de travail cloud hybrides pour garantir la conformité aux normes de sécurité. Collectez, recherchez et analysez des données de sécurité à partir d’un large éventail de sources, dont les pare-feu et autres solutions partenaires.
    - **Alertes de protection contre les menaces** : les analyses comportementales avancées et Microsoft Intelligent Security Graph offrent une longueur d’avance face à l’évolution des attaques informatiques. Les analytiques comportementales intégrées et le machine learning peuvent identifier les attaques et les vulnérabilités zero-day. Surveillez les réseaux, machines et services cloud pour prévenir les attaques entrantes et les activités consécutives à une violation. Simplifiez l’investigation avec des outils interactifs et des informations sur les menaces contextuelles.
    - **Contrôles d’accès et d’application** (AAC) : bloquez les programmes malveillants et les autres applications indésirables en appliquant des suggestions de mise en liste verte/noire adaptées à vos charges de travail spécifiques et alimentées par machine learning. Réduisez la surface d’attaque réseau grâce à un accès contrôlé et juste à temps aux ports de gestion sur les machines virtuelles Azure. Les contrôles d’accès et d’application réduisent considérablement l’exposition à la force brute et à d’autres attaques réseau.
    - **Fonctionnalités de sécurité des conteneurs** : tirez parti de la gestion des vulnérabilités et de la protection contre les menaces en temps réel sur vos environnements en conteneur. Lors de l’activation d’**Azure Defender pour les registres de conteneurs**, 12 heures peuvent s’écouler avant que toutes les fonctionnalités soient activées. Les frais sont basés sur le nombre d’images conteneur uniques transmises à votre registre connecté. Une fois qu’une image a été analysée une fois, elle ne sera plus facturée à moins qu’elle ne soit modifiée et transmise une fois de plus.
    - **Protection étendue contre les menaces pour les ressources connectées à l’environnement Azure** – Azure Defender offre une protection étendue contre les menaces natives Azure pour les services Azure communs à toutes vos ressources : Azure Resource Manager, Azure DNS, la couche réseau Azure et Azure Key Vault. Offrant une visibilité unique sur la couche de gestion Azure et la couche Azure DNS, Azure Defender peut protéger les ressources cloud connectées à ces couches.


## <a name="faq---pricing-and-billing"></a>FAQ – Prix et facturation 

- [Comment puis-je suivre qui a effectué des modifications au niveau d’Azure Defender dans Security Center dans mon organisation ?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [Quels sont les plans proposés par Security Center ?](#what-are-the-plans-offered-by-security-center)
- [Comment activer Azure Defender pour mon abonnement ?](#how-do-i-enable-azure-defender-for-my-subscription)
- [Puis-je activer Azure Defender pour des serveurs sur un sous-ensemble de serveurs dans mon abonnement ?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Si j’ai déjà une licence Microsoft Defender for Endpoint, puis-je bénéficier d’une remise pour Azure Defender ?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Azure Defender pour les serveurs est activé pour Mon abonnement, dois-je payer pour les serveurs qui ne sont pas en cours d’exécution ?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [Les machines sur lesquelles l’agent Log Analytics n’est pas installé seront-elles facturées ?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, est-il facturé deux fois ?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, l’ingestion de données gratuite de 500 Mo est-elle disponible sur chacun d’eux ?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [L’ingestion de données gratuite de 500 Mo est-elle calculée pour un espace de travail entier ou strictement machine par machine ?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)
- [Quels sont les types de données inclus dans l’allocation quotidienne de données de 500 Mo ?](#what-data-types-are-included-in-the-500-mb-data-daily-allowance)


### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>Comment puis-je suivre qui a effectué des modifications au niveau d’Azure Defender dans Security Center dans mon organisation ?
Des abonnements Azure peuvent avoir plusieurs administrateurs autorisés à modifier les paramètres de tarification. Pour savoir quel utilisateur a effectué une modification, utilisez le journal d’activité Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Journal d’activité Azure montrant un événement de modification de tarif":::

Si les informations de l’utilisateur ne sont pas listées dans la colonne **Événement lancé par**, explorez les données JSON de l’événement pour obtenir les détails correspondants.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Explorateur JSON du journal d’activité Azure":::


### <a name="what-are-the-plans-offered-by-security-center"></a>Quels sont les plans proposés par Security Center ? 
Security Center présente deux offres : 

- Azure Security Center gratuit 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>Comment activer Azure Defender pour mon abonnement ? 
Vous pouvez utiliser l’une des méthodes suivantes pour activer Azure Defender pour votre abonnement : 

| Méthode                                          | Instructions                                                                                                                                       |
|-------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Pages Azure Security Center du portail Azure | [Activation d’Azure Defender](enable-azure-defender.md)                                                                                                  |
| API REST                                        | [API pricings](/rest/api/securitycenter/pricings)                                                                                                  |
| Azure CLI                                       | [az security pricing](/cli/azure/security/pricing)                                                                                                 |
| PowerShell                                      | [Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)                                                                      |
| Azure Policy                                    | [Bundle Pricings](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json) |
|                                                 |                                                                                                                                                    |

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>Puis-je activer Azure Defender pour des serveurs sur un sous-ensemble de serveurs dans mon abonnement ?
Non. Lorsque vous activez [Azure Defender pour les serveurs](defender-for-servers-introduction.md) sur un abonnement, tous les serveurs de l’abonnement sont protégés par Azure Defender. 

Une alternative consiste à activer Azure Defender pour les serveurs au niveau de l’espace de travail Log Analytics. Dans ce cas, seuls les serveurs qui rapportent à cet espace de travail seront protégés et facturés. Toutefois, plusieurs fonctionnalités ne seront pas disponibles. Cela inclut l’accès juste-à-temps aux machines virtuelles, les détections réseau, la conformité réglementaire, le renforcement réseau adaptatif, le contrôle d’application adaptatif, etc. 

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Si j’ai déjà une licence Microsoft Defender for Endpoint, puis-je bénéficier d’une remise pour Azure Defender ?
Si vous disposez déjà d’une licence pour Microsoft Defender for Endpoint, vous n’aurez pas à payer pour cette partie de votre licence Azure Defender.

Pour demander votre remise, contactez l’équipe de support de Security Center et fournissez l’ID d’espace de travail, la région et le nombre de licences Microsoft Defender for Endpoint qui sont appliquées sur les ordinateurs de l’espace de travail donné.

La remise est effective à compter de la date d’approbation et ne sera pas rétroactive.

### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Azure Defender pour les serveurs est activé pour Mon abonnement, dois-je payer pour les serveurs qui ne sont pas en cours d’exécution ? 
Non. Quand vous activez [Azure Defender pour les serveurs](defender-for-servers-introduction.md) sur un abonnement, vous n’êtes pas facturé pour les machines qui sont dans l’état d’alimentation désalloué pendant qu’elles sont dans cet état. Les machines sont facturées en fonction de leur état d’alimentation comme indiqué dans le tableau suivant :

| State        | Description                                                                                                                                      | Facturation de l’utilisation de l’instance |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Démarrage en cours     | La machine virtuelle démarre.                                                                                                                               | Pas de facturation            |
| Exécution en cours      | État de fonctionnement normal d’une machine virtuelle                                                                                                                    | Facturation                |
| En cours d’arrêt     | Il s’agit d’un état transitoire. À l’issue de l’opération, l’état indiqué est Arrêté.                                                                           | Facturation                |
| Arrêté      | La machine virtuelle a été arrêtée à partir du système d’exploitation invité ou à l’aide des API PowerOff. Le matériel est toujours alloué à la machine virtuelle et demeure sur l’hôte. | Facturation                |
| Libération | État transitoire. À l’issue de l’opération, la machine virtuelle indique l’état Désalloué.                                                                             | Pas de facturation            |
| Libéré  | La machine virtuelle a été arrêtée et supprimée de l’hôte.                                                                                  | Pas de facturation            |

:::image type="content" source="media/security-center-pricing/deallocated-virtual-machines.png" alt-text="Machines virtuelles Azure montrant une machine désallouée":::

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>Les machines sur lesquelles l’agent Log Analytics n’est pas installé seront-elles facturées ?
Oui. Lorsque vous activez [Azure Defender pour les serveurs](defender-for-servers-introduction.md) sur un abonnement, les machines de cet abonnement bénéficient d’une série de protections, même si vous n’avez pas installé l’agent Log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, est-il facturé deux fois ? 
Oui. Si vous avez configuré votre agent Log Analytics pour envoyer des données à deux espaces de travail de Log Analytics différents ou plus (multihébergement), chaque espace de travail sur lequel est installée une solution « sécurité » ou « logiciel anti-programme malveillant » vous est facturé. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Si un agent Log Analytics émet un rapport dans plusieurs espaces de travail, l’ingestion de données gratuite de 500 Mo est-elle disponible sur chacun d’eux ?
Oui. Si vous avez configuré votre agent Log Analytics pour envoyer des données à deux espaces de travail Log Analytics différents ou plus (multihébergement), vous obtenez une ingestion de données gratuite de 500 Mo. Cela est calculé par nœud, par espace de travail signalé, par jour et disponible pour chaque espace de travail sur lequel la solution « Sécurité » ou « Logiciel anti-programme malveillant » est installée. Toutes les données ingérées au-delà de la limite de 500 Mo vous seront facturées.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>L’ingestion de données gratuite de 500 Mo est-elle calculée pour un espace de travail entier ou strictement machine par machine ?
Vous obtiendrez une ingestion de données gratuite de 500 Mo par jour, pour chaque machine connectée à l’espace de travail. Spécifiquement pour les types de données de sécurité directement collectés par Azure Security Center.

Ces données correspondent à un tarif journalier moyenné sur l’ensemble des nœuds. Par conséquent, même si certaines machines envoient 100 Mo et que d’autres envoient 800 Mo, si le total ne dépasse pas la limite de gratuité **[nombre de machines] x 500 Mo**, aucun supplément ne vous est facturé.

### <a name="what-data-types-are-included-in-the-500-mb-data-daily-allowance"></a>Quels sont les types de données inclus dans l’allocation quotidienne de données de 500 Mo ?

La facturation de Security Center est étroitement liée à la facturation de Log Analytics. Security Center permet une attribution de 500 Mo/nœud/jour par rapport au sous-ensemble de [types de données de sécurité](/azure/azure-monitor/reference/tables/tables-category#security)suivant :
- SecurityAlert
- SecurityBaseline
- SecurityBaselineSummary
- SecurityDetection
- SecurityEvent
- WindowsFirewall
- MaliciousIPCommunication
- SysmonEvent
- ProtectionStatus
- Les types de données Update et UpdateSummary sont activés lorsque la solution Update Management n’est pas exécutée sur l’espace de travail ou le ciblage de solution

Si l’espace de travail est au niveau tarifaire hérité Par nœud, les allocations du Centre de sécurité et de Log Analytics sont combinées et appliquées conjointement à toutes les données ingérées facturables.

## <a name="next-steps"></a>Étapes suivantes
Cet article a décrit les options tarifaires de Security Center. Consultez les documents connexes suivants :

- [Optimisation des coûts de votre charge de travail Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Détails de la tarification dans la devise de votre choix et en fonction de votre région](https://azure.microsoft.com/pricing/details/security-center/)
- Vous pouvez gérer vos coûts et limiter la quantité de données collectées pour une solution en limitant celle-ci à un ensemble spécifique d’agents. Le [ciblage de solution](../azure-monitor/insights/solution-targeting.md) vous permet d’appliquer une étendue à la solution et de cibler un sous-ensemble d’ordinateurs dans l’espace de travail. Si vous utilisez le ciblage de solution, Security Center liste l’espace de travail comme n’ayant pas de solution.