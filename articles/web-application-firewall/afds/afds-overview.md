---
title: Qu’est-ce que le pare-feu d’applications web Azure pour Azure Front Door ?
description: Découvrez de quelle manière le pare-feu d’applications web Azure sur le service Azure Front Door protège vos applications web contre les attaques malveillantes.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 11/23/2019
ms.author: victorh
ms.openlocfilehash: b646035f6a952f679059abab86d94179f447f9ff
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406197"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Pare-feu d’applications web Azure sur Azure Front Door

Le pare-feu d’applications web (WAF) Azure sur Azure Front Door offre une protection centralisée à vos applications web distribuées dans le monde entier à l’aide d’Azure Front Door. Il est conçu pour défendre vos services web contre les vulnérabilités et attaques courantes, ainsi que pour assurer le haut niveau de disponibilité de votre service pour vos utilisateurs en plus de vous aider à répondre à vos obligations de conformité.

La solution WAF sur Front Door est mondiale et centralisée. Elle est déployée aux emplacements de périphérie du réseau Azure dans le monde entier. Chaque requête entrante pour une application web avec WAF activé qui est fournie par Front Door est inspectée à la périphérie du réseau. La solution WAF peut ainsi contrer les attaques malveillantes au plus près des sources d’attaque, avant même que les attaques pénètrent votre réseau virtuel, et offrir une protection globale à grande échelle sans sacrifier les performances. Vous pouvez facilement lier une stratégie WAF à n’importe quel profil Front Door dans votre abonnement et déployer de nouvelles règles en quelques minutes afin de répondre rapidement aux changements incessants des modèles de menaces.

![Pare-feu d’applications web (WAF) d’Azure](../media/overview/wafoverview.png)

## <a name="waf-policy-and-rules"></a>Stratégie et règles WAF

Vous pouvez configurer une stratégie WAF et l’associer à un ou plusieurs front-ends Front Door pour la protection. Une stratégie WAF se compose de deux types de règles de sécurité :

- des règles personnalisées créées par le client ;

- des ensembles de règles managées qui rassemblent les règles préconfigurées managées par Azure.

Quand ces deux types de règles sont utilisés conjointement, les règles personnalisées sont appliquées avant celles d’un ensemble de règles managées. Une règle est constituée d’une condition de correspondance, d’une priorité et d’une action. Les types d’actions pris en charge sont : AUTORISER, BLOQUER, JOURNALISER et REDIRIGER. Vous pouvez créer une stratégie entièrement personnalisée qui répond aux exigences de protection spécifiques de votre application en combinant des règles personnalisées et des règles managées.

Les règles dans une stratégie sont traitées par ordre de priorité, où la priorité est représentée par un entier unique qui définit l’ordre de traitement des règles. Plus la valeur entière est petite, plus la priorité est élevée. Les règles ayant des valeurs inférieures sont évaluées avant les règles avec des valeurs plus élevées. Une fois qu'une correspondance de règle est trouvée, l’action associée définie dans la règle est appliquée à la requête. Après le traitement de cette correspondance, aucune autre règle avec une priorité inférieure n’est traitée.

Une application web fournie par Front Door peut être associée à une seule stratégie WAF. Toutefois, il n’est pas obligatoire d’associer une stratégie WAF dans une configuration Front Door. Si une stratégie WAF est utilisée, elle est répliquée sur tous nos emplacements de périphérie pour assurer la cohérence dans toutes les stratégies de sécurité dans le monde.

## <a name="waf-modes"></a>Modes WAF

Une stratégie WAF peut être configurée pour s’exécuter dans les deux modes suivants :

- **Mode Détection :** lors d’une exécution en mode Détection, le pare-feu d’applications web n’entreprend aucune autre action à part la supervision et l’enregistrement de la requête, et la mise en correspondance de la règle WAF dans les journaux WAF. Vous pouvez activer les diagnostics de journalisation pour Front Door (si vous utilisez le portail, accédez à la section **Diagnostics** dans le portail Azure).

