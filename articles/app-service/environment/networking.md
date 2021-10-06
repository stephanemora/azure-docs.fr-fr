---
title: Mise en réseau d’App Service Environment
description: Détails de la mise en réseau d’App Service Environment
author: madsd
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 06/30/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 177a9095a6a1cfb15a7bd17e106406521d1eda14
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639013"
---
# <a name="app-service-environment-networking"></a>Mise en réseau d’App Service Environment

> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service Isolé v2
> 


App Service Environment (ASE) est un déploiement monolocataire d’Azure App Service qui héberge des applications web, des applications API et des applications de fonction. Lorsque vous installez un environnement ASE, vous choisissez le réseau virtuel Azure (VNet) dans lequel vous souhaitez qu’il soit déployé. L’ensemble de l’application de trafic entrant et sortant se trouve à l’intérieur du réseau virtuel que vous spécifiez. ASE est déployé dans un sous-réseau unique de votre réseau virtuel. Rien d’autre ne peut être déployé dans ce même sous-réseau.

## <a name="subnet-requirements"></a>Configuration requise du sous-réseau

Le sous-réseau doit être vide et délégué à Microsoft.Web/hostingEnvironments.

La taille du sous-réseau peut avoir une incidence sur les limites de mise à l’échelle des instances de plan App Service au sein de l’ASE. Nous vous recommandons d’utiliser un espace d’adressage/24 (256 adresses) pour votre sous-réseau afin de garantir un nombre suffisant d’adresses pour prendre en charge la mise à l’échelle de production.

Pour utiliser un sous-réseau plus petit, vous devez tenir compte des détails suivants relatifs à l’ASE et à la configuration réseau.

Tout sous-réseau donné possède cinq adresses réservées aux fins de gestion. En plus des adresses de gestion, ASE mettra à l’échelle de manière dynamique l’infrastructure de prise en charge et utilisera entre 4 et 27 adresses, en fonction de la configuration, de l’échelle et de la charge. Les adresses restantes peuvent être utilisées pour les instances du plan d’App Service. La taille minimale de votre sous-réseau est un espace d’adressage/27 (32 adresses).

L’effet de l’exécution d’adresses insuffisantes est que vous pouvez être limité à la montée en charge de vos plans d’App Service dans l’ASE ou vous pouvez constater une latence accrue pendant la charge du trafic intensif si nous ne sommes pas en mesure de mettre à l’échelle l’infrastructure de prise en charge.

## <a name="addresses"></a>Adresses 

ASE dispose des informations réseau suivantes lors de sa création :

| Type d’adresse | description |
|--------------|-------------|
| Réseau virtuel ASE | Réseau virtuel dans lequel ASE est déployé |
| Sous-réseau de l’ASE | Sous-réseau dans lequel ASE est déployé |
| Suffixe de domaine | Suffixe de domaine utilisé par les applications réalisées dans cet environnement ASE |
| Adresse IP virtuelle | Ce paramètre représente l’adresse IP virtuelle utilisée par l’ASE. Les deux valeurs possibles sont interne et externe |
| Adresse entrante | L’adresse entrante correspond à l’adresse à laquelle vos applications sur cet environnement ASE sont atteintes. Si vous avez une adresse IP virtuelle interne, il s’agit d’une adresse de votre sous-réseau ASE. Si l’adresse est externe, il s’agit d’une adresse publique |
| Adresses sortantes par défaut | Les applications de cet environnement ASE utilisent cette adresse, par défaut, lors de l’établissement d’appels sortants vers Internet |

ASEv3 contient des détails sur les adresses utilisées par l’environnement ASE dans la partie **Adresses IP** du portail ASE.

![Interface utilisateur des adresses ASE](./media/networking/networking-ip-addresses.png)

À mesure que vous mettez à l’échelle vos plans App Service dans votre environnement ASE, vous utiliserez davantage d’adresses de votre sous-réseau ASE. Le nombre d’adresses utilisées varie selon le nombre d’instances du plan App Service que vous avez et du volume de trafic qu’ASE reçoit. Les applications de l’environnement ASE n’ont pas d’adresses dédiées dans le sous-réseau ASE. Les adresses spécifiques utilisées par une application dans le sous-réseau ASE par une application changent au fil du temps.

## <a name="ports"></a>Ports

L’environnement ASE reçoit le trafic d’application sur les ports 80 et 443. Si ces ports sont bloqués, vous ne pouvez pas atteindre vos applications. 

> [!NOTE]
> Le port 80 doit être autorisé à partir d’AzureLoadBalancer vers le sous-réseau ASE pour le trafic Keep Alive entre l’équilibreur de charge et l’infrastructure ASE. Vous pouvez toujours contrôler le trafic du port 80 vers l’adresse IP virtuelle de votre environnement ASE.
> 

