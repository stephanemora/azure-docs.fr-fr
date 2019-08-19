---
title: Protection de vos ressources réseau dans Azure Security Center | Microsoft Docs
description: Ce document traite des suggestions dans Azure Security Center qui peuvent vous aider à protéger vos ressources réseau Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 96c55a02-afd6-478b-9c1f-039528f3dea0
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: v-mohabe
ms.openlocfilehash: fb91d6a9cdc56c88b424b7e0382f283c8b55dac9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234325"
---
# <a name="protect-your-network-resources-in-azure-security-center"></a>Protection de vos ressources réseau dans Azure Security Center
Azure Security Center analyse continuellement l’état de sécurité de vos ressources Azure pour les meilleures pratiques de sécurité réseau. Lorsque Security Center identifie des failles de sécurité potentielles, il crée des suggestions qui vous guident tout au long du processus de configuration des contrôles nécessaires afin de renforcer et protéger vos ressources.

Cet article traite des suggestions relatives à la sécurité réseau qui s’appliquent à vos ressources Azure. Les suggestions relatives à la mise en réseau se concentrent autour des pare-feux nouvelle génération, des groupes de sécurité réseau, des règles de trafic entrant trop permissives concernant l’accès juste-à-temps aux machines virtuelles, et bien plus encore. Pour obtenir la liste des suggestions relatives à la mise en réseau et des actions de correction, consultez [Gestion des suggestions de sécurité dans Azure Security Center.](security-center-recommendations.md)

