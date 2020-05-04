---
title: Meilleures pratiques
description: Découvrez les meilleures pratiques et des conseils utiles pour le développement de votre solution Azure Batch.
ms.date: 04/03/2020
ms.topic: article
ms.openlocfilehash: 43a0020953ea44593cf38298a78547194751fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117503"
---
# <a name="azure-batch-best-practices"></a>Meilleures pratiques relatives à Azure Batch

Cet article présente une collection de meilleures pratiques pour utiliser le service Azure Batch de manière efficace. Ces bonnes pratiques sont issues de notre expérience d’Azure Batch, mais également de celle qu’en ont les clients. Il est important de comprendre cet article pour éviter les pièges de conception, les problèmes de performances potentiels et les anti-modèles lors du développement et de l’utilisation de Batch.

Cet article porte sur les points suivants :

> [!div class="checklist"]
> - Quelles sont les meilleures pratiques
> - Pourquoi vous devez utiliser les meilleures pratiques
> - Ce qui peut se produire si vous ne respectez pas les meilleures pratiques
> - Comment respecter les meilleures pratiques

## <a name="pools"></a>Pools

Les pools Batch sont les ressources de calcul pour l’exécution de travaux sur le service Batch. Les sections suivantes fournissent des conseils sur les meilleures pratiques à respecter lors de l’utilisation de pools Batch.

### <a name="pool-configuration-and-naming"></a>Configuration et dénomination de pools

- **Mode d’allocation de pool** Lorsque vous créez un compte Batch, vous pouvez choisir entre deux modes d’allocation de pool : **service Batch** ou **abonnement utilisateur**. Dans la plupart des cas, vous devez utiliser le mode de service Batch par défaut. Les pools sont alloués en arrière-plan dans des abonnements managés par Azure Batch. Dans l’autre mode d’abonnement utilisateur, les machines virtuelles Batch et les autres ressources sont créées directement dans l’abonnement lors de la création d’un pool. Les comptes d’abonnement utilisateur sont principalement utilisés pour permettre un petit sous-ensemble important de scénarios. Pour plus d’informations sur le mode d’abonnement utilisateur, consultez [Configuration supplémentaire pour le mode d’abonnement utilisateur](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode).

- **Tenez compte du temps d’exécution des travaux et des tâches lors de la détermination du mappage du travail au pool.**
    Si vous avez des travaux constitués principalement de tâches de courte durée et que le nombre total de tâches attendu est faible, de sorte que le temps d’exécution global prévu du travail n’est pas long, n’allouez pas de nouveau pool pour chaque travail. Le temps de répartition des nœuds diminuera le temps d’exécution du travail.

- **Les pools doivent avoir plusieurs nœuds de calcul.**
    Il n’est pas garanti que les nœuds individuels soient toujours disponibles. Bien que rares, les défaillances matérielles, les mises à jour du système d’exploitation et une foule d’autres problèmes peuvent entraîner la déconnexion de nœuds individuels. Si votre charge de travail Batch requiert une progression déterministe et garantie, vous devez allouer des pools avec plusieurs nœuds.

- **Ne réutilisez pas les noms de ressources.**
    Les ressources Batch (travaux, pools, etc.) vont et viennent souvent au fil du temps. Par exemple, vous pouvez créer un pool le lundi, le supprimer le mardi, puis créer un autre pool le jeudi. Chaque nouvelle ressource que vous créez doit avoir un nom unique que vous n’avez pas utilisé auparavant. Pour ce faire, vous pouvez utiliser un GUID (comme nom complet de la ressource ou comme partie de celui-ci) ou incorporer l’heure de création de la ressource dans son nom. Batch prend en charge [DisplayName](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.jobspecification.displayname?view=azure-dotnet), qui peut être utilisé pour donner un nom lisible par l’utilisateur à une ressource, même si l’ID réel de la ressource est un nom qui n’est pas convivial. L’utilisation de noms uniques facilite la différenciation des ressources particulières dans les journaux et les métriques. Cela élimine également toute ambiguïté si vous devez créer une demande de support pour une ressource.