## <a name="extra-configurations"></a>Configurations supplémentaires

Vous pouvez définir des groupes de sécurité réseau (NSG) et des tables de routage (UDR) comme vous le souhaitez, sans restriction. Vous pouvez forcer le tunnelage de tout le trafic sortant de votre environnement ASE vers un périphérique de pare-feu de sortie, tel que Pare-feu Azure, et ne pas avoir à vous soucier d’autre chose que des dépendances de vos applications. Vous pouvez placer des périphériques WAF, tels que la passerelle applicative, devant le trafic entrant vers votre environnement ASE pour exposer des applications spécifiques sur cet environnement. Pour obtenir une autre adresse sortante dédiée à l’Internet, vous pouvez utiliser NAT Gateway avec votre environnement ASE. Pour utiliser une passerelle NAT avec votre environnement ASE, configurez la NAT Gateway sur le sous-réseau ASE. 

## <a name="dns"></a>DNS

### <a name="dns-configuration-to-your-ase"></a>Configuration DNS vers votre environnement ASE

Si votre environnement ASE est créé avec une adresse IP virtuelle externe, vos applications sont automatiquement placées dans un DNS public. Si votre environnement ASE est créé avec une adresse IP virtuelle interne, vous devrez peut-être configurer le service DNS pour celle-ci. Si vous avez choisi de configurer automatiquement les zones privées Azure DNS lors de la création de l’environnement ASE, DNS est configuré dans votre réseau virtuel ASE. Si vous avez sélectionné l’option de configuration manuelle de DNS, vous devez utiliser votre propre serveur DNS ou configurer les zones privées Azure DNS. Pour trouver l’adresse entrante de votre ASE, accédez à l’interface utilisateur **portail ASE > adresses IP**. 

Si vous souhaitez utiliser votre propre serveur DNS, vous devez ajouter les enregistrements suivants :

1. créez une zone pour &lt;nom ASE&gt;.appserviceenvironment.net
1. créez un enregistrement A dans cette zone qui pointe * vers l’adresse IP entrante qu’utilise votre ASE
1. créez un enregistrement A dans cette zone qui pointe @ vers l’adresse IP entrante qu’utilise votre ASE
1. créez une zone dans le scm nommé &lt;nom ASE&gt;.appserviceenvironment.net
1. Créez un enregistrement A dans la zone scm qui pointe * vers l’adresse IP qu’utilise le point de terminaison privé de votre ASE.

Pour configurer DNS dans les zones privées Azure DNS :

1. créez une zone privée Azure DNS nommée `<ASE-name>.appserviceenvironment.net`
1. créez un enregistrement A dans cette zone qui pointe * vers l’adresse IP entrante
1. créez un enregistrement A dans cette zone qui pointe @ vers l’adresse IP entrante
1. créez un enregistrement A dans cette zone qui pointe *.scm vers l’adresse IP entrante

Les paramètres DNS du suffixe de domaine par défaut de votre ASE ne limitent pas vos applications à être accessibles uniquement par ces noms. Vous pouvez définir un nom de domaine personnalisé sans validation sur vos applications dans un ASE. Si vous souhaitez ensuite créer une zone nommée *contoso.net*, vous pouvez le faire et la pointer vers l’adresse IP entrante. Le nom de domaine personnalisé fonctionne pour les demandes d’application, mais pas pour le site GCL. Le site GCL est disponible uniquement pour *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net*. 

#### <a name="dns-configuration-from-your-ase"></a>Configuration DNS à partir de votre environnement ASE

Les applications de votre environnement ASE utilisent le DNS avec lequel votre réseau virtuel est configuré. Si vous souhaitez que certaines applications utilisent un serveur DNS différent de celui avec lequel votre réseau virtuel est configuré, vous pouvez définir celui-ci manuellement pour chaque application avec les paramètres d’application WEBSITE_DNS_SERVER et WEBSITE_DNS_ALT_SERVER. Le paramètre d’application WEBSITE_DNS_ALT_SERVER configure le serveur DNS secondaire. Le serveur DNS secondaire est utilisé uniquement en l’absence de réponse du serveur DNS principal. 

## <a name="limitations"></a>Limites

Bien qu’ASE se déploie dans un réseau virtuel client, il existe quelques fonctionnalités de mise en réseau qui ne sont pas disponibles avec ASE : 

* Envoyer du trafic SMTP. Vous pouvez toujours bénéficier des alertes déclenchées par e-mail, mais votre application ne peut pas envoyer de trafic sortant sur le port 25
* Utiliser Network Watcher ou un flux NSG pour surveiller le trafic sortant

## <a name="more-resources"></a>Plus de ressources

- [Informations de référence sur les variables d’environnement et les paramètres d’application](../reference-app-settings.md)