> [!NOTE]
> La page **Mise en réseau** vous permet d’en apprendre davantage sur l’intégrité de vos ressources Azure relative au réseau. Le mappage réseau et les contrôles réseau adaptatifs sont disponibles pour le niveau standard d’Azure Security Center uniquement. [Si vous utilisez le niveau gratuit, vous pouvez cliquer sur le bouton pour **Afficher la mise en réseau héritée** et recevoir des suggestions de ressources de mise en réseau](#legacy-networking).
>

La page **Mise en réseau** page fournit une vue d’ensemble des sections que vous pouvez approfondir. Pour obtenir plus d’informations sur l’intégrité de vos ressources réseau :

- Mappage réseau (niveau standard d’Azure Security Center uniquement)
- Durcissement réseau adaptatif
- Suggestions en matière de sécurité réseau.
- Panneau **Mise en réseau** héritée (panneau de mise en réseau précédent) 
 
![Volet Mise en réseau](./media/security-center-network-recommendations/networking-pane.png)

## <a name="network-map"></a>Mappage réseau
Le mappage réseau interactif fournit un affichage graphique avec des superpositions de sécurité qui vous proposent des suggestions et des aperçus pour renforcer vos ressources réseau. À l’aide du mappage, vous pouvez voir la topologie du réseau de vos charges de travail Azure et des connexions entre vos machines virtuelles et sous-réseaux. Vous pouvez voir la capacité à examiner au niveau du détail certaines ressources ainsi que leurs suggestions à partir du mappage.

Pour ouvrir le mappage réseau :

1. Dans Security Center, sous Hygiène de sécurité de la ressource, sélectionnez **Mise en réseau**.
2. Sous **Mappage réseau** cliquez sur **Voir topologie**.
 
La valeur par défaut du mappage de la topologie affiche :

- Les abonnements que vous avez sélectionné dans Azure. Le mappage prend en charge plusieurs abonnements.
- Machines virtuelles, sous-réseaux et réseaux virtuels du type de ressource Resource Manager (les ressources Azure Classic ne sont pas prises en charge)
- Réseaux virtuels appairés
- Uniquement les ressources disposant de [suggestions de réseau](security-center-recommendations.md) avec un niveau de gravité élevé ou moyen  
- Ressources accessibles sur Internet
- Le mappage est optimisé pour les abonnements que vous avez sélectionnés dans Azure. Si vous modifiez votre sélection, le mappage est recalculé et ré-optimisé en fonction de vos nouveaux paramètres.  

![Mappage de la topologie de mise en réseau](./media/security-center-network-recommendations/network-map-info.png)

## <a name="understanding-the-network-map"></a>Compréhension du mappage réseau

Le mappage réseau peut afficher vos ressources Azure dans un affichage **Topologie** et un affichage **Trafic**.

### <a name="the-topology-view"></a>Affichage Topologie

Dans l’affichage **Topologie** du mappage réseau, vous pouvez afficher les aperçus suivants concernant vos ressources de mise en réseau :

- Dans le cercle intérieur, vous pouvez voir tous les réseaux virtuels au sein de vos abonnements sélectionnés. Le cercle suivant contient tous les sous-réseaux. Le cercle extérieur contient toutes les machines virtuelles.
- Les lignes qui connectent vos ressources dans le mappage vous permettent de savoir quelles ressources sont associées entre elles et de connaître la structure de votre réseau Azure. 
- Utilisez les indicateurs de niveau de gravité pour obtenir rapidement une vue d’ensemble des ressources disposant de suggestions ouvertes de Security Center.
- Vous pouvez cliquer sur les ressources pour les explorer au niveau du détail et afficher les détails et suggestions directement ainsi que dans le contexte du mappage réseau.  
- Si trop de ressources sont affichées sur le mappage, Azure Security Center utilise son algorithme propriétaire pour mettre vos ressources en cluster de manière intelligente. Il met en surbrillance les ressources qui sont dans l’état le plus critique et disposent des suggestions de niveau de gravité le plus élevé. 

Étant donné que le mappage est interactif et dynamique, chaque nœud est interactif, et l’affichage peut changer en fonction des filtres :

1. Vous pouvez modifier ce que vous voyez sur le mappage réseau à l’aide de filtres en haut. Vous pouvez centrer le mappage sur :

   -  **Intégrité de la sécurité** : Vous pouvez filtrer le mappage selon la gravité (haute, moyenne, faible) de vos ressources Azure.
   - **Suggestions** : Vous pouvez sélectionner les ressources qui sont affichées selon les suggestions actives sur ces ressources. Par exemple, vous pouvez afficher uniquement les ressources pour lesquels Security Center vous recommande d’activer des groupes de sécurité réseau.
   - **Zones de réseau** : Par défaut, le mappage affiche uniquement les ressources accessibles sur Internet. Vous pouvez également sélectionner les machines virtuelles internes.
 
2. Vous pouvez cliquer sur **Réinitialiser** dans le coin supérieur gauche à tout moment pour retourner le mappage à son état par défaut.

Pour explorer une ressource au niveau du détail :

1. Lorsque vous sélectionnez une ressource spécifique sur le mappage, le volet droit s’ouvre et vous donne des informations générales sur la ressource, les solutions de sécurité connectées, s’il y en a, et les suggestions relatives à la ressource. Vous aurez le même type de comportement pour chaque type de ressource que vous sélectionnez. 
2. Lorsque vous pointez un nœud du mappage avec la souris, vous pouvez afficher des informations générales sur la ressource, y compris l’abonnement, le type de ressource et le groupe de ressources.
3. Utilisez le lien pour zoomer sur l’info-bulle et recentrer le mappage sur ce nœud spécifique. 
4. Pour recentrer le mappage sur un autre élément que ce nœud spécifique, effectuez un zoom arrière.

### <a name="the-traffic-view"></a>Affichage Trafic

L’affichage **Trafic** vous offre un mappage de tout les trafics possibles entre vos ressources. Il vous un mappage visuel de toutes les règles que vous avez configurées et qui définissent quelles ressources peuvent communiquer avec qui. Cela permet de voir la configuration existante des groupes de sécurité réseau ainsi que d’identifier rapidement les configurations à risque possibles au sein de vos charges de travail.

### <a name="uncover-unwanted-connections"></a>Découvrir les connexions indésirables

Le point fort de cet affichage se situe dans sa capacité à vous montrer ces connexions autorisées avec les vulnérabilités qui peuvent exister. Vous pouvez alors utiliser cette combinaison de données pour effectuer le renforcement nécessaire sur vos ressources. 

Par exemple, vous pouvez détecter deux machines pour lesquelles vous ignoriez qu’elles pouvaient communiquer. Cela vous permet de mieux isoler les charges de travail et les sous-réseaux.

### <a name="investigate-resources"></a>Examiner les ressources

Pour explorer une ressource au niveau du détail :

1. Lorsque vous sélectionnez une ressource spécifique sur le mappage, le volet droit s’ouvre et vous donne des informations générales sur la ressource, les solutions de sécurité connectées, s’il y en a, et les suggestions relatives à la ressource. Vous aurez le même type de comportement pour chaque type de ressource que vous sélectionnez. 
2. Cliquez sur **Trafic** pour afficher la liste des trafics entrant et sortant possibles sur la ressource. Il s’agit d’une liste complète de ceux qui peuvent communiquer avec la ressource, et inversement, ainsi que des protocoles et ports utilisés pour communiquer. Par exemple, quand vous sélectionnez une machine virtuelle, toutes les machines virtuelles avec lesquelles elle peut communiquer sont montrées, et quand vous sélectionnez un sous-réseau, tous les sous-réseaux avec lesquels elle peut communiquer sont montrés.

**Ces données sont basées sur les analyses de groupes de sécurité réseau, ainsi que sur des algorithmes de Machine Learning avancés qui analysent plusieurs règles pour en comprendre les croisements et les interactions.** 

![Mappage de trafic réseau](./media/security-center-network-recommendations/network-map-traffic.png)

## Mise en réseau héritée <a name ="legacy-networking"></a>

Si vous n’avez pas le niveau Standard de Security Center, cette section explique comment afficher les suggestions de mise en réseau gratuites.

Pour accéder à ces informations, dans le panneau Mise en réseau, cliquez sur **Afficher la mise en réseau héritée**. 

![Mise en réseau héritée](./media/security-center-network-recommendations/legacy-networking.png)

### <a name="internet-facing-endpoints-section"></a>Section des points de terminaison accessibles sur Internet
La section **Points de terminaison accessibles sur Internet** présente les machines virtuelles qui sont actuellement configurées avec un point de terminaison accessible sur Internet, ainsi que leur état actuel.

Ce tableau indique le nom du point de terminaison, l’adresse IP Internet ainsi que l’état de gravité actuel du groupe de sécurité réseau et des suggestions de pare-feu nouvelle génération. Le tableau est trié par niveau de gravité.

### <a name="networking-topology-section"></a>Section de topologie de mise en réseau
La section **Topologie de mise en réseau** contient un affichage hiérarchique des ressources.

Ce tableau est trié (machines virtuelles et sous-réseaux) par niveau de gravité.

Dans cet affichage topologique, le premier niveau affiche les réseaux virtuels. Le deuxième niveau affiche les sous-réseaux et le troisième niveau affiche les machines virtuelles appartenant à ces sous-réseaux. La colonne de droite indique l’état actuel des suggestions du groupe de sécurité réseau pour ces ressources.

Le troisième niveau affiche les machines virtuelles, ce qui est similaire à ce qui est décrit précédemment. Vous pouvez cliquer sur une ressource pour en savoir plus ou pour appliquer la configuration ou le contrôle de sécurité nécessaire.

## <a name="network-recommendations"></a>Recommandations pour le réseau

|Nom de la recommandation|Description|severity|Degré de sécurisation|Type de ressource|
|----|----|----|----|----|----|
|Les groupes de sécurité réseau situés au niveau du sous-réseau doivent être activés|Activez des groupes de sécurité réseau pour contrôler l'accès réseau des ressources déployées sur vos sous-réseaux.|Élevé / Moyen|30|Subnet|
|Les machines virtuelles doivent être associées à un groupe de sécurité réseau|Activez des groupes de sécurité réseau pour contrôler l’accès réseau de vos machines virtuelles.|Élevé / Moyen|30|Machine virtuelle|
|L'accès doit être limité pour les groupes de sécurité réseau permissifs avec machines virtuelles connectées à Internet|Renforcez les groupes de sécurité réseau de vos machines virtuelles accessibles sur Internet, en limitant l'accès des règles d'autorisation existantes.|Élevé|20|Machine virtuelle|
|Les règles relatives aux applications web doivent être renforcées sur les groupes de sécurité réseau IaaS|Renforcez le groupe de sécurité réseau (NSG) des machines virtuelles qui exécutent des applications web lorsque les règles NSG sont trop permissives en ce qui concerne les ports des applications web.|Élevé|20|Machine virtuelle|
|L'accès à App Services doit être limité|Limitez l'accès à App Services en modifiant la configuration du réseau pour empêcher le trafic entrant provenant de plages trop larges.|Élevé|10|App Service|
|Les ports de gestion doivent être fermés sur vos machines virtuelles|Renforcez le groupe de sécurité réseau de vos machines virtuelles de manière à limiter l'accès aux ports de gestion.|Élevé|10|Machine virtuelle|
DDoS Protection Standard doit être activé|Protégez les réseaux virtuels contenant des applications avec adresses IP publiques en activant la norme de service de protection DDoS. La protection DDoS permet de limiter les risques d'attaques volumétriques et les attaques par protocole réseau.|Élevé|10|Réseau virtuel|
|Le transfert IP doit être désactivé sur votre machine virtuelle|Désactivez le transfert IP. Lorsque le transfert IP est activé sur la carte réseau d'une machine virtuelle, cette dernière peut recevoir du trafic adressé à d'autres destinations. Le transfert IP n'est que rarement nécessaire (par exemple, lors de l'utilisation de la machine virtuelle en tant qu'appliance virtuelle de réseau). Par conséquent, un examen par l'équipe de sécurité réseau est requis.|Moyenne|10|Machine virtuelle|
|L'application web ne doit pas être accessible via HTTPS|Activez l'accès « HTTPS uniquement » pour les applications web. L'utilisation de HTTPS garantit l'authentification du serveur/service, et protège les données en transit contre les attaques par écoute clandestine de la couche réseau.|Moyenne|20|Application web|
|Le contrôle d'accès réseau juste-à-temps doit être appliqué sur les machines virtuelles|Appliquez un contrôle d'accès de machine virtuelle Juste-à-temps (JAT) pour bloquer définitivement l'accès aux ports sélectionnés, et permettez aux utilisateurs autorisés de les ouvrir, via JAT, pour une durée limitée.|Élevé|20|Machine virtuelle|
|Les applications Function App ne doivent être accessibles que via HTTPS|Activez l'accès « HTTPS uniquement » pour les applications Function App. L'utilisation de HTTPS garantit l'authentification du serveur/service, et protège les données en transit contre les attaques par écoute clandestine de la couche réseau.|Moyenne|20|Conteneur de fonctions|
|La sécurisation du transfert vers des comptes de stockage doit être activée|Activez le transfert sécurisé vers des comptes de stockage. L’option de sécurisation du transfert oblige votre compte de stockage à accepter uniquement des requêtes provenant de connexions sécurisées (HTTPS). L'utilisation de HTTPS garantit l'authentification entre le serveur et le service et protège les données en transit contre les attaques de la couche réseau (attaque de l'intercepteur ou « man-in-the-middle », écoute clandestine, détournement de session).|Élevé|20|Compte de stockage|

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, consultez les rubriques suivantes :

* [Protection de vos machines virtuelles dans Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Protection de vos applications dans Azure Security Center](security-center-application-recommendations.md)
* [Protection de votre service SQL Azure dans Azure Security Center](security-center-sql-service-recommendations.md)

Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
