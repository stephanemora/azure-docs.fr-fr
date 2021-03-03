---
title: Géorécupération d’urgence Azure Service Bus | Microsoft Docs
description: Utiliser les régions géographiques pour le basculement et la récupération d’urgence dans Azure Service Bus
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: 86d35465e5b31514f4d215095932b857ce7dcb35
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384300"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Géorécupération d’urgence Azure Service Bus

La résilience contre les pannes désastreuses des ressources de traitement des données constitue une exigence pour de nombreuses entreprises. Dans certains cas, elle est même requise par les réglementations sectorielles. 

Azure Service Bus répartit déjà le risque de défaillances catastrophiques d’ordinateurs individuels ou même de racks complets sur différents clusters couvrant plusieurs domaines d’échec au sein d’un centre de données. Le service implémente des mécanismes transparents de détection des défaillances et de basculement. Il continue ainsi de fonctionner selon les niveaux de service garantis et, en général, sans interruptions perceptibles lorsque de telles défaillances se produisent. Si un espace de noms Service Bus a été créé avec l’option [Zones de disponibilité](../availability-zones/az-overview.md) activée, le risque de panne est davantage réparti sur trois sites physiquement séparés. Le service dispose par ailleurs de réserves de capacité suffisantes pour faire face instantanément à la perte complète et irrémédiable d’un site complet. 

Le modèle de cluster Azure Service Bus tout actif avec prise en charge des zones de disponibilité est supérieur à tous les produits de type répartiteurs de messages locaux en termes de résilience face à des défaillances matérielles graves, voire à une perte catastrophique de sites de centres de données entiers. Il peut néanmoins se produire des situations graves impliquant une destruction physique généralisée contre laquelle même ces mesures ne suffisent pas. 

La fonctionnalité de géo-reprise d’activité après sinistre de Service Bus est conçue pour faciliter la récupération après un sinistre de cette ampleur et l’abandon définitif d’une région Azure défaillante, sans qu’il soit nécessaire de modifier la configuration des applications. L’abandon d’une région Azure implique généralement plusieurs services. Cette fonctionnalité vise principalement à préserver l’intégrité de la configuration des applications composites. La fonctionnalité est disponible de manière globale pour la référence SKU Premium Service Bus. 

La fonctionnalité de géo-reprise d’activité après sinistre garantit que la totalité de la configuration d’un espace de noms (files d’attente, rubriques, abonnements et filtres) est répliquée en continu d’un espace de noms principal vers un espace de noms secondaire quand ils sont couplés. Elle permet également de lancer à tout moment un basculement ponctuel vers l’espace de noms secondaire. L’action de basculement fait pointer le nom d’alias choisi pour l’espace de noms vers l’espace de noms secondaire, puis arrête le jumelage. Le basculement est presque instantané une fois lancé. 

> [!IMPORTANT]
> La fonctionnalité permet une continuité instantanée des opérations avec la même configuration, mais **ne réplique pas les messages conservés dans les files d’attente, les abonnements aux rubriques ni les files d’attente de lettres mortes**. Pour préserver la sémantique des files d’attente, une telle réplication impose non seulement la réplication des données de message, mais aussi de chacun des changements d’état dans le répartiteur. Pour la plupart des espaces de noms Service Bus, le trafic de réplication requis dépasserait de beaucoup le trafic d’application. Par ailleurs, avec des files d’attente à débit élevé, la plupart des messages seraient toujours répliqués sur l’espace de noms secondaire alors qu’ils seraient déjà en cours de suppression de l’espace de noms principal. En résulterait un trafic inutile excessif. Dans le cas des itinéraires de réplication à latence élevée, qui s’appliquent à de nombreux couplages possibles pour la géo-reprise d’activité après sinistre, il peut également se révéler impossible que le trafic de réplication suive le trafic d’application en raison des effets de limitation induits par la latence.
 
> [!TIP]
> Pour répliquer le contenu des files d’attente et des abonnements aux rubriques et gérer les espaces de noms correspondants dans des configurations actives/actives de façon à faire face aux pannes et aux sinistres, ne vous contentez pas d’utiliser cette fonctionnalité de géo-reprise d’activité après sinistre. Suivez les [conseils de réplication](service-bus-federation-overview.md).  

## <a name="outages-and-disasters"></a>Pannes et sinistres

Il est important de noter la différence entre « panne » et « sinistre ». 

Une *panne* se définit comme l’indisponibilité temporaire d’Azure Service Bus. La panne impacte certains composants du service, comme une banque de messages, ou le centre de données entier. Toutefois, une fois le problème résolu, Service Bus redevient disponible. En règle générale, une panne ne provoque aucune perte de messages ou d’autres données. Une coupure de courant dans le centre de données est un exemple de panne. Certaines pannes sont uniquement dues à une courte perte de connexion résultant de problèmes de réseau ou d’autres perturbations temporaires. 

Un *sinistre* se définit comme une perte définitive ou à long terme d’un cluster Service Bus, d’une région Azure ou d’un centre de données. La région ou le centre de données peut redevenir disponible ou pas, et rester arrêté(e) pendant plusieurs heures ou jours. Les incendies, les inondations ou les tremblements de terre sont des exemples de sinistres. Un sinistre qui devient permanent peut entraîner la perte de certains messages, événements ou d’autres données. Toutefois, dans la plupart des cas, il ne doit y avoir aucune perte de données et les messages peuvent être récupérés une fois que le centre de données est sauvegardé.

La fonctionnalité de géorécupération d’urgence d’Azure Service Bus est une solution de récupération d’urgence. Les concepts et le workflow décrits dans cet article concernent des scénarios de sinistres, pas des pannes temporaires ou transitoires. Pour obtenir une présentation détaillée de la récupération d’urgence dans Microsoft Azure, consultez [cet article](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Concepts et terminologie de base

La fonctionnalité de récupération d’urgence implémente la récupération d’urgence des métadonnées, en s’appuyant sur les espaces de noms de récupération d’urgence principal et secondaire. La fonctionnalité de géo-reprise d’activité après sinistre est disponible uniquement pour le [niveau tarifaire Premium](service-bus-premium-messaging.md). Vous n’avez pas besoin de modifier de chaîne de connexion, car la connexion est établie à l’aide d’un alias.

Cet article emploie les termes suivants :

-  *Alias* : nom d’une configuration de récupération d’urgence que vous avez configurée. L’alias fournit une chaîne de connexion de nom de domaine complet (FQDN) stable. Les applications utilisent cet alias de chaîne de connexion pour se connecter à un espace de noms. L’utilisation d’un alias garantit que la chaîne de connexion restera inchangée après le déclenchement du basculement.

-  *Espace de noms principal/secondaire* : espaces de noms qui correspondent à l’alias. L’espace de noms principal est « actif » et reçoit les messages (il peut s’agir d’un espace de noms existant ou nouveau). L’espace de noms secondaire est « passif » et ne reçoit pas de messages. Les métadonnées sont synchronisées entre ces deux espaces de noms, qui peuvent ainsi accepter facilement les messages sans aucune modification du code d’application ou de la chaîne de connexion. Pour vous assurer que seul l’espace de noms actif reçoit des messages, vous devez utiliser l’alias. 

    > [!IMPORTANT]
    > La fonctionnalité de géo-reprise d’activité après sinistre nécessite que l’abonnement et le groupe de ressources soient identiques pour les espaces de noms principal et secondaire.
-  *Métadonnées* : entités telles que les files d'attentes, les rubriques et les abonnements ; incluent également leurs propriétés sur le service associé à l'espace de noms. Seules les entités et leurs paramètres sont automatiquement répliqués. Les messages ne sont pas répliqués.

-  *Basculement* : processus d’activation de l’espace de noms secondaire.

## <a name="setup"></a>Programme d’installation

La section suivante est une présentation de l’association de deux espaces de noms.

![1][]

Tout d’abord, vous créez ou utilisez un espace de noms principal existant et un espace de noms secondaire, avant d’associer les deux. Cette association crée un alias qui vous servira à vous connecter. Étant donné que vous utilisez un alias, vous n’avez pas besoin de modifier les chaînes de connexion existantes. Vous pouvez uniquement ajouter de nouveaux espaces de noms à votre association de basculement. 

1. Créez l’espace de noms principal.
1. Créez l’espace de noms secondaire dans l’abonnement et le groupe de ressources qui contiennent l’espace de noms principal, mais dans une région différente. Cette étape est facultative. Vous pouvez créer l’espace de noms secondaire lors de la création du jumelage à l’étape suivante. 
1. Dans le portail Azure, accédez à votre espace de noms principal.
1. Sélectionnez **Géo-récupération** dans le menu de gauche, puis **Lancer le jumelage** dans la barre d’outils. 

    :::image type="content" source="./media/service-bus-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Lancer le jumelage à partir de l’espace de noms principal":::    
1. Sur la page **Lancer le jumelage**, procédez comme suit :
    1. Sélectionnez un espace de noms secondaire existant ou créez-en un dans l’abonnement et le groupe de ressources qui contiennent l’espace de noms principal. Dans cet exemple, un espace de noms existant est utilisé comme espace de noms secondaire.  
    1. Dans le champ **Alias**, entrez un alias pour le jumelage de géo-reprise d’activité après sinistre. 
    1. Sélectionnez ensuite **Create** (Créer). 

        :::image type="content" source="./media/service-bus-geo-dr/initiate-pairing-page.png" alt-text="Sélectionner l’espace de noms secondaire":::        
1. Vous devez voir la page **Alias de géo-reprise d’activité après sinistre Service Bus**, comme indiqué dans l’image suivante. Vous pouvez également accéder à la page **Alias de géo-reprise d’activité après sinistre** à partir de la page de l’espace de noms principal en sélectionnant **Géo-reprise** dans le menu de gauche. 

    :::image type="content" source="./media/service-bus-geo-dr/service-bus-geo-dr-alias-page.png" alt-text="Page Alias de géo-reprise d’activité après sinistre Service Bus":::
1. Sur la page **Alias de géo-reprise d’activité après sinistre**, sélectionnez **Stratégies d’accès partagé** dans le menu de gauche pour accéder à la chaîne de connexion principale de l’alias. Utilisez cette chaîne de connexion au lieu d’utiliser la chaîne de connexion directe à l’espace de noms principal/secondaire. Initialement, l’alias pointe vers l’espace de noms principal.
1. Basculez vers la page **Vue d’ensemble**. Vous pouvez effectuer les actions suivantes : 
    1. Arrêter le jumelage entre les espaces de noms principal et secondaire. Sélectionnez **Arrêter le jumelage** dans la barre d’outils. 
    1. Basculer manuellement sur l’espace de noms secondaire. 
        1. Sélectionnez **Basculement** dans la barre d’outils. 
        1. Confirmez que vous souhaitez basculer sur l’espace de noms secondaire en saisissant votre alias. 
        1. Activez l’option de **basculement sécurisé** pour basculer en toute sécurité sur l’espace de noms secondaire. Cette fonctionnalité permet de s’assurer que les réplications de géo-reprise d’activité après sinistre sont terminées avant de basculer sur l’espace de noms secondaire. 
        1. Sélectionnez ensuite **Basculement**. 
        
            :::image type="content" source="./media/service-bus-geo-dr/failover-page.png" alt-text="{alt-text}":::
    
            > [!IMPORTANT]
            > Le basculement active l’espace de noms secondaire et supprime l’espace de noms principal du jumelage de géo-reprise d’activité après sinistre. Créer un autre espace de noms pour avoir une nouvelle paire de géo-reprise d’activité après sinistre. 

1. Enfin, vous devez ajouter un système de surveillance afin de détecter si un basculement est nécessaire. Dans la plupart des cas, le service fait partie d’un écosystème de grande taille. C’est pourquoi des basculements automatiques sont rarement possibles, dans la mesure où, souvent, les basculements doivent être synchronisés avec le reste de l’infrastructure ou du sous-système.

### <a name="service-bus-standard-to-premium"></a>De Service Bus Standard à Premium
Si vous avez [migré votre espace de noms Azure Service Bus Standard vers Azure Service Bus Premium](service-bus-migrate-standard-premium.md), vous devez utiliser l’alias préexistant (c’est-à-dire la chaîne de connexion de l’espace de noms Service Bus Standard) pour créer la configuration de récupération d’urgence avec **PS/CLI** ou **l’API REST**.

Cela tient au fait que, lors de la migration, le nom DNS/la chaîne de connexion de l’espace de noms Azure Service Bus Standard devient un alias pour l’espace de noms Azure Service Bus Premium.

Vos applications clientes doivent utiliser cet alias (c’est-à-dire la chaîne de connexion de l’espace de noms Azure Service Bus Standard) pour se connecter à l’espace de noms Premium là où le jumelage de récupération d’urgence a été configuré.

Si vous utilisez le portail pour la configuration de la récupération d’urgence, le portail extrait cette notification pour vous.


## <a name="failover-flow"></a>Flux de basculement

Le basculement est déclenché manuellement par le client (soit explicitement à l’aide d’une commande, soit via une logique métier appartenant au client qui déclenche cette commande) et jamais par Azure. Ainsi, le client dispose d’une pleine propriété et d’une visibilité complète pour la résolution des pannes sur le segment principal d’Azure.

![4][]

Après le déclenchement du basculement :

1. La chaîne de connexion ***alias*** est mise à jour pour pointer vers l’espace de noms secondaire Premium.

2. Les clients (expéditeurs et destinataires) se connectent automatiquement à l’espace de noms secondaire.

3. L’association existante entre les espaces de noms Premium principal et secondaire est rompue.

Après le lancement du basculement :

1. Vous voulez être sûr de pouvoir refaire un basculement en cas de nouvelle panne. Pour cela, configurez un autre espace de noms passif et mettez à jour le jumelage. 

2. Extrayez des messages de l’ancien espace de noms principal dès qu’il est de nouveau disponible. Après cela, utilisez cet espace de noms pour les messages réguliers en dehors de votre configuration de géorécupération ou supprimez l’ancien espace de noms principal.

> [!NOTE]
> Seule la sémantique de transfert du basculement est prise en charge. Dans ce scénario, vous basculez puis effectuez un nouveau couplage avec un nouvel espace de noms. La restauration automatique n’est pas prise en charge ; par exemple, dans un cluster SQL. 

Vous pouvez automatiser le basculement à l’aide de systèmes de surveillance ou à l’aide de solutions de surveillance personnalisées. Toutefois, cette automatisation nécessite des tâches de planification et du travail supplémentaires, qui ne seront pas abordés dans cet article.

![2][]

## <a name="management"></a>Gestion

Si vous avez fait une erreur (par exemple, vous avez associé les mauvaises régions lors de la configuration initiale), vous pouvez rompre le couplage des deux espaces de noms à tout moment. Si vous souhaitez utiliser les espaces de noms couplés comme des espaces de noms standard, supprimez l’alias.

## <a name="use-existing-namespace-as-alias"></a>Utiliser l’espace de noms existant en tant qu’alias

Si vous avez un scénario dans lequel vous ne pouvez pas modifier les connexions des producteurs et des consommateurs, vous pouvez réutiliser le nom de votre espace de noms comme nom d’alias. Voir l’[exemple de code sur GitHub ici](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exemples

Les [exemples sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) montrent comment configurer et lancer un basculement. Ces exemples illustrent les concepts suivants :

- Un exemple .NET et les paramètres requis dans Azure Active Directory pour utiliser Azure Resource Manager avec Service Bus afin de configurer et d’activer la géo-reprise d’activité après sinistre.
- Étapes requises pour exécuter l’exemple de code.
- Comment utiliser un espace de noms existant en tant qu’alias.
- Exécuter pas à pas pour activer de façon alternative la géorécupération d’urgence via PowerShell ou CLI.
- [Envoyer et recevoir](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) à partir de l’espace de noms principal ou secondaire actuel à l’aide de l’alias.

## <a name="considerations"></a>Considérations

Notez les points suivants pour cette version :

1. Dans votre planification de basculement, vous devez également tenir compte du facteur temps. Par exemple, si vous perdez la connectivité pendant plus de 15 à 20 minutes, vous pouvez décider de lancer le basculement.

2. Le fait qu’aucune donnée ne soit répliquée signifie que les sessions actuellement actives ne sont pas répliquées. En outre, la détection des doublons et les messages planifiés peuvent ne pas fonctionner. Les nouvelles sessions, les nouveaux messages planifiés et les nouveaux doublons fonctionneront. 

3. Le basculement d’une infrastructure distribuée complexe doit être [répétée](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) au moins une fois.

4. La synchronisation des entités peut prendre un certain temps, à raison d’environ 50 à 100 entités par minute. Les abonnements et les règles comptent également comme des entités.

### <a name="availability-zones"></a>Zones de disponibilité

La référence SKU de Service Bus Premium prend en charge les [zones de disponibilité](../availability-zones/az-overview.md), en fournissant des emplacements isolés des pannes au sein d’une même région Azure. Service Bus gère trois copies de la banque de messagerie (1 copie principale et 2 secondaires). Service Bus synchronise les opérations relatives aux données et à la gestion sur les trois copies. Si la copie principale échoue, l’une des copies secondaires devient la copie principale, sans temps d’arrêt ressenti. Si les applications constatent des déconnexions temporaires de Service Bus, la logique de nouvelle tentative dans le SDK se reconnectera automatiquement à Service Bus. 

Lorsque vous utilisez des zones de disponibilité, les métadonnées et les données (messages) sont répliquées dans les centres de données de la zone de disponibilité. 

> [!NOTE]
> Dans Azure Service Bus Premium, la prise en charge des zones de disponibilité est fournie uniquement pour les [régions Azure](../availability-zones/az-region.md) qui comprennent déjà des zones de disponibilité.

Vous pouvez activer les Zones de disponibilité sur les nouveaux espaces de noms uniquement, à l’aide du portail Azure. Service Bus ne prend pas en charge la migration des espaces de noms existants. Vous ne pouvez pas désactiver la redondance de zone après l’avoir activée sur votre espace de noms.

![3][]

## <a name="private-endpoints"></a>Instances Private Endpoint
Cette section fournit des informations supplémentaires concernant l’utilisation de la géo-reprise d’activité après sinistre avec des espaces de noms qui utilisent des points de terminaison privés. Pour en savoir plus sur l’utilisation des points de terminaison privés avec Service Bus en général, consultez [Intégrer Azure Service Bus à Azure Private Link](private-link-service.md).

### <a name="new-pairings"></a>Nouveaux pairages
Si vous tentez de créer un pairage entre un espace de noms principal avec un point de terminaison privé et un espace de noms secondaire sans point de terminaison privé, le pairage échoue. Le pairage ne fonctionnera que si les espaces de noms principal et secondaire ont des points de terminaison privés. Nous vous recommandons d’utiliser les mêmes configurations sur les espaces de noms principal et secondaire, ainsi que sur les réseaux virtuels dans lesquels des points de terminaison privés sont créés. 

> [!NOTE]
> Lorsque vous tentez d’appairer l’espace de noms principal avec un point de terminaison privé et l’espace de noms secondaire, le processus de validation vérifie uniquement s’il existe un point de terminaison privé sur l’espace de noms secondaire. Il ne vérifie pas si le point de terminaison fonctionne ou fonctionnera après le basculement. Il vous incombe de vous assurer que l’espace de noms secondaire avec un point de terminaison privé fonctionnera comme prévu après le basculement.
>
> Pour vérifier que les configurations de point de terminaison privé sont identiques, envoyez une requête [Get queues](/rest/api/servicebus/stable/queues/get) à l’espace de noms secondaire depuis l’extérieur du réseau virtuel et vérifiez que vous recevez un message d’erreur de la part du service.

### <a name="existing-pairings"></a>Pairages existants
Si un pairage entre les espaces de noms principal et secondaire existe déjà, la création de point de terminaison privé sur l’espace de noms principal échoue. Pour résoudre ce problème, commencez par créer un point de terminaison privé sur l’espace de noms secondaire, puis créez-en un pour l’espace de noms principal.

> [!NOTE]
> Bien que nous autorisions un accès en lecture seule à l’espace de noms secondaire, les mises à jour des configurations de points de terminaison privés sont autorisées. 

### <a name="recommended-configuration"></a>Configuration recommandée
Lorsque vous créez une configuration de récupération d’urgence pour votre application et Service Bus, vous devez créer des points de terminaison privés pour les espaces de noms Service Bus principal et secondaire sur les réseaux virtuels hébergeant des instances principales et secondaires de votre application.

Supposons que vous disposiez de deux réseaux virtuels, VNET-1 et VNET-2, et d’espaces de noms principal et secondaire, ServiceBus-Namespace1-Primary et ServiceBus-Namespace2-Secondary. Vous devez procéder comme suit : 

- Sur ServiceBus-Namespace1-Primary, créez deux points de terminaison privés qui utilisent des sous-réseaux de VNET-1 et VNET-2.
- Sur ServiceBus-Namespace2-Secondary, créez deux points de terminaison privés qui utilisent les mêmes sous-réseaux de VNET-1 et VNET-2. 

![Points de terminaison privés et réseaux virtuels](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


L’avantage de cette approche est que le basculement peut se produire au niveau de la couche Application, indépendamment de l’espace de noms Service Bus. Examinez les scénarios suivants : 

**Basculement uniquement d’application :** Ici, l’application n’existera pas dans le VNET-1, mais passera à VNET-2. Comme les deux points de terminaison privés sont configurés sur VNET-1 et VNET-2 pour les espaces de noms principal et secondaire, l’application fonctionnera normalement. 

**Basculement de l’espace de noms Service Bus uniquement :** Ici encore, étant donné que les deux points de terminaison privés sont configurés sur les deux réseaux virtuels pour les espaces de noms principal et secondaire, l’application fonctionnera normalement. 

> [!NOTE]
> Pour obtenir des conseils sur la géo-reprise d’activité après sinistre d’un réseau virtuel, consultez [Réseau virtuel : Continuité d’activité](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les [informations de référence de l’API REST](/rest/api/servicebus/stable/disasterrecoveryconfigs) sur la géorécupération d’urgence.
- Exécutez [l’exemple de géorécupération d’urgence sur GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Consultez [l’exemple d’envoi de messages à un alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) pour la géorécupération d’urgence.

Pour en savoir plus sur la messagerie Service Bus, consultez les articles suivants :

* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
