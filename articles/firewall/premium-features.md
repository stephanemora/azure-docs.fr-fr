---
title: Fonctionnalités du Pare-feu Azure Premium - Préversion
description: Le Pare-feu Azure Premium est un service de sécurité réseau informatique managé qui protège vos ressources de réseau virtuel Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: victorh
ms.openlocfilehash: ff5c6961e64deddc8e52dc92a7c34b5b369a44ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715562"
---
# <a name="azure-firewall-premium-preview-features"></a>Fonctionnalités du Pare-feu Azure Premium - Préversion

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="Logo de certification ICSA" border="false"::::::image type="content" source="media/premium-features/pci-logo.png" alt-text="Logo de certification PCI" border="false":::


> [!IMPORTANT]
> Pare-feu Azure Premium est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Le Pare-feu Azure Premium - Préversion est un pare-feu de nouvelle génération avec des fonctionnalités qui sont nécessaires pour les environnements hautement sensibles et réglementés.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagramme de vue d’ensemble du Pare-feu Azure Premium":::

Le Pare-feu Azure Premium utilise une stratégie de pare-feu, une ressource globale qui peut être utilisée pour gérer de manière centralisée vos pare-feu à l’aide d’Azure Firewall Manager. À partir de cette version, toutes les nouvelles fonctionnalités sont configurables via la stratégie de pare-feu uniquement. Les règles de pare-feu (classiques) continuent à être prises en charge et peuvent être utilisées pour configurer les fonctionnalités existantes du pare-feu standard.  La stratégie de pare-feu peut être gérée indépendamment ou avec Azure Firewall Manager. Une stratégie de pare-feu associée à un seul pare-feu n’est pas facturée.

> [!IMPORTANT]
> Actuellement, la référence SKU Pare-feu Premium n’est pas prise en charge dans les déploiements de hub sécurisé et les configurations de tunnel forcé. 

Le Pare-feu Azure Premium - Préversion inclut les fonctionnalités suivantes :

- **Inspection TLS** : déchiffre le trafic sortant, traite les données, puis les chiffre et les transmet à la destination.
- **IDPS** : un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer.
- **Filtrage d’URL** : étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.
- **Catégories web** : permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc.

## <a name="features"></a>Fonctionnalités

### <a name="tls-inspection"></a>Inspection TLS

Le Pare-feu Azure Premium met fin aux connexions TLS sortantes et Est-Ouest. L’inspection TLS entrante est prise en charge avec [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md), ce qui permet un chiffrement de bout en bout. Le Pare-feu Azure effectue les fonctions de sécurité à valeur ajoutée requises et chiffre à nouveau le trafic envoyé à la destination d’origine.

> [!TIP]
> Les versions 1.0 et 1.1 de TSL sont déconseillées et non prises en charge. Les versions 1.0 et 1.1 de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont déconseillées. Migrez vers TLS 1.2 dès que possible.

Pour en savoir plus sur les conditions requises pour les certificats d’autorité de certification intermédiaires du Pare-feu Azure Premium - Préversion, consultez [Certificats du Pare-feu Azure Premium - Préversion](premium-certificates.md).

### <a name="idps"></a>IDPS

Un système IDPS (Intrusion Detection and Prevention System) vous permet de surveiller les activités malveillantes, de consigner des informations sur ces activités, de les signaler, voire de les bloquer. 

Le Pare-feu Azure Premium propose un système IDPS basé sur les signatures pour permettre une détection rapide des attaques en recherchant des modèles spécifiques, tels que des séquences d’octets dans le trafic réseau ou des séquences d’instructions malveillantes connues utilisées par un programme malveillant. Les signatures IDPS sont complètement managées et mises à jour en permanence.

Le système IDPS vous permet de détecter les attaques dans tous les ports et protocoles pour le trafic non chiffré. Cela étant, lorsque le trafic HTTPS doit être inspecté, le Pare-feu Azure peut utiliser sa fonction d’inspection TLS pour déchiffrer le trafic et mieux détecter les activités malveillantes.  

La liste de contournement IDPS vous permet de ne pas filtrer le trafic vers les adresses IP, les plages et les sous-réseaux spécifiés dans cette liste.  

### <a name="url-filtering"></a>Un filtrage des URL

Le filtrage d’URL étend la fonctionnalité de filtrage de nom de domaine complet du Pare-feu Azure pour prendre en compte une URL entière. Par exemple, `www.contoso.com/a/c` plutôt que `www.contoso.com`.  

Le filtrage d’URL peut être appliqué au trafic HTTP et HTTPS. Lorsque le trafic HTTPS est inspecté, le Pare-feu Azure Premium - Préversion peut utiliser sa fonctionnalité d’inspection TLS pour déchiffrer le trafic et extraire l’URL cible afin de vérifier si l’accès est autorisé. L’inspection TLS nécessite un consentement au niveau de la règle d’application. Après activation, vous pouvez utiliser des URL pour le filtrage avec HTTPS. 

### <a name="web-categories"></a>Catégories web

Les catégories web permettent aux administrateurs d’autoriser ou de refuser aux utilisateurs l’accès aux catégories de sites web telles que les sites web de jeux d’argent, les sites web de réseaux sociaux, etc. Les catégories web sont également incluses dans le Pare-feu Azure Standard, mais elles sont plus précises dans le Pare-feu Azure Premium - Préversion. Contrairement à la fonctionnalité de catégories web de la référence SKU Standard qui correspond à la catégorie basée sur un nom de domaine complet, la référence SKU Premium correspond à la catégorie en fonction de l’URL complète pour le trafic HTTP et HTTPS. 

