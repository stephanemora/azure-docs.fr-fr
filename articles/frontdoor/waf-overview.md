---
title: Quel est le pare-feu d’applications web Azure pour Azure porte d’entrée ? (Préversion)
description: Découvrez comment Azure pare-feu d’applications web pour le service de la porte d’entrée Azure protège vos applications web contre les attaques malveillantes.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 17cf6629aca6c73bc96e4cf0c172a2e87a7aafb8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910074"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door-preview"></a>Quel est le pare-feu d’applications web Azure pour Azure porte d’entrée ? (Préversion)

Pare-feu d’applications Azure web (WAF) (version préliminaire) offre une protection centralisée pour vos applications web qui sont fournies dans le monde entier à l’aide de la porte d’entrée Azure. Il est conçu et géré de défendre vos services web contre les vulnérabilités et exploits courants et de conserver votre service à haute disponibilité pour vos utilisateurs en plus de vous aider à répondre aux exigences de conformité.

> [!IMPORTANT]
> Le pare-feu d’applications Azure web (WAF) pour Azure porte d’entrée est actuellement en version préliminaire publique.
> Cette version préliminaire est fournie sans contrat de niveau de Service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Applications Web sont plus en plus la cible d’attaques malveillantes, telles que les refus de service, attaques par injection SQL et les saturations des attaques de script entre sites. Ces attaques peuvent entraîner une perte de données et de panne du service, représentent une menace significative aux propriétaires d’applications web.

Empêcher ces attaques dans le code d’application peut se révéler difficile et nécessiter une maintenance rigoureuse, des mises à jour correctives ainsi que la surveillance au niveau de plusieurs couches de la topologie de l’application. Un pare-feu d’applications web centralisé facilite grandement la gestion de la sécurité et offre une meilleure garantie de protection aux administrateurs de l’application contre les menaces ou les intrusions. En outre, une solution WAF peut réagir à une menace de sécurité plus rapidement par la mise à jour corrective d’une vulnérabilité connue dans un emplacement central, au lieu de sécuriser chacune des applications web individuelles.

WAF pour porte d’entrée est une solution globale et centralisée. Il est déployé sur les emplacements de périphérie de réseau Azure dans le monde entier et que chaque requête entrante pour une application web de WAF activé remise par la porte d’entrée est inspecté à la périphérie du réseau. Cela permet de WAF empêcher les attaques malveillantes proches les sources d’attaque, avant leur entrée sur votre réseau virtuel et offre une protection globale à l’échelle sans sacrifier les performances. Une stratégie de pare-feu d’applications Web peut être facilement liée à n’importe quel profil porte d’entrée dans votre abonnement et de nouvelles règles peuvent être déployés en quelques minutes, ce qui vous permet de répondre rapidement à l’évolution des modèles de menace.

![Pare-feu d’applications web Azure](./media/waf-overview/web-application-firewall-overview.png)