- **Continuité pendant la maintenance et la défaillance des pools.**
    Il est préférable que vos travaux utilisent des pools de manière dynamique. Si vos travaux utilisent le même pool pour tout, il y a une chance que vos travaux ne soient pas exécutés en cas de problème avec le pool. Cela est particulièrement important pour les charges de travail urgentes. Pour remédier à ce problème, sélectionnez ou créez un pool de manière dynamique lorsque vous planifiez chaque travail, ou disposez d’un moyen de substituer le nom du pool pour pouvoir ignorer un pool non sain.

- **Continuité de l’activité pendant la maintenance et la défaillance des pools** Il existe de nombreuses causes possibles qui peuvent empêcher un pool de croître jusqu’à la taille requise, par exemple, des erreurs internes, des contraintes de capacité, etc. Pour cette raison, vous devez être prêt à recibler des travaux dans un autre pool (éventuellement avec une taille de machine virtuelle différente. Batch le prend en charge via [UpdateJob](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.protocol.joboperationsextensions.update?view=azure-dotnet)) le cas échéant. Évitez d’utiliser un ID de pool statique en pensant qu’il ne sera jamais supprimé et jamais modifié.

### <a name="pool-lifetime-and-billing"></a>Durée de vie et facturation d’un pool

La durée de vie d’un pool peut varier en fonction de la méthode de répartition et des options appliquées à la configuration du pool. À tout moment, les pools peuvent avoir une durée de vie arbitraire et un nombre variable de nœuds de calcul dans le pool. Il vous incombe de gérer les nœuds de calcul dans le pool, soit explicitement, soit par le biais de fonctionnalités fournies par le service (mise à l’échelle automatique ou pool automatique).

