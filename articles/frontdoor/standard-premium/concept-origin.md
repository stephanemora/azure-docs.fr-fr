---
title: Origine et groupe d’origines dans Azure Front Door Standard/Premium
description: Cet article décrit ce que sont une origine et un groupe d’origines dans une configuration Azure Front Door.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 42a9a0ea23faa481140a46ec52b2214431dd723e
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097805"
---
# <a name="origin-and-origin-group-in-azure-front-door-standardpremium-preview"></a>Origine et groupe d’origines dans Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Cet article aborde des concepts sur le fonctionnement de votre déploiement d’application web avec Azure Front Door Standard/Premium. Vous découvrirez également ce que sont une *origine* et un *groupe d’origines* dans la configuration Azure Front Door Standard/Premium.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="origin"></a>Origine

Une origine Azure Front Door Standard/Premium fait référence au nom d’hôte ou à l’IP publique de votre application qui sert les demandes de vos clients. Azure Front Door Standard/Premium prend en charge les ressources Azure et non-Azure dans un groupe d’origines. L’application peut également être hébergée dans votre centre de données local ou avec un autre fournisseur de cloud. Attention, il ne faut pas confondre l’origine avec votre niveau de base de données ou de stockage. L’origine doit être considérée comme le point de terminaison public de votre back-end d’application. Quand vous ajoutez une origine à un groupe d’origines Azure Front Door Standard/Premium, vous devez également ajouter les informations suivantes :

* **Type d’origine** : type de ressource que vous souhaitez ajouter. Front Door prend en charge la découverte automatique de vos back-ends d’application à partir d’App Service, d’un service cloud ou de Stockage. Si vous voulez une autre ressource Azure ou même un back-end non-Azure, sélectionnez **Hôte personnalisé**.

    >[!IMPORTANT]
    >Pendant la configuration, les API ne valident pas si l’origine n’est pas accessible depuis l’environnement Front Door. Assurez-vous que Front Door peut atteindre votre origine.

* **Abonnement et nom d’hôte de l’origine :** si vous n’avez pas sélectionné **Hôte personnalisé** pour le type d’hôte de votre back-end, sélectionnez votre back-end en choisissant l’abonnement approprié et le nom d’hôte de back-end correspondant.

* **En-tête de l’hôte d’origine** : valeur d’en-tête de l’hôte envoyée au back-end pour chaque demande. Pour plus d’informations, consultez [En-tête d’hôte d’origine](#hostheader).

* **Priorité :** attribuez des priorités à votre origine différente quand vous souhaitez utiliser une origine primaire pour tout le trafic. Fournissez également des sauvegardes si les origines de sauvegarde ou primaire sont indisponibles. Pour plus d'informations, veuillez consulter [Priorité](#priority).

* **Poids :** attribuez des poids à votre origine différente si vous voulez répartir le trafic entre un ensemble d’origines, que ce soit de façon équitable ou selon des coefficients de pondération. Pour plus d’informations, veuillez consulter [Poids](#weighted).

### <a name="origin-host-header"></a><a name = "hostheader"></a>En-tête de l’hôte d’origine

Les demandes qui sont transférées par Azure Front Door Standard/Premium vers une origine incluent un champ d’en-tête d’hôte utilisé par l’origine pour récupérer la ressource ciblée. La valeur de ce champ provient généralement de l’URI de l’origine qui contient l’en-tête et le port de l’hôte.

Par exemple, une demande formulée pour `www.contoso.com` aura l’en-tête d’hôte `www.contoso.com`. Si vous utilisez le portail Microsoft Azure pour configurer votre origine, la valeur par défaut pour ce champ sera le nom d’hôte du back-end. Si votre origine est `contoso-westus.azurewebsites.net`, la valeur renseignée automatiquement pour l’en-tête de l’hôte d’origine dans le portail Azure est `contoso-westus.azurewebsites.net`. Toutefois, si vous utilisez des modèles Azure Resource Manager ou une autre méthode sans définir explicitement ce champ, Front Door transmet le nom d’hôte entrant comme valeur pour l’en-tête d’hôte. Si la demande a été formulée pour `www.contoso.com` et que votre origine est `contoso-westus.azurewebsites.net` avec un champ d’en-tête vide, Front Door attribue à l’en-tête de l’hôte la valeur `www.contoso.com`.

La plupart des back-endq d’application (comme Azure Web Apps, Stockage Blob et Cloud Services) exigent une correspondance entre l’en-tête de l’hôte et le domaine du back-end. Cependant, l’hôte front-end qui route vers votre back-end aura un nom d’hôte différent, comme `www.contoso.net`.

Si votre origine nécessite une correspondance entre l’en-tête de l’hôte et le nom d’hôte du back-end, veuillez vous assurer que l’en-tête de l’hôte back-end inclut le nom de l’hôte du back-end.

#### <a name="configuring-the-origin-host-header-for-the-origin"></a>Configuration de l’en-tête de l’hôte d’origine pour l’origine

Pour configurer le champ de l’**en-tête de l’hôte d’origine** pour une origine dans la section du groupe d’origines :

1. Ouvrez votre ressource Front Door et sélectionnez le groupe d’origines contenant l’origine à configurer.

2. Si ce n’est déjà fait, ajoutez une origine, ou modifiez-en une.

3. Définissez le champ d’en-tête de l’hôte d’origine sur une valeur personnalisée ou laissez-le vide. Le nom d’hôte pour la requête entrante sera utilisé en tant que valeur d’en-tête hôte.

## <a name="origin-group"></a>Groupe d’origines

Un groupe d’origines dans Azure Front Door Standard/Premium fait référence à l’ensemble d’origines qui reçoit un trafic similaire pour leur application. En d’autres termes, il s’agit d’un regroupement logique des instances de votre application dans le monde entier, qui reçoivent le même trafic et répondent avec un comportement attendu. Ces origines peuvent être déployées dans différentes régions ou au sein d’une même région. Toutes les origines peuvent être en mode de déploiement actif/actif (ou ce qui est défini en tant que configuration active/passive).

Un groupe d’origines définit le mode d’évaluation des origines par le biais de sondes d’intégrité. Il définit également comment l’équilibrage de charge entre eux se produit.

### <a name="health-probes"></a>Sondes d’intégrité

Azure Front Door Standard/Premium envoie des requêtes de sonde HTTP/HTTPS périodiques à chacune de vos origines configurées. Les requêtes de sonde déterminent la proximité et l’intégrité de chaque origine, afin d’équilibrer la charge des demandes de vos utilisateurs finaux. Les paramètres de sonde d’intégrité d’un groupe d’origines définissent la façon dont nous sondons l’état d’intégrité des back-ends de l’application. Les paramètres de configuration de l’équilibrage de charge disponibles sont les suivants :

* **Chemin** : URL utilisée pour les requêtes de sonde pour toutes les origines du groupe d’origines. Par exemple, si l’une de vos origines est `contoso-westus.azurewebsites.net` et que le chemin défini est /probe/test.aspx, les environnements Front Door envoient les requêtes de sonde d’intégrité à `http://contoso-westus.azurewebsites.net/probe/test.aspx`, en partant du principe que le protocole est HTTP.

* **Protocole** : détermine si les requêtes de sonde d’intégrité de Front Door vers vos origines sont envoyées via le protocole HTTP ou HTTPS.

* **Méthode** : méthode HTTP à utiliser pour l’envoi des sondes d’intégrité. Les options incluent GET ou HEAD (par défaut).
    > [!NOTE]
    > Afin de limiter la charge et les coûts qui pèsent sur vos back-ends, Front Door recommande d’utiliser des requêtes HEAD pour les sondes d’intégrité.

* **Intervalle (en secondes)**  : définit la fréquence des sondes d’intégrité de vos origines, ou les intervalles dans lequel chacun des environnements Front Door envoie une sonde.

    >[!NOTE]
    >Pour accélérer les basculements, baissez la valeur de l’intervalle. Plus la valeur est faible, plus le nombre de sondes d’intégrité de vos back-ends augmente. Par exemple, si l’intervalle est défini sur 30 secondes avec 100 points de présence (POP) Front Door dans le monde entier, chaque back-end reçoit environ 200 requêtes d’intégrité par minute.

Pour plus d’informations, veuillez consulter la section [Sondes d’intégrité](concept-health-probes.md).

### <a name="load-balancing-settings"></a>Paramètres d’équilibrage de charge

Les paramètres d’équilibrage de charge du groupe d’origines définissent la façon dont nous évaluons les sondes d’intégrité. Ces paramètres déterminent si l’origine est saine ou pas. Ils vérifient également comment équilibrer la charge du trafic entre différentes origines dans le groupe d’origines. Les paramètres de configuration de l’équilibrage de charge disponibles sont les suivants :

* **Taille de l’échantillon :** cette propriété identifie le nombre d’échantillons de sondes d’intégrité à prendre en compte pour évaluer l’intégrité d’une origine.

* **Taille d’échantillon réussi :** définit la taille d’échantillon comme mentionnée ci-dessus, le nombre d’échantillons réussis nécessaires pour appeler l’origine saine. Par exemple, imaginons le cas d’un intervalle de sonde d’intégrité Front Door de 30 secondes, dont la taille d’échantillon est de 5, et la taille d’échantillon réussi de 3. Chaque fois que nous évaluons les sondes d’intégrité pour votre origine, nous examinons les 5 derniers échantillons pendant 150 secondes (5 x 30). Au moins 3 sondages réussis sont nécessaires pour déclarer que l’origine est saine.

* **Sensibilité de latence (latence supplémentaire) :** définit si vous souhaitez qu’Azure Front Door Standard/Premium envoie la requête à l’origine dans la plage de sensibilité de mesure de latence ou transfère la requête vers le back-end le plus proche.

Pour plus d’informations, consultez [Routage du trafic basé sur les latences les plus faibles](#latency).

## <a name="routing-methods"></a>Méthodes de routage

Azure Front Door Standard/Premium prend en charge différents types de méthodes de routage du trafic pour déterminer comment acheminer votre trafic HTTP/HTTPS vers différents points de terminaison de service. Lorsque les requêtes de vos clients atteignent Front Door, la méthode de routage configurée est appliquée pour veiller à ce que les requêtes soient transmises à la meilleure instance de back-end. 

Quatre méthodes de routage du trafic sont disponibles dans Azure Front Door Standard/Premium :

* **[Latence](#latency) :** Le routage basé sur la latence garantit que les requêtes sont envoyées aux backends ayant la latence la plus faible acceptables dans une plage de sensibilité. En fait, les requêtes de vos utilisateurs sont envoyées à l'ensemble de back-ends « le plus proche » en termes de latence du réseau.
* **[Priority](#priority):** Vous pouvez attribuer des priorités à vos back-ends lorsque vous souhaitez configurer un back-end principal pour traiter l'ensemble du trafic. Le back-end secondaire peut servir de sauvegarde en cas d'indisponibilité du back-end principal.
* **[Weighted](#weighted):** Vous pouvez attribuer des pondérations à vos back-ends lorsque vous souhaitez répartir le trafic sur un ensemble de back-ends, que vous souhaitiez effectuer une répartition uniforme ou en fonction de coefficients de pondération.

Toutes les configurations Azure Front Door Standard/Premium incluent la supervision de l’intégrité des back-ends et le basculement global instantané automatisé. Pour plus d’informations, consultez [Supervision des back-ends](concept-health-probes.md). Votre instance Front Door peut fonctionner à partir d'une seule méthode de routage. Mais en fonction des besoins de votre application, vous pouvez également combiner plusieurs méthodes de routage pour créer une topologie de routage optimale.

### <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Routage du trafic basé sur les latences les plus faibles

Le déploiement de back-ends à différents emplacements du globe peut améliorer la réactivité de vos applications en acheminant le trafic vers la destination « la plus proche » de vos utilisateurs finaux. La méthode de routage du trafic par défaut pour votre configuration Front Door transfère les requêtes de vos utilisateurs finaux vers le back-end le plus proche de l'environnement Front Door qui a reçu la requête. Associée à l’architecture Anycast d’Azure Front Door, cette approche garantit que chacun de vos utilisateurs finaux obtient des performances optimales personnalisées en fonction de son emplacement.

Le back-end « le plus proche » n'est pas nécessairement le plus proche en termes de distance géographique. Front Door détermine plutôt les backends les plus proches en mesurant la latence du réseau.

Vous trouverez ci-dessous le flux décisionnel global :

| Backends disponibles | Priorité | Signal de latence (en fonction de sonde d’intégrité) | Poids |
|-------------| ----------- | ----------- | ----------- |
| Tout d'abord, sélectionnez tous les back-ends qui sont signalés intègres (200 OK) par la sonde d'intégrité. S'il y a six back-ends A, B, C, D, E et F, parmi lesquels C est non intègre et E est désactivé, la liste des back-ends disponibles est A, B, D et F.  | Sont ensuite sélectionnés les back-ends disposant de la priorité la plus élevée parmi ceux qui sont disponibles. Si la priorité des back-ends A, B et D est de 1 et celle du back-end F de 2, les back-ends sélectionnés sont A, B et D.| Sélectionnez les backends ayant une plage de latence (latence minimale et sensibilité de latence spécifiées en millisecondes). Si le back-end A possède une valeur de 15 ms, le back-end B une valeur de 30 ms et le back-end D une valeur 60 ms en dehors de l'environnement Front Door où la requête est arrivée et que la sensibilité de latence est de 30 ms, le pool de latences les plus faibles comprend les back-ends A et B, D étant éloigné de 30 ms du back-end le plus proche qui est A. | Enfin, Front Door répartira le trafic par tourniquet (Round Robin) entre le pool de backends sélectionné final dans les proportions définies par les pondérations spécifiées. Par exemple, si le backend A a une pondération de 5 et que le backend B a une pondération de 8, le trafic sera distribué dans les proportions 5:8 entre les backends A et B. |

>[!NOTE]
> Par défaut, la propriété de sensibilité de latence est définie sur 0 ms, ce qui correspond au transfert systématique de la requête au backend disponible le plus rapide.

### <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Routage du trafic basé sur les priorités

Les organisations cherchent souvent à assurer la haute disponibilité de leurs services en déployant plusieurs services de sauvegarde utilisables en cas de défaillance du service principal. Dans l’ensemble du secteur, cette topologie est également appelée « topologie de déploiement Active/En veille ou Active/passive ». La méthode de routage du trafic « Priorité » permet aux clients Azure d’implémenter facilement ce modèle de basculement.

Votre porte d’entrée par défaut contient une liste de backends de priorité égale. Par défaut, Front Door envoie le trafic uniquement aux backends de priorité supérieure (valeur de priorité la plus faible) ; en d’autres termes, l’ensemble de backends principal. Si les back-ends principaux ne sont pas disponibles, Front Door achemine le trafic vers l'ensemble de back-ends secondaire (deuxième valeur de priorité la plus faible). Si les back-ends principaux et secondaires ne sont pas disponibles, le trafic est envoyé aux back-ends de troisième rang, et ainsi de suite. La disponibilité du backend est basée sur l’état configuré (activé ou désactivé) et sur l’état d’intégrité du backend en cours comme déterminé par les sondes d’intégrité.

#### <a name="configuring-priority-for-backends"></a>Configuration de la priorité des backends

Dans la configuration Front Door, chacun des back-ends de votre pool dispose d'une propriété appelée « Priorité », qui peut être une valeur comprise entre 1 et 5. Avec Azure Front Door, vous configurez la priorité des back-ends explicitement à l’aide de cette propriété pour chaque back-end. Cette propriété est une valeur comprise entre 1 et 5. Plus la valeur est basse, plus la priorité est élevée. Des backends peuvent partager des valeurs de priorité.

### <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Méthode de routage du trafic basé sur la pondération
La méthode de routage du trafic « Pondéré » vous permet de répartir le trafic uniformément ou d’utiliser une pondération prédéfinie.

Dans la méthode de routage du trafic basé sur la pondération, vous affectez une pondération à chaque backend dans la configuration Front Door de votre pool de backends. La pondération est un entier compris entre 1 et 1000. Ce paramètre utilise la pondération par défaut « 50 ».

Avec la liste des back-ends disponibles dont la sensibilité de latence est acceptable, le trafic est distribué avec un mécanisme de tourniquet (round-robin) en utilisant les proportions définies par les pondérations spécifiées. Si la sensibilité de latence est définie sur 0 milliseconde, cette propriété n'a pas d'effet, sauf si deux back-ends ont la même latence de réseau. 

La méthode pondérée permet des scénarios utiles :

* **Mise à niveau progressive d’une application** : donne un pourcentage de trafic à acheminer vers un nouveau back-end, et augmente progressivement le trafic jusqu'à ce qu'il soit au même niveau que celui des autres back-ends.
* **Migration d’application vers Azure** : créez un pool de backends avec à la fois des backends Azure et des backends externes. Ajustez la pondération des backends pour privilégier les nouveaux backends. Pour effectuer progressivement ce paramétrage, commencez par désactiver les nouveaux backends, puis affectez-leur les pondérations les plus faibles, et augmentez-les lentement à des niveaux où ils prennent le plus de trafic. Enfin, désactivez les backends les moins prisés, puis supprimez-les du pool.  
* **Extension du cloud (cloud bursting) pour une capacité supplémentaire** : étendez rapidement un déploiement local dans le cloud en le plaçant derrière Front Door. Quand vous avez besoin d’une capacité supplémentaire dans le cloud, vous pouvez ajouter ou activer des backends supplémentaires, et spécifier quelle partie du trafic est destinée à chaque backend.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [créer une instance Front Door Standard/Premium](create-front-door-portal.md).
