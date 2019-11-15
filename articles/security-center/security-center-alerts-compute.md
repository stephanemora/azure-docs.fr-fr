---
title: Détection des menaces pour le calcul natif cloud dans Azure Security Center | Microsoft Docs
description: Cet article présente les alertes de calcul natif cloud disponibles dans Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2019
ms.author: memildin
ms.openlocfilehash: 6b6acb0ae1452795fe02906779b920e4b41f9a55
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748397"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Détection des menaces pour le calcul natif cloud dans Azure Security Center

La solution native Azure Security Center offre une visibilité unique sur les journaux internes qui facilite l’identification de la méthodologie d’attaque sur différentes cibles. Cet article présente les alertes disponibles pour les services Azure suivants :

* [Azure App Service](#app-services)
* [Conteneurs Azure](#azure-containers) 

> [!NOTE]
> Ces services ne sont pas disponibles actuellement dans les régions Azure Government et de cloud souverain.

## Azure App Service <a name="app-services"></a>

Security Center utilise l’échelle du cloud pour identifier les attaques ciblant les applications exécutées sur App Service. Les attaquants sondent les applications web pour trouver et exploiter les faiblesses. Avant d’être routées vers des environnements spécifiques, les demandes adressées aux applications s’exécutant dans Azure passent par plusieurs passerelles, où elles sont inspectées et journalisées. Ces données sont ensuite utilisées pour identifier les codes malveillants exploitant une faille de sécurité ainsi que les attaquants, et pour découvrir de nouveaux modèles qui seront utilisés ultérieurement.

En utilisant la visibilité dont Azure bénéficie en tant que fournisseur de cloud, Security Center analyse les journaux internes d’App Service pour identifier la méthodologie d’attaque sur plusieurs cibles. Les attaques distribuées et les attaques par analyse généralisée sont des exemples de méthodologie. En général, ce type d’attaque provient d’un petit sous-ensemble d’adresses IP, et présente des modèles de progression vers des points de terminaison similaires sur plusieurs hôtes. Les attaques recherchent une page ou un plug-in vulnérable et ne peuvent pas être identifiées du point de vue d’un seul hôte.

Security Center a également accès aux machines virtuelles et aux bacs à sable sous-jacents. Couplée à la forensique de la mémoire, l’infrastructure peut raconter l’histoire, depuis la circulation d’une nouvelle attaque dans la nature à la compromission des machines des clients. Par conséquent, même si Security Center est déployé après qu’une application web a été attaquée, il est capable de détecter les attaques en cours.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Détection d’un appel de thème WordPress suspect**|Le journal d’activité d’App Service indique une activité d’injection de code possible sur votre ressource App Service.<br/> Cette activité suspecte est similaire à une activité caractérisée par la manipulation d’un thème WordPress pour prendre en charge l’exécution de code côté serveur, suivie d’une requête web directe pour appeler le fichier de thème manipulé. Ce type d’activité peut s’inscrire dans le cadre d’une campagne d’attaque visant WordPress.|
|**Détection d’une connexion à une page web à partir d’une adresse IP anormale**|Le journal d’activité d’App Service indique une connexion à une page web sensible à partir d’une adresse source qui ne s’y était jamais connectée. Cette connexion peut indiquer que quelqu’un tente une attaque par force brute dans les pages d’administration de votre application web. Elle peut également être le résultat de l’utilisation d’une nouvelle adresse IP par un utilisateur légitime.|
|**Une adresse IP qui s’est connectée à votre interface FTP Azure App Service a été trouvée dans Threat Intelligence**|L’analyse des journaux FTP d’App Service a détecté l’établissement d’une connexion à partir d’une adresse source qui a été trouvée dans le flux de Threat Intelligence. Durant cette connexion, un utilisateur a accédé aux pages listées.|
|**Détection d’une prise d’empreinte web**|Le journal d’activité d’App Service indique une activité de prise d’empreinte web possible sur votre ressource App Service. <br/>Cette activité suspecte est associée à un outil appelé Blind Elephant. L’outil prend l’empreinte des serveurs web et tente de détecter les applications installées et leurs versions. Les attaquants utilisent souvent cet outil pour sonder les applications web afin de rechercher les vulnérabilités.|
|**Détection d’un accès suspect à une page web potentiellement vulnérable**|Le journal d’activité d’App Service indique un accès à une page web qui semble sensible. <br/>Cette activité suspecte provient d’une adresse source dont le modèle d’accès est semblable à celui d’un analyseur web. Ce type d’activité est souvent associé à une tentative par un attaquant d’analyser votre réseau pour essayer d’accéder à des pages web sensibles ou vulnérables.|
|**Fichier PHP dans le dossier de chargement**|Le journal d’activité d’App Service indique que quelque chose a accédé à une page PHP suspecte située dans le dossier de chargement. <br/>Généralement, ce type de dossier ne contient pas de fichiers PHP. L’existence de ce type de fichier peut indiquer une exploitation tirant parti des vulnérabilités du chargement de fichiers arbitraires.|
|**Tentative d’exécution de commandes Linux sur un service d’application Windows**|L’analyse des processus App Service a détecté une tentative d’exécution d’une commande Linux sur un service d’application Windows. Cette action s’exécutait aux côtés de l’application web. Ce comportement est souvent observé pendant les campagnes qui exploitent une vulnérabilité dans une application web courante.|
|**Détection de l’exécution d’un processus PHP suspect**|Les journaux des machines indiquent qu’un processus PHP suspect est en cours d’exécution. L’action incluait une tentative d’exécution de commandes de système d’exploitation ou de code PHP à partir de la ligne de commande, en utilisant le processus PHP. Bien que ce comportement puisse être légitime, dans les applications web, il peut indiquer des activités malveillantes, telles que des tentatives d’infecter des sites web avec des interpréteurs de commandes web.|
|**Exécution de processus à partir du dossier temporaire**|L’analyse des processus App Service a détecté l’exécution d’un processus à partir du dossier temporaire de l’application. Bien que ce comportement puisse être légitime, dans les applications web, il peut indiquer des activités malveillantes.|
|**Détection d’une tentative d’exécution d’une commande qui requiert des privilèges élevés**|L’analyse des processus App Service a détecté une tentative d’exécution d’une commande qui demande des privilèges élevés. La commande s’est exécutée dans le contexte de l’application web. Bien que ce comportement puisse être légitime, dans les applications web, il peut indiquer des activités malveillantes.|

## Conteneurs Azure <a name="azure-containers"></a>

Security Center assure une détection des menaces en temps réel pour vos environnements conteneurisés et génère des alertes en cas d’activités suspectes. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

Nous détectons les menaces à différents niveaux : 

* **Au niveau de l’hôte** : l’agent de Security Center (disponible avec le niveau tarifaire Standard ; consultez les [tarifs](security-center-pricing.md) pour plus d’informations) supervise Linux pour détecter les activités suspectes. L’agent déclenche des alertes s’il détecte des activités suspectes réalisées à partir du nœud ou d’un conteneur qui y est exécuté. Parmi ces activités, citons la détection de shell web et la connexion avec des adresses IP suspectes connues.

    Pour fournir des insights plus complets sur la sécurité de votre environnement conteneurisé, l’agent supervise l’analytique des conteneurs. Il déclenche des alertes sur des événements tels que la création de conteneurs privilégiés, l’accès suspect à des serveurs d’API et à des serveurs Secure Shell (SSH) s’exécutant dans un conteneur Docker.

    >[!NOTE]
    > Si vous choisissez de ne pas installer les agents sur vos machines hôtes, vous ne recevrez qu’une partie des avantages et alertes de détection des menaces. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.

* **Au niveau du cluster AKS**, une supervision de la détection des menaces est effectuée sur la base de l’analyse des journaux d’audit Kubernetes. Pour activer cette supervision **sans agent**, ajoutez l’option Kubernetes à votre abonnement à partir de la page **Tarification et paramètres** (voir les [tarifs](security-center-pricing.md)). Pour générer des alertes à ce niveau, Security Center supervise vos services managés par AKS à l’aide des journaux collectés par AKS. Les tableaux de bord Kubernetes exposés, la création de rôles à privilèges élevés et la création de montages sensibles sont des exemples d’événements à ce niveau.

    >[!NOTE]
    > Security Center génère des alertes de détection pour les déploiements et les actions d’Azure Kubernetes Service qui se produisent après l’activation de l’option Kubernetes dans les paramètres de l’abonnement. 

De plus, notre équipe mondiale d’experts en sécurité surveille en permanence l’évolution des menaces. Ils ajoutent des alertes et des vulnérabilités propres aux conteneurs au fur et à mesure de leur découverte.


### <a name="aks-cluster-level-alerts"></a>Alertes au niveau du cluster AKS

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**PRÉVERSION - Liaison de rôle au rôle d’administrateur de cluster détecté**|L’analyse du journal d’audit Kubernetes a détecté une nouvelle liaison au rôle d’administrateur de cluster aboutissant à des privilèges d’administrateur. Le fait de fournir inutilement des privilèges d’administrateur peut entraîner des problèmes d’escalade de privilèges dans le cluster.|
|**PRÉVERSION - Tableau de bord Kubernetes exposé détecté**|L’analyse du journal d’audit Kubernetes a détecté une exposition du tableau de bord Kubernetes par un service LoadBalancer. Les tableaux de bord exposés permettent un accès non authentifié à la gestion des clusters et constituent une menace pour la sécurité.|
|**PRÉVERSION - Nouveau rôle avec privilèges élevés détecté**|L’analyse du journal d’audit Kubernetes a détecté un nouveau rôle avec des privilèges élevés. Une liaison à un rôle avec des privilèges élevés donne à l’utilisateur/au groupe des privilèges élevés dans le cluster. Le fait de fournir inutilement des privilèges élevés peut entraîner des problèmes d’escalade de privilèges dans le cluster.|
|**PRÉVERSION - Nouveau conteneur détecté dans l’espace de noms kube-system**|L’analyse du journal d’audit Kubernetes a détecté un nouveau conteneur dans l’espace de noms kube-system qui ne fait pas partie des conteneurs qui s’exécutent normalement dans cet espace de noms. Les espaces de noms kube-system ne doivent pas contenir de ressources utilisateur. Les attaquants peuvent utiliser cet espace de noms pour masquer des composants malveillants.|
|**PRÉVERSION - Conteneur d’extraction de données monétaires numériques détecté**|L’analyse du journal d’audit Kubernetes a détecté un conteneur qui a une image associée à un outil d’extraction de monnaies numériques.|
|**PRÉVERSION - Conteneur privilégié détecté**|L’analyse du journal d’audit Kubernetes a détecté un nouveau conteneur privilégié. Un conteneur privilégié a accès aux ressources du nœud et rompt l’isolation entre les conteneurs. En cas de compromission, un attaquant peut utiliser le conteneur privilégié pour accéder au nœud.|
|**PRÉVERSION - Conteneur avec un montage de volume sensible détecté**|L’analyse du journal d’audit Kubernetes a détecté un nouveau conteneur avec un montage de volume sensible. Le volume détecté est un type hostPath qui monte un fichier ou un dossier sensible depuis le nœud sur le conteneur. Si le conteneur est compromis, l’attaquant peut utiliser ce montage pour accéder au nœud.|



### <a name="host-level-alerts"></a>Alertes au niveau de l’hôte

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Conteneur privilégié détecté**|Les journaux de la machine indiquent qu’un conteneur Docker privilégié est en cours d’exécution. Un conteneur privilégié a un accès complet aux ressources de l’hôte. En cas de compromission, un attaquant peut utiliser le conteneur privilégié pour accéder à l’ordinateur hôte.|
|**Exécution d’une commande privilégiée dans le conteneur**|Les journaux de la machine indiquent qu’une commande privilégiée a été exécutée dans un conteneur Docker. Une commande privilégiée a des privilèges étendus sur la machine hôte.|
|**Démon Docker exposé détecté**|Les journaux de la machine indiquent que le démon Docker (dockerd) expose un socket TCP. Par défaut, la configuration Docker n’utilise pas le chiffrement ou l’authentification quand un socket TCP est activé. Toute personne ayant accès au port approprié peut alors obtenir un accès complet au démon Docker.|
|**Le serveur SSH s’exécute à l’intérieur d’un conteneur**|Les journaux de la machine indiquent qu’un serveur SSH est en cours d’exécution dans un conteneur Docker. Bien que ce comportement puisse être intentionnel, il indique fréquemment qu’un conteneur est mal configuré ou a subi une violation.|
|**Conteneur avec une image d’extracteur détecté**|Les journaux de la machine indiquent l’exécution d’un conteneur Docker exécutant une image associée à l’extraction de monnaies numériques. Ce comportement peut éventuellement indiquer que vos ressources font l’objet d’un abus.|
|**Demande suspecte à l’API Kubernetes**|Les journaux de la machine indiquent qu’une demande suspecte a été adressée à l’API Kubernetes. La demande a été envoyée à partir d’un nœud Kubernetes, éventuellement à partir de l’un des conteneurs en cours d’exécution dans le nœud. Bien que ce comportement puisse être intentionnel, il peut indiquer que le nœud exécute un conteneur compromis.|
|**Demande suspecte au tableau de bord Kubernetes**|Les journaux de la machine indiquent qu’une demande suspecte a été adressée au tableau de bord Kubernetes. La demande a été envoyée à partir d’un nœud Kubernetes, éventuellement à partir de l’un des conteneurs en cours d’exécution dans le nœud. Bien que ce comportement puisse être intentionnel, il peut indiquer que le nœud exécute un conteneur compromis.|