WAF Azure peut également être activée avec Application Gateway. Pour plus d’informations, consultez [pare-feu d’applications Web](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>Stratégies de pare-feu d’applications Web et des règles

Vous pouvez configurer une stratégie de pare-feu d’applications Web et associer cette stratégie à un ou plusieurs porte frontaux pour la protection. Une stratégie de pare-feu d’applications Web se compose de deux types de règles de sécurité :
- règles personnalisées qui ont été créés par le client.
- les ensembles de règles gérées qui sont une collection d’Azure managed préconfiguré ensemble de règles. Lorsque les deux sont présentes, les règles personnalisées sont exécutées avant l’exécution des règles dans un ensemble de règles managé. Une règle est constituée d’une condition de correspondance, une priorité et une action. Types d’actions prises en charge sont : Autoriser, bloquer, de journal et de redirection. Vous pouvez créer une stratégie entièrement personnalisée qui répond aux exigences de protection de votre application en combinant les règles personnalisées et non managées.

Règles au sein d’une stratégie sont exécutées dans un ordre de priorité dans lequel la priorité est un entier unique qui définit l’ordre d’exécution de la règle. Valeur entière inférieure indique une priorité plus élevée et ceux sont évaluées avant les règles ayant une valeur entière supérieure. Une fois une règle est mis en correspondance, l’action correspondante qui a été définie dans la règle est appliquée à la demande. Une fois qu’une correspondance est traitée, les règles avec une priorité plus faible ne sont pas traitées davantage.

Une application web fournie par la porte d’entrée peut avoir qu’une seule stratégie de pare-feu d’applications Web associée à la fois. Toutefois, vous pouvez avoir une configuration de la porte d’entrée sans les stratégies de pare-feu d’applications Web associées à elle. Si une stratégie de pare-feu d’applications Web est présente, elle est répliquée sur tous nos emplacements edge pour assurer la cohérence dans les stratégies de sécurité dans le monde entier.

## <a name="waf-modes"></a>Modes WAF

Stratégie de pare-feu d’applications Web peut être configurée pour s’exécuter dans les deux modes suivants :

- **Mode de détection :** Lorsque vous exécutez en mode de détection, WAF ne prend pas d’autres actions autres que des analyses et enregistre la demande et sa mise en correspondance règle WAF pour les journaux WAF. Vous pouvez activer la journalisation des diagnostics pour porte d’entrée (à l’aide du portail, cela peut être obtenue en accédant à la **Diagnostics** section dans le portail Azure).

- **Mode de prévention :** Lorsque configuré pour s’exécuter en mode de prévention, effectuées par WAF l’action spécifiée si une demande correspond à une règle et si une correspondance est trouvée, aucune autre règle avec une priorité inférieure n’est évaluées. Toutes les demandes de mise en correspondance sont également enregistrés dans les journaux WAF.

## <a name="waf-actions"></a>Actions de pare-feu d’applications Web

Les clients de pare-feu d’applications Web peuvent choisir d’exécuter à partir d’une des actions lorsqu’une requête correspond aux conditions d’une règle :
- **Autoriser :**  Demande passe par le pare-feu d’applications Web et est transférée vers le pool principal. Aucune des règles de priorité inférieure peuvent bloquer cette demande.
- **Bloc :** La demande est bloquée et WAF envoie une réponse au client sans la transférer la demande vers le back-end.
- **rapport :**  Demande est enregistrée dans les journaux WAF et WAF continue de l’évaluation des règles de priorité inférieure.
- **Redirection :** WAF redirige la demande vers l’URI spécifié. L’URI spécifié est un paramètre de niveau de stratégie. Une fois configuré, toutes les demandes qui correspondent à la **rediriger** action sera envoyée à cet URI.


## <a name="waf-rules"></a>Règles de pare-feu d’applications Web

Une stratégie de pare-feu d’applications Web peut être constitué de deux types de règles de sécurité - règles personnalisées, créés par le client et les ensembles de règles managé, gérés par Azure préconfigurés l’ensemble de règles.

### <a name="custom-authored-rules"></a>Créés par des règles personnalisées
Vous pouvez configurer des règles personnalisées WAF comme suit :

- **Liste de blocage et de liste verte d’IP :** Vous pouvez configurer des règles personnalisées pour contrôler l’accès à vos applications web basées sur une liste d’adresses IP client ou les plages d’adresses IP. Types d’adresses IPv4 et IPv6 sont prises en charge. Cette liste peut être configurée pour bloquer ou autoriser les demandes dont l’adresse IP source correspond à une adresse IP dans la liste.

- **Contrôle d’accès basé sur le géographique :** Vous pouvez configurer des règles personnalisées pour contrôler l’accès à vos applications web basées sur le code de pays associé à une adresse IP du client.

- **Contrôle d’accès basé sur les paramètres HTTP :** Vous pouvez configurer des règles personnalisées en fonction de chaîne correspondant aux paramètres de demande HTTP/HTTPS, chaînes de requête POST args, URI de requête, en-tête de demande et corps de la demande.

- **À la demande de contrôle d’accès basé sur une méthode :** Vous pouvez configurer des règles personnalisées en fonction de la méthode de demande HTTP de la demande, telles que GET, PUT ou HEAD.

- **Contrainte de taille :** Vous pouvez configurer des règles personnalisées reposant sur les longueurs des parties spécifiques d’une requête comme chaîne de requête Uri, ou le corps de la demande.

- **Taux de limitation de règles :** Une règle de contrôle du débit consiste à limiter un trafic élevé anormal en provenance de toute adresse IP de client. Vous pouvez configurer un seuil sur le nombre de demandes web autorisée à partir d’une adresse IP du client pendant une durée d’une minute. Cela est différent d’une règle de personnalisé basé sur la liste Autoriser ou bloquer des IP qui soit permet tout ou blocs de tous les demandes à partir d’une adresse IP du client. Limitation du débit peut être combinée avec des conditions de correspondance supplémentaires telles que les paramètres HTTP (S) correspondant pour le contrôle de la fréquence granulaire.


### <a name="azure-managed-rule-sets"></a>Ensembles de règles gérés par Azure

Ensembles de règles gérés par Azure fournissent un moyen simple de déployer la protection contre un ensemble commun de menaces de sécurité. Dans la mesure où ces ensembles de règles sont gérées par Azure, les règles sont mises à jour en fonction des besoins pour vous protéger contre les nouvelles signatures d’attaque. En préversion publique, le gérés par Azure ensemble par défaut règle inclut des règles sur les catégories de menaces suivantes :

- Les scripts intersites
- Attaques Java
- Inclusion d’un fichier local
- Attaques par injection de PHP
- Exécution de la commande à distance
- Inclusion de fichier distant
- Fixation de session
- Protection contre les injections de code SQL

Incrémente le numéro de version de l’ensemble de règles par défaut lorsque de nouvelles signatures d’attaque sont ajoutés à l’ensemble de règles.
Ensemble de règles par défaut est activée par défaut en mode de détection dans vos stratégies de pare-feu d’applications Web. Vous pouvez activer ou désactiver des règles individuelles au sein de l’ensemble par défaut règle afin de répondre aux exigences de votre application. Vous pouvez également définir des actions spécifiques (autoriser/bloquer/redirection/LOG) par règle. Action par défaut consiste à bloquer. En outre, les règles personnalisées peuvent être configurées dans la même stratégie de pare-feu d’applications Web si vous souhaitez contourner les règles préconfigurées dans l’ensemble de règles par défaut.
Règles personnalisées sont toujours appliqués avant l’évaluation des règles dans l’ensemble de règles par défaut. Si une demande correspond à une règle personnalisée, action de règle correspondante est appliquée, et la demande est bloquée ou passée au back-end, sans appel de toutes les règles personnalisées supplémentaires ou les règles dans l’ensemble de règles par défaut. En outre, vous avez la possibilité pour supprimer l’ensemble de la règle par défaut à partir de vos stratégies de pare-feu d’applications Web.


## <a name="configuration"></a>Configuration
Pendant la préversion publique :
- Configuration et déploiement de tous les types de règles WAF sont entièrement prise en charge les API REST, les modèles Azure Resource Manager et Azure PowerShell.
- À l’aide du portail Azure, vous pouvez configurer ou afficher uniquement les gérés par Azure par défaut règle définie.

## <a name="monitoring"></a>Surveillance

Surveillance de WAF à porte est intégré avec Azure Monitor pour effectuer le suivi des alertes et de surveiller facilement les tendances du trafic.

## <a name="pricing"></a>Tarifs

La version préliminaire publique, toute utilisation associée WAF pour porte d’entrée est gratuite et ne sera pas débitée.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).