- **Mode Prévention :** quand il est configuré pour s’exécuter en mode Prévention, le pare-feu d’applications web entreprend l’action spécifiée si une requête correspond à une règle et, si une correspondance est trouvée, aucune autre règle avec une priorité inférieure n’est évaluée. Toutes les requêtes mises en correspondance sont également enregistrées dans les journaux WAF.

## <a name="waf-actions"></a>Actions WAF

Les clients de la solution WAF peuvent choisir d’exécuter l’une des actions disponibles quand une requête correspond aux conditions d’une règle :

- **Autoriser :**  la requête est transférée au back-end par le biais du pare-feu d'applications web. Aucune autre règle de priorité inférieure ne peut bloquer cette requête.
- **Bloquer :** la requête est bloquée et le pare-feu d'applications web envoie une réponse au client sans transférer la requête au back-end.
- **Journaliser :**  la requête est enregistrée dans les journaux WAF, puis le pare-feu d'applications web continue l’évaluation des règles de priorité inférieure.
- **Rediriger :** le pare-feu d'applications web redirige la requête vers l’URI spécifié. L’URI spécifié est un paramètre défini au niveau de la stratégie. Une fois l’URI configuré, toutes les requêtes associées à l’action **Rediriger** sont envoyées à cet URI.

## <a name="waf-rules"></a>Règles WAF

Une stratégie WAF peut comporter deux types de règles de sécurité : des règles personnalisées, créées par le client, et des ensembles de règles managées, qui rassemblent les règles préconfigurées managées par Azure.

### <a name="custom-authored-rules"></a>Règles WAF personnalisées

Vous pouvez configurer des règles WAF personnalisées comme expliqué ci-dessous :

- **Listes d’adresses IP autorisées et bloquées :** vous pouvez configurer des règles personnalisées pour contrôler l’accès à vos applications web en fonction d’une liste d’adresses IP clientes ou de plages d’adresses IP. Les deux types d’adresses IPv4 et IPv6 sont pris en charge. Cette liste peut être configurée pour bloquer ou autoriser les requêtes dont l’adresse IP source correspond à l’une des adresses IP listées.

- **Contrôle d’accès en fonction de l’emplacement géographique :** vous pouvez configurer des règles personnalisées pour contrôler l’accès à vos applications web en fonction du code pays qui est associé à l’adresse IP d’un client.

- **Contrôle d’accès en fonction des paramètres HTTP :** vous pouvez configurer des règles personnalisées basées sur la correspondance des chaînes dans les paramètres de requête HTTP/HTTPS, notamment des chaînes de requête, des arguments POST, de l’URI de requête, de l’en-tête de requête et du corps de requête.

- **Contrôle d’accès en fonction de la méthode de requête :** vous pouvez configurer des règles personnalisées en fonction de la méthode de requête HTTP utilisée, telle que GET, PUT ou HEAD.

- **Contrainte de taille :** vous pouvez configurer des règles personnalisées basées sur la longueur des différentes parties d’une requête, comme la chaîne, l’URI ou le corps de la requête.

- **Règles de limitation du débit :** Une règle de contrôle du débit consiste à limiter un trafic élevé anormal en provenance de toute adresse IP de client. Vous pouvez configurer un seuil sur le nombre de requêtes web autorisées à partir d’une adresse IP cliente pendant une durée d’une minute. Ce type de règle est différent d’une règle personnalisée basée sur une liste d’adresses autorisées ou bloquées qui autorise ou bloque toutes les requêtes provenant d’une adresse IP cliente. Pour contrôler le débit encore plus précisément, vous pouvez combiner des règles de limitation du débit avec des conditions de correspondance supplémentaires, telles que la mise en correspondance des paramètres HTTP/HTTPS.

### <a name="azure-managed-rule-sets"></a>Ensembles de règles managées par Azure