- **Maintenez les pools à jour.**
    Vous devez redimensionner vos pools à zéro tous les quelques mois pour vous assurer d’obtenir les dernières mises à jour de l’agent de nœud et les correctifs de bogues les plus récents. Votre pool ne reçoit pas les mises à jour de l’agent de nœud à moins qu’il ne soit recréé ou redimensionné à 0 nœud de calcul. Avant de recréer ou de redimensionner votre pool, il est recommandé de télécharger tous les journaux de l’agent de nœud à des fins de débogage, comme indiqué dans la section [Nœuds](#nodes).

- **Recréation de pool** Dans le même ordre d’idées, il n’est pas recommandé de supprimer et recréer vos pools quotidiennement. Au lieu de cela, créez un nouveau pool, mettez à jour vos travaux existants pour qu’ils pointent vers le nouveau pool. Une fois que toutes les tâches ont été déplacées vers le nouveau pool, supprimez l’ancien pool.

- **Efficacité du pool et facturation** Batch n’entraîne pas en soi de frais supplémentaires, mais vous êtes facturé pour les ressources de calcul utilisées. Vous êtes facturé pour chaque nœud de calcul dans le pool, quel que soit l’état dans lequel il se trouve. Cela comprend les frais requis pour le fonctionnement du nœud, tels que les coûts de stockage et de mise en réseau. Pour en savoir plus sur les meilleures pratiques, consultez [Analyse des coûts et budgets pour Azure Batch](budget.md).

### <a name="pool-allocation-failures"></a>Échecs de répartition de pool

Les échecs de répartition de pool peuvent se produire à tout moment pendant la première répartition ou lors de redimensionnements ultérieurs. Cela peut être dû à un épuisement temporaire de la capacité dans une région ou à des défaillances dans d’autres services Azure sur lesquels Batch s’appuie. Votre quota de base n’est pas une garantie, mais plutôt une limite.

### <a name="unplanned-downtime"></a>Temps d’arrêt non planifié

Il est possible que les pools Batch rencontrent des événements de temps d’arrêt dans Azure. Il est important de garder cela à l’esprit lors de la planification et du développement de votre scénario ou de votre flux de travail pour Batch.

En cas de défaillance d’un nœud, Batch tente automatiquement de récupérer ces nœuds de calcul en votre nom. Cela peut déclencher la replanification d’une tâche en cours d’exécution sur le nœud qui est récupéré. Consultez [Conception des nouvelles tentatives](#designing-for-retries-and-re-execution) pour en savoir plus sur les tâches interrompues.

- **Dépendance de région Azure** Il est recommandé de ne pas dépendre d’une seule région Azure si vous avez une charge de travail urgente ou de production. Bien que rare, il existe des problèmes qui peuvent perturber une région entière. Par exemple, si votre traitement doit démarrer à un moment donné, envisagez d’augmenter l’échelle du pool dans votre région primaire *bien avant votre heure de début*. Si cette mise à l’échelle du pool échoue, vous pouvez vous replier sur l’augmentation de l’échelle d’un pool dans une ou plusieurs régions de sauvegarde. Les pools sur plusieurs comptes et dans différentes régions fournissent une sauvegarde prête et facilement accessible en cas de problème avec un autre pool. Pour plus d’informations, consultez [Conception de votre application pour une haute disponibilité](high-availability-disaster-recovery.md).

## <a name="jobs"></a>travaux

Un travail est un conteneur conçu pour contenir des centaines, des milliers, voire des millions de tâches.

- **Placer de nombreuses tâches dans un travail** L’utilisation d’un travail pour exécuter une seule tâche est inefficace. Par exemple, il est plus efficace d’utiliser un seul travail contenant 1 000 tâches plutôt que de créer 100 travaux qui contiennent 10 tâches chacun. L’exécution de 1 000 travaux, chacun avec une seule tâche, serait l’approche la moins efficace, la plus lente et la plus coûteuse à prendre.

    Ne concevez pas de solution Batch qui nécessite des milliers de travaux actifs simultanément. Étant donné qu’il n’existe aucun quota pour les tâches, l’exécution d’autant de tâches que possible sous le moins de travaux possible utilise efficacement vos [quotas de travail et de planification de travail](batch-quota-limit.md#resource-quotas).

- **Durée de vie du travail** Un travail Batch a une durée de vie illimitée jusqu’à sa suppression du système. L’état d’un travail indique s’il peut ou non accepter des tâches supplémentaires pour la planification. Un travail ne passe pas automatiquement à l’état terminé, sauf s’il a été arrêté explicitement. Cela peut être déclenché automatiquement via la propriété [onAllTasksComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.onalltaskscomplete?view=azure-dotnet) ou [maxWallClockTime](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints).

Il existe un [quota de travail actif et de planification de travail](batch-quota-limit.md#resource-quotas) par défaut. Les travaux et les planifications de travail à l’état terminé ne sont pas comptabilisés dans ce quota.

## <a name="tasks"></a>Tâches

Les tâches sont des unités fonctionnelles individuelles qui composent un travail. Les tâches sont soumises par l’utilisateur et planifiées par Batch sur les nœuds de calcul. Il y a plusieurs considérations de conception à prendre en compte lors de la création et de l’exécution des tâches. Les sections suivantes décrivent des scénarios courants et expliquent comment concevoir vos tâches pour les exécuter efficacement et gérer les problèmes.

- **Enregistrez les données de tâche dans le cadre de la tâche.**
    Les nœuds de calcul sont éphémères par nature. Il existe de nombreuses fonctionnalités dans Batch, telles que le pool automatique et la mise à l’échelle automatique, qui facilitent la disparition des nœuds. Lorsque les nœuds quittent le pool (en raison d’un redimensionnement ou d’une suppression de pool), tous les fichiers sur ces nœuds sont également supprimés. Pour cette raison, il est recommandé qu’avant la fin d’une tâche, celle-ci déplace sa sortie hors du nœud sur lequel elle s’exécute et vers un stockage durable. De même, en cas d’échec d’une tâche, elle doit déplacer les journaux requis pour diagnostiquer l’échec dans un stockage durable. Batch prend en charge Stockage Azure afin de charger des données via [OutputFiles](batch-task-output-files.md), ainsi qu’un large éventail de systèmes de fichiers partagés, ou vous pouvez effectuer le chargement vous-même dans vos tâches.

### <a name="task-lifetime"></a>Durée de vie de la tâche

- **Supprimez les tâches lorsqu’elles sont terminées.**
    Supprimez les tâches lorsqu’elles ne sont plus nécessaires, ou définissez une contrainte de tâche [retentionTime](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.retentiontime?view=azure-dotnet). Si une `retentionTime` est définie, Batch nettoie automatiquement l’espace disque utilisé par la tâche lorsque `retentionTime` expire.

    La suppression de tâches permet deux choses. Elle garantit que vous n’avez pas une accumulation de tâches dans le travail, ce qui rend l’interrogation ou la recherche de la tâche qui vous intéresse plus difficile (car vous devrez filtrer les tâches terminées). Elle nettoie également les données de tâche correspondantes sur le nœud (à condition que la contrainte `retentionTime` n’ait pas déjà été atteinte). Ceci garantit que vos nœuds ne se remplissent pas de données de tâches et ne manquent pas d’espace disque.

### <a name="task-submission"></a>Envoi de tâche

- **Envoyez un grand nombre de tâches dans une collection.**
    Les tâches peuvent être envoyées individuellement ou dans des collections. Soumettez des tâches dans des [collections](https://docs.microsoft.com/rest/api/batchservice/task/addcollection) jusqu’à 100 à la fois lorsque vous envoyez des tâches en masse afin de réduire le temps de traitement et d’envoi.

### <a name="task-execution"></a>Exécution des tâches

- **Choix de votre nombre maximal de tâches par nœud** Batch prend en charge la surconsommation de tâches sur les nœuds (exécution plus de tâches qu’un nœud n’a de cœurs). C’est à vous de veiller à ce que vos tâches « s’adaptent » aux nœuds de votre pool. Par exemple, vous pouvez avoir une dégradation de l’expérience si vous tentez de planifier huit tâches qui consomment chacune 25 % de l’utilisation de l’UC sur un nœud (dans un pool avec `maxTasksPerNode = 8`).

### <a name="designing-for-retries-and-re-execution"></a>Conception pour les nouvelles tentatives et la réexécution

Batch peut retenter automatiquement les tâches. Il existe deux types de nouvelles tentatives : contrôlée par l’utilisateur et interne. Les nouvelles tentatives contrôlées par l’utilisateur sont spécifiées par le paramètre [maxTaskRetryCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskconstraints.maxtaskretrycount?view=azure-dotnet) de la tâche. Quand un programme spécifié dans la tâche quitte avec un code de sortie différent de zéro, la tâche est retentée jusqu’à la valeur de `maxTaskRetryCount`.

Bien que cela soit rare, une tâche peut être retentée en interne en raison de défaillances sur le nœud de calcul, comme l’impossibilité de mettre à jour l’état interne ou une défaillance sur le nœud pendant l’exécution de la tâche. La tâche sera retentée sur le même nœud de calcul, si possible, jusqu’à une limite interne avant d’être abandonnée et différée. Elle sera alors replanifiée par Batch, éventuellement sur un nœud de calcul différent.

- **Créer des tâches durables** Les tâches doivent être conçues pour résister aux défaillances et prendre en charge les nouvelles tentatives. Ce paramètre est particulièrement important pour les tâches durables. Pour ce faire, assurez-vous que les tâches génèrent le même résultat, même si elles sont exécutées plusieurs fois. L’une des façons d’y parvenir consiste à faire de vos tâches une « recherche d’objectif ». Une autre méthode est de vous assurer que vos tâches sont idempotentes (les tâches auront le même résultat, peu importe combien de fois elles seront exécutées).

    Un exemple courant est une tâche de copie de fichiers vers un nœud de calcul. Une approche simple est une tâche qui copie tous les fichiers spécifiés à chaque fois qu’elle s’exécute, ce qui est inefficace et n’est pas conçu pour résister à une défaillance. Au lieu de cela, créez une tâche pour vous assurer que les fichiers se trouvent sur le nœud de calcul, c’est-à-dire une tâche qui ne recopie pas les fichiers déjà présents. De cette façon, la tâche reprends là où elle s’est arrêtée si elle a été interrompue.

- **Nœuds basse priorité** Il n’existe aucune différence de conception lors de l’exécution de vos tâches sur des nœuds dédiés ou de basse priorité. Qu’une tâche soit anticipée lorsqu’elle est exécutée sur un nœud de basse priorité ou interrompue en raison d’une défaillance sur un nœud dédié, les deux situations sont atténuées en concevant la tâche dans le but de résister à une défaillance.

- **Durée d’exécution des tâches** Évitez les tâches avec une durée d’exécution courte. Les tâches qui ne durent qu’une à deux secondes ne sont pas idéales. Vous devez essayer d’effectuer une grande quantité de travail dans une tâche individuelle (10 secondes minimum, allant jusqu’à des heures ou des jours). Si chaque tâche s’exécute pendant une minute (ou plus), la surcharge de planification en tant que fraction du temps de calcul global est faible.

## <a name="nodes"></a>Nœuds

- **Les tâches de démarrage doivent être idempotentes** Comme les autres tâches, la tâche de démarrage de nœud doit être idempotente, car elle sera réexécutée à chaque démarrage du nœud. Une tâche idempotente est simplement une tâche qui produit un résultat constant lorsqu’elle est exécutée plusieurs fois.

- **Gérez les services durables par le biais de l’interface des services du système d’exploitation.**
    Il est parfois nécessaire d’exécuter un autre agent parallèlement à l’agent Batch dans le nœud, par exemple, pour collecter des données à partir du nœud et les signaler. Nous vous recommandons de déployer ces agents en tant que services de système d’exploitation, tels qu’un service Windows ou un service `systemd` Linux.

    Lors de l’exécution de ces services, ceux-ci ne doivent pas verrouiller les fichiers des répertoires managés par Batch sur le nœud, sinon Batch ne pourra pas supprimer ces répertoires en raison du verrouillage des fichiers. Par exemple, si vous installez un service Windows dans une tâche de démarrage, au lieu de lancer le service directement à partir du répertoire de travail de la tâche de démarrage, copiez les fichiers ailleurs (si les fichiers existent, ignorez simplement la copie). Installez le service à partir de l’emplacement. Lorsque Batch exécute à nouveau votre tâche de démarrage, il supprime le répertoire de travail de la tâche de démarrage et le recrée. Cela fonctionne parce que le service a des verrouillages de fichier sur l’autre répertoire, et non sur le répertoire de travail de la tâche de démarrage.

- **Éviter de créer des jonctions de répertoires dans Windows** Les jonctions de répertoires, parfois appelées liens physiques de répertoires, sont difficiles à gérer lors du nettoyage des tâches et des travaux. Utilisez des symlinks (liens symboliques) plutôt que des liens physiques.

- **Collecter les journaux de l’agent Batch en cas de problème** Si vous remarquez un problème impliquant le comportement d’un nœud ou de tâches en cours d’exécution sur un nœud, il est recommandé de collecter les journaux de l’agent Batch avant de libérer les nœuds en question. Les journaux de l’agent Batch peuvent être collectés à l’aide de l’API de chargement des journaux du service Batch. Ces journaux peuvent être fournis dans le cadre d’un ticket de support envoyé à Microsoft. Ils vous aideront à détecter les problèmes et à les résoudre.

## <a name="security"></a>Sécurité

### <a name="security-isolation"></a>Isolation à des fins de sécurité

À des fins d’isolation, si votre scénario nécessite l’isolation des travaux les uns des autres, vous devez isoler ces derniers en les plaçant dans des pools distincts. Un pool est la limite d’isolation de sécurité dans Batch et, par défaut, deux pools ne sont pas visibles l’un par l’autre ni en mesure de communiquer entre eux. Évitez d’utiliser des comptes Batch distincts comme méthode d’isolation.

## <a name="moving"></a>Déplacement

### <a name="move-batch-account-across-regions"></a>Déplacer un compte Batch entre régions

Il existe différents scénarios dans lesquels vous pouvez être amené à déplacer votre compte Batch existant d’une région à une autre. Par exemple, vous pouvez le déplacer vers une autre région dans le cadre de la planification d’une reprise d’activité après sinistre.

Vous ne pouvez pas déplacer des comptes Azure Batch d’une région à l’autre. Toutefois, vous pouvez utiliser un modèle Azure Resource Manager pour exporter la configuration existante de votre compte Batch.  Vous pouvez ensuite déplacer la ressource dans une autre région en exportant le compte Batch vers un modèle, en modifiant les paramètres pour qu’ils correspondent à la région de destination, puis en déployant le modèle dans la nouvelle région. Après avoir chargé le modèle dans la nouvelle région, vous devrez recréer les certificats, les planifications des travaux et les packages d’application. Pour valider les changements et terminer le déplacement du compte Batch, n’oubliez pas de supprimer le groupe de ressources ou le compte Batch d’origine.

Pour plus d’informations sur Resource Manager et les modèles, consultez [Démarrage rapide : Créer et déployer des modèles Azure Resource Manager à l’aide du portail Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

## <a name="connectivity-to-the-batch-service"></a>Connectivité au service Batch

### <a name="network-security-groups-nsgs-and-user-defined-routes-udrs"></a>Groupes de sécurité réseau (NSG) et Itinéraires définis par l’utilisateur (UDR)

Lorsque vous approvisionnez des [pools Batch dans un réseau virtuel](batch-virtual-network.md), veillez à respecter scrupuleusement les instructions relatives à l’utilisation de la balise de service `BatchNodeManagement`, des ports, des protocoles et de la direction de la règle.
L’utilisation de la balise du service est fortement recommandée, contrairement à celle des adresses IP du service Batch sous-jacent, qui peuvent changer au fil du temps. L’utilisation directe des adresses IP du service Batch peut occasionner une instabilité, des interruptions ou des pannes de pools Batch lorsque le service Batch met à jour les adresses IP utilisées au fil le temps. Si vous utilisez actuellement des adresses IP du service Batch dans vos règles NSG, il est recommandé de passer à l’utilisation de la balise de service.

Pour les itinéraires définis par l’utilisateur, assurez-vous d’avoir mis en place un processus qui met régulièrement à jour les adresses IP du service Batch dans votre table d’itinéraires à mesure que celles-ci évoluent au fil du temps. Pour savoir comment obtenir la liste des adresses IP du service Batch, consultez [Balises de service locales](../virtual-network/service-tags-overview.md). Les adresses IP du service Batch sont associées à la balise de service `BatchNodeManagement` (ou à la variante régionale correspondant à la région de votre compte Batch).

### <a name="honoring-dns"></a>Respect du DNS

Assurez-vous que vos systèmes respectent la durée de vie (TTL) du DNS pour l’URL de service de votre compte Batch. Par ailleurs, assurez-vous que les clients de votre service Batch et d’autres mécanismes de connectivité au service Batch ne reposent pas sur des adresses IP.

Si vos demandes reçoivent des réponses HTTP de niveau 5xx et que celles-ci contiennent un en-tête « Connection: close », le client de votre service Batch doit respecter la recommandation en fermant la connexion existante, en résolvant le DNS pour l’URL de service du compte Batch, et en essayant les demandes suivantes sur une nouvelle connexion.

### <a name="retrying-requests-automatically"></a>Nouvelles tentatives automatiques des demandes

Assurez-vous que les clients de votre service Batch disposent de stratégies de nouvelle tentative appropriées pour réessayer automatiquement vos demandes, même pendant un fonctionnement normal, et non exclusivement pendant les périodes de maintenance du service. Ces stratégies de nouvelle tentative doivent couvrir un intervalle d’au moins 5 minutes. Les fonctionnalités de nouvelles tentatives automatiques sont fournies avec divers kits de développement logiciel Batch, tels que la [classe .NET RetryPolicyProvider](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.retrypolicyprovider?view=azure-dotnet).