Par exemple, si le Pare-feu Azure intercepte une demande HTTPS pour `www.google.com/news`, la catégorisation suivante est attendue : 

- Pare-feu Standard : seule la partie du nom de domaine complet étant examinée, `www.google.com` est classé en tant que *Moteur de recherche*. 

- Pare-feu Premium : l’URL complète étant examinée, `www.google.com/news` est classée en tant qu’*Actualités*.

Les catégories sont organisées en fonction de leur gravité sous **Responsabilité**, **Bande passante élevée**, **Utilisation métier**, **Perte de productivité**, **Navigation générale** et **Sans catégorie**.

#### <a name="category-exceptions"></a>Exceptions de catégorie

Vous pouvez créer des exceptions à vos règles de catégorie web. Créez une collection de règles d’autorisation ou de refus distinct avec une priorité plus élevée au sein du groupe de collections de règles. Par exemple, vous pouvez configurer une collection de règles qui autorise `www.linkedin.com` avec la priorité 100, avec une collection de règles qui refuse **Réseaux sociaux** avec la priorité 200. Cette opération crée l’exception pour la catégorie web **Réseaux sociaux** prédéfinie.

#### <a name="categorization-change"></a>Modification de la catégorisation

Vous pouvez demander une modification de catégorisation si vous  :

 - pensez qu’un nom de domaine complet (FQDN) ou une URL doit être dans une catégorie différente 
 
or 

- avez une catégorie suggérée pour un FQDN ou une URL sans catégorie

Vous êtes invité à envoyer une demande à l’adresse [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 

## <a name="known-issues"></a>Problèmes connus

Les problèmes connus du Pare-feu Azure Premium - Préversion sont les suivants :

|Problème  |Description  |Limitation des risques  |
|---------|---------|---------|
|Inspection TLS prise en charge uniquement sur le port HTTPS standard|L’inspection TLS prend uniquement en charge HTTPS/443. |Aucun. D’autres ports seront pris en charge dans le cadre de la mise à la disponibilité générale.|
|Prise en charge ESNI pour la résolution de nom de domaine complet dans HTTPS|Le chiffrement SNI n’est pas pris en charge dans l’établissement d'une liaison HTTPS.|Aujourd’hui, seul Firefox prend en charge ESNI via une configuration personnalisée. La solution de contournement recommandée consiste à désactiver cette fonctionnalité.|
|Certificats clients (TLS)|Les certificats clients sont utilisés pour créer une approbation d’identité mutuelle entre le client et le serveur. Les certificats clients sont utilisés lors d’une négociation TLS. Le Pare-feu Azure renégocie une connexion avec le serveur et n’a pas accès à la clé privée des certificats clients.|Aucun|
|QUIC/HTTP3|QUIC est la nouvelle version majeure de HTTP. Il s’agit d’un protocole basé sur UDP sur 80 (PLAN) et 443 (SSL). L’inspection FQDN/URL/TLS n’est pas prise en charge.|Configurez le passage UDP 80/443 en tant que règles de réseau.|
|Hub sécurisé et tunneling forcé non pris en charge dans Premium|Actuellement, la référence SKU du Pare-feu Premium n’est pas prise en charge dans les déploiements de hub sécurisé et les configurations de tunnel forcé.|Correctif planifié lors de la disponibilité générale.|
Certificats signés par le client non approuvés|Les certificats signés par le client ne sont pas approuvés par le pare-feu lorsqu’ils proviennent d’un serveur web intranet.|Correctif planifié lors de la disponibilité générale.
|Adresses IP source et de destination erronées dans les alertes pour IDPS avec inspection TLS.|Lorsque vous activez l’inspection TLS et que le système IDPS émet une nouvelle alerte, l’adresse IP source/de destination affichée est incorrecte (l’adresse IP interne est affichée à la place de l’adresse IP d’origine).|Correctif planifié lors de la disponibilité générale.|
|Adresse IP source erronée dans les alertes avec système IDPS pour HTTP (sans inspection TLS).|Lorsque le trafic HTTP en texte brut est utilisé, que le système IDPS émet une nouvelle alerte et que la destination est publique, l’adresse IP source affichée est incorrecte (l’adresse IP interne est affichée à la place de l’adresse IP d’origine).|Correctif planifié lors de la disponibilité générale.|
|Propagation du certificat|Après l’application d’un certificat d’autorité de certification sur le pare-feu, la prise en compte du certificat peut prendre de 5 à 10 minutes.|Correctif planifié lors de la disponibilité générale.|
|Contournement IDPS|Le contournement IDPS ne fonctionne pas pour le trafic avec terminaison TLS. L’adresse IP source et les groupes IP sources ne sont pas pris en charge.|Correctif planifié lors de la disponibilité générale.|
|Prise en charge du protocole TLS 1.3|Le protocole TLS 1.3 est partiellement pris en charge. Le tunnel TLS entre le client et le pare-feu est basé sur le protocole TLS 1.2, et celui entre le pare-feu et le serveur web externe est basé sur le protocole TLS 1.3.|Des mises à jour sont à l’étude.|




## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les certificats du Pare-feu Azure Premium](premium-certificates.md)
- [Déployer et configurer le Pare-feu Azure Premium - Préversion](premium-deploy.md)
- [Migrer vers le Pare-feu Azure Premium - Préversion](premium-migrate.md)