Les ensembles de règles managées par Azure fournissent un moyen simple de déployer une solution de protection contre diverses menaces de sécurité courantes. Dans la mesure où ces ensembles de règles sont managées par Azure, les règles sont mises à jour comme il convient pour vous protéger contre les nouvelles signatures d’attaque. Disponible en préversion publique, l’ensemble de règles par défaut managées par Azure inclut des règles de protection contre les catégories de menaces suivantes :

- Scripts intersites (XSS)
- Attaques Java
- Inclusion de fichier local (LFI)
- Injection de code PHP
- Exécution de commande à distance
- Inclusion de fichier distant (RFI)
- Fixation de session
- Protection contre les injections de code SQL
- Attaquants de protocole

Le numéro de version de l’ensemble de règles par défaut s’incrémente à chaque ajout d’une nouvelle signature d’attaque à l’ensemble de règles.
L’ensemble de règles par défaut est activé en mode Détection par défaut dans vos stratégies WAF. Vous pouvez activer ou désactiver des règles individuellement dans l’ensemble de règles par défaut en fonction des exigences propres à votre application. Vous pouvez également définir des actions spécifiques (AUTORISER/BLOQUER/REDIRIGER/JOURNALISER) pour chaque règle. L’action par défaut est BLOQUER. De plus, les règles personnalisées peuvent être configurées dans la même stratégie WAF si vous souhaitez ignorer les règles préconfigurées de l’ensemble de règles par défaut.
Les règles personnalisées sont toujours appliquées avant l’évaluation des règles de l’ensemble de règles par défaut. Si une requête correspond à une règle personnalisée, l’action de règle correspondante est appliquée, et la requête est soit bloquée, soit transmise au back-end, sans appliquer aucune autre règle personnalisée ni règle de l’ensemble de règles par défaut. Par ailleurs, vous avez la possibilité de supprimer l’ensemble de règles par défaut de vos stratégies WAF.


### <a name="bot-protection-rule-set-preview"></a>Ensemble de règles de protection des bots (préversion)

Un ensemble de règles de protection des bots managées peut être activé pour votre pare-feu WAF afin d’effectuer des actions personnalisées sur les requêtes provenant de catégories de bots connues. Trois catégories de bots sont prises en charge : Bad Bots (Mauvais bots), Good Bots (Bons bots) et Unknown Bots (Bots inconnus). Les signatures de bot sont gérées et mises à jour dynamiquement par la plateforme WAF. Les adresses IP malveillantes pour les Bad Bots (Mauvais bots) proviennent du flux Microsoft Threat Intelligence. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) alimente Microsoft Threat Intelligence et est utilisé par de nombreux services, notamment Azure Security Center. Les Good Bots (Bons bots), incluent les moteurs de recherche validés. Les catégories inconnues incluent des groupes de bots supplémentaires. Vous pouvez définir des actions personnalisées pour bloquer, autoriser, journaliser ou rediriger pour les différentes catégories de bots.

![Ensemble de règles de protection bot](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> L’ensemble de règles de protection des bots, actuellement disponible en préversion publique, est fourni avec un contrat de niveau de service en préversion. Certaines fonctionnalités peuvent être limitées ou non prises en charge.  Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si la protection des bots est activée, les demandes entrantes qui correspondent à des règles de bot sont journalisées dans le journal FrontdoorWebApplicationFirewallLog. Vous pouvez accéder aux journaux WAF à partir d’un compte de stockage, d’un hub d’événements ou de Log Analytics.

## <a name="configuration"></a>Configuration

La configuration et le déploiement de tous les types de règles WAF sont entièrement pris en charge si vous utilisez le portail Azure, des API REST, des modèles Azure Resource Manager ou Azure PowerShell.

## <a name="monitoring"></a>Surveillance

La supervision de WAF pour Front Door est intégrée à Azure Monitor, ce qui vous permet de suivre les alertes et d’analyser facilement les tendances du trafic.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Pare-feu d’applications web sur Azure Application Gateway](../ag/ag-overview.md)
