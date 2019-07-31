---
title: Détection des menaces pour le calcul natif cloud dans Azure Security Center | Microsoft Docs
description: Cette rubrique présente les alertes du calcul natif cloud disponibles dans Azure Security Center. dans Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 14433806a28e31cef1a278e16cb69e7c9b1a2458
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295839"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Détection des menaces pour le calcul natif cloud dans Azure Security Center

En tant que fournisseur de cloud, Security Center s’appuie sur la visibilité unique dont il dispose pour analyser les journaux internes et identifier la méthodologie d’attaque sur plusieurs cibles. Cette rubrique présente les alertes disponibles pour les services Azure suivants :

* [Azure App Service](#app-services)
* [Containers](#azure-containers) 

## Azure App Service <a name="app-services"></a>

Security Center s’appuie sur l’échelle du cloud pour identifier les attaques ciblant les applications des clients en cours d’exécution sur Azure App Service. Les applications web faisant partie de pratiquement tout réseau moderne, les attaquants sondent leur présence et exploitent leurs faiblesses. Avant d’être routées vers des environnements spécifiques, les demandes adressées aux applications s’exécutant dans Azure passent par plusieurs passerelles où elles sont inspectées et journalisées. Ces données sont ensuite utilisées pour identifier les codes malveillants exploitant une faille de sécurité, les attaquants et pour découvrir de nouveaux modèles qui seront utilisés ultérieurement.

En tirant parti de la visibilité dont Azure dispose en tant que fournisseur de cloud, Security Center analyse les journaux internes d’App Service pour identifier la méthodologie d’attaque sur plusieurs cibles. C’est, par exemple, le cas des attaques distribuées et des attaques par analyse généralisée. En général, ce type d’attaque provient d’un petit sous-ensemble d’adresses IP et présente des modèles de progression vers des points de terminaison similaires sur plusieurs hôtes, à la recherche d’une page ou d’un plug-in vulnérable. Il peut être détecté à l’aide du cloud, mais ne peut pas être identifié du point de vue d’un seul hôte.

Security Center a également accès aux machines virtuelles et aux bacs à sable sous-jacents. Couplée à la forensique de la mémoire, l’infrastructure peut raconter l’histoire, depuis la circulation d’une nouvelle attaque dans la nature à la compromission des machines des clients. Ainsi, Security Center peut détecter les attaques contre les applications web longtemps après que celles-ci ont été exploitées.

> [!div class="mx-tableFixed"]

|Alerte|Description|
|---|---|
|**Détection d’un appel de thème WordPress suspect**|Le journal d’activité Azure App Service indique une activité d’injection de code possible sur votre ressource App Service.<br/> Cette activité suspecte est similaire à une activité caractérisée par la manipulation d’un thème WordPress pour prendre en charge l’exécution de code côté serveur, suivie d’une demande web directe pour appeler le fichier de thème manipulé. Ce type d’activité a été observé par le passé dans le cadre d’une campagne d’attaque visant WordPress.|
|**Détection d’une connexion à une page web à partir d’une adresse IP anormale**|Le journal d’activité Azure App Service indique une connexion à une page web sensible à partir d’une adresse source qui ne s’y était jamais connectée. Cette situation peut indiquer que quelqu’un tente une attaque par force brute dans les pages d’administration de votre application web. Elle peut également être le résultat de l’utilisation d’une nouvelle adresse IP par un utilisateur légitime.|
|**Une adresse IP qui s’est connectée à votre interface FTP Azure App Service a été trouvée dans Threat Intelligence**|L’analyse des journaux FTP Azure App Service a détecté une connexion à partir d’une adresse source qui a été trouvée dans le flux de Threat Intelligence. Au cours de cette connexion, un utilisateur a accédé aux pages listées au-dessous.|
|**Détection d’une prise d’empreinte web**|Le journal d’activité Azure App Service indique une activité de prise d’empreinte web possible sur votre ressource App Service. <br/>Cette activité suspecte détectée est associée à un outil appelé Blind Elephant. L’outil prend l’empreinte des serveurs web et tente de détecter les applications installées et leurs versions. Les attaquants utilisent souvent cet outil pour sonder les applications web afin de rechercher les vulnérabilités.|
|**Détection d’un accès suspect à une page web potentiellement vulnérable**|Le journal d’activité Azure App Service indique un accès à une page web qui semble sensible. <br/>Cette activité suspecte provient d’une adresse source dont le modèle d’accès est semblable à celui d’un analyseur web. Ce type d’activité est souvent associé à une tentative par un attaquant d’analyser votre réseau pour essayer d’accéder à des pages web sensibles ou vulnérables.|
|**Fichier PHP dans le dossier de chargement**|Le journal d’activité Azure App Service indique que quelque chose a accédé à une page PHP suspecte située dans le dossier de chargement. <br/>Généralement, ce type de dossier ne contient pas de fichiers PHP. L’existence de ce type de fichier peut indiquer une exploitation tirant parti des vulnérabilités du chargement de fichiers arbitraires.|
|**Tentative d’exécution de commandes Linux sur un service d’application Windows**|L’analyse des processus App Service a détecté une tentative d’exécution d’une commande Linux sur un service d’application Windows. Cette action s’exécutait aux côtés de l’application web. Ce comportement est souvent observé pendant les campagnes qui exploitent une vulnérabilité dans une application web courante.|
|**Détection de l’exécution d’un processus PHP suspect**|Les journaux des machines indiquent qu’un processus PHP suspect est en cours d’exécution. L’action incluait une tentative d’exécution de commandes de système d’exploitation ou de code PHP à partir de la ligne de commande en utilisant le processus PHP. Bien que ce comportement puisse être légitime, il est également constaté dans les applications web au travers d’activités malveillantes telles que les tentatives d’infecter des sites web avec des interpréteurs de commandes web.|
|**Exécution de processus à partir du dossier temporaire**|L’analyse des processus App Service a détecté l’exécution d’un processus à partir du dossier temporaire de l’application. Bien que ce comportement puisse être légitime, il est également constaté dans les applications web au travers d’activités malveillantes.|
|**Détection d’une tentative d’exécution d’une commande qui requiert des privilèges élevés**|L’analyse des processus App Service a détecté une tentative d’exécution d’une commande qui demande des privilèges élevés. La commande s’est exécutée dans le contexte de l’application web. Bien que ce comportement puisse être légitime, il est également constaté dans les applications web au travers d’activités malveillantes.|

> [!NOTE]
> La détection des menaces du Centre de sécurité pour App Service n’est pas disponible dans les régions de cloud souverain et Azure Government.

Pour plus d’informations sur les alertes de détection des menaces pour App Service, consultez Protéger App Service avec Azure Security Center et découvrez comment activer la supervision et la protection de vos charges de travail App Service.

## Conteneurs <a name="azure-containers"></a>

Security Center assure une détection des menaces en temps réel pour vos conteneurs sur des machines Linux basées sur le framework auditd. Les alertes identifient plusieurs activités Docker suspectes, comme la création d’un conteneur privilégié sur un hôte, l’indication de l’exécution d’un serveur SSH (Secure Shell) à l’intérieur d’un conteneur Docker ou l’utilisation de mineurs de chiffrement. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs. En plus des détections Linux, Security Center offre une analytique davantage tournée vers les déploiements de conteneurs.
