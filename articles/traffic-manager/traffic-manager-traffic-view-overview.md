---
title: Vue du trafic dans Azure Traffic Manager
description: Dans cette introduction, vous découvrirez le fonctionnement de la vue du trafic de Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743047"
---
# <a name="traffic-manager-traffic-view"></a>Traffic View de Traffic Manager

Traffic Manager assure un routage au niveau du DNS (Domain Name System). Ce service permet à vos utilisateurs finaux d’accéder à des points de terminaison sains suivant la méthode de routage de votre choix. La Vue du trafic permet à Microsoft Azure Traffic Manager d’afficher vos bases d’utilisateurs (avec une granularité au niveau de la résolution DNS) et de leur schéma de trafic. Lorsque vous activez Traffic View, ces informations sont traitées pour vous fournir des renseignements exploitables. 

Grâce à Traffic View, vous pouvez :
- comprendre où se trouvent vos bases d’utilisateurs (jusqu’à une granularité fixée au niveau de la résolution DNS locale)
- voir le volume de trafic (observé pour les requêtes DNS gérées par Azure Traffic Manager) provenant de ces régions.
- obtenir des insights sur la latence représentative expérimentée par ces utilisateurs.
- explorez en profondeur les modèles de trafic spécifiques à partir de chacune de ces bases d’utilisateurs vers les régions Azure où vous avez des points de terminaison. 

Par exemple, vous pouvez utiliser Vue du trafic pour savoir quelles régions enregistrent un volume de trafic important, mais connaissent des latences supérieures. Vous vous appuyez ensuite sur ces informations pour planifier votre expansion à de nouvelles régions Azure. Vos utilisateurs bénéficient ainsi d’une latence plus faible.

## <a name="how-traffic-view-works"></a>Fonctionnement de Traffic View

La Vue du trafic fonctionne en examinant les requêtes entrantes reçues au cours des sept derniers jours pour un profil. À partir des informations sur les requêtes entrantes, la Vue du trafic extrait l’adresse IP source de la résolution DNS, utilisée pour représenter l’emplacement des utilisateurs. Ces informations sont regroupées au niveau d’une résolution DNS pour créer les régions de base des utilisateurs. Traffic Manager conserve les informations géographiques des adresses IP. Il examine ensuite les régions Azure vers lesquelles la requête est routée, et constitue une carte des flux de trafic pour les utilisateurs issus de ces régions.
 
À l’étape suivante, Traffic Manager met en corrélation la région de base des utilisateurs avec la carte des régions Azure à l’aide de la table de latence de l’intelligence réseau. Cette table est conservée pour permettre à différents réseaux d’utilisateurs finaux de connaître la latence moyenne rencontrée par les utilisateurs de ces régions lorsqu’ils se connectent à des régions Azure. Tous ces calculs sont ensuite regroupés sous forme de tableaux au niveau de l’adresse IP de la résolution DNS locale avant de vous être présentés. Vous pouvez utiliser les informations de différentes manières.

La fréquence de mise à jour des données de la Vue du trafic dépend de plusieurs variables de service internes. Toutefois, les données sont actualisées toutes les 24 heures.

>[!NOTE]
>La latence décrite dans la Vue du trafic correspond à la latence représentative entre l’utilisateur final et les régions Azure auxquelles il est connecté. Il ne s’agit pas de la latence des recherches DNS. Vue du trafic permet une meilleure estimation de la latence entre le programme de résolution DNS locale et la région Azure vers laquelle la requête est acheminée, s’il n’y a pas suffisamment de données disponible, la latence retournée est null. 

## <a name="visual-overview"></a>Présentation visuelle

Lorsque vous accédez à la section **Vue du trafic** de votre page Azure Traffic Manager, vous voyez apparaître une carte géographique incluant une superposition des insights Vue du trafic. Cette carte fournit des informations sur la base d’utilisateurs et les points de terminaison de votre profil Traffic Manager.

![Vue géographique Vue du trafic Traffic Manager][1]

### <a name="user-base-information"></a>Informations de la base d’utilisateurs

Lorsqu’elles sont disponibles, les informations sur l’emplacement des résolutions DNS locales sont affichées sur la carte. La couleur du programme de résolution DNS indique la latence moyenne rencontrée par les utilisateurs finaux ayant utilisé ce programme de résolution DNS pour les requêtes Traffic Manager.

Si vous pointez sur un emplacement du programme de résolution DNS dans le mappage, il affiche les informations suivantes :
- l’adresse IP du programme de résolution DNS ;
- la quantité de trafic de requête DNS affichée par Traffic Manager à partir de celui-ci ;
- les points de terminaison pour lesquels le trafic à partir du programme de résolution DNS a été routé, sous forme de ligne entre le point de terminaison et le programme de résolution DNS ; 
- la latence moyenne entre cet emplacement et le point de terminaison, représentée par la couleur de la ligne qui les relie.

### <a name="endpoint-information"></a>Informations sur le point de terminaison

Les régions Azure dans lesquelles se trouvent les points de terminaison s’affichent sous la forme de points bleus sur la carte. Si votre point de terminaison est externe et ne correspond à aucune région Azure, il apparaît en haut de la carte. Sélectionnez n’importe quel point de terminaison pour voir les différents emplacements (en fonction de la résolution DNS utilisée) de provenance de son trafic. Les connexions se présentent sous la forme d’une ligne entre le point de terminaison et l’emplacement de la résolution DNS. Elles sont colorées en fonction de la latence représentative de cette paire. Sont indiqués le nom du point de terminaison et la région Azure dans laquelle il s’exécute. Le volume total de demandes qui y sont dirigées par ce profil Traffic Manager s’affiche également.


## <a name="tabular-listing-and-raw-data-download"></a>Téléchargement de données brutes et liste de tableaux

Vous pouvez afficher les données relatives à la Vue du trafic sous la forme d’un tableau, dans le portail Azure. Il existe une entrée pour chaque paire adresse IP de la résolution DNS/point de terminaison qui donne différentes informations :

* Adresse IP de la résolution DNS
* Nom.
* Emplacement géographique de la région Azure dans laquelle se trouve le point de terminaison (le cas échéant)
* Volume des demandes associées à cette résolution DNS à ce point de terminaison
* Latence représentative associée aux utilisateurs finaux utilisant ce DNS (le cas échéant) 

Vous pouvez également télécharger les données de la Vue du trafic dans un fichier CSV, qui peut être utilisé dans le cadre du flux de travail analytique de votre choix.

## <a name="billing"></a>Facturation

Lorsque vous utilisez la Vue du trafic, c’est le nombre de points de données utilisés pour créer les insights présentés qui sert de base de facturation. Actuellement, le seul type de point de données utilisé correspond aux requêtes reçues par rapport à votre profil Traffic Manager. Pour plus d’informations sur les prix, visitez la page [Tarifs Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>FAQ

* [À quoi sert la fonctionnalité Affichage du trafic ?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Quels sont les avantages de la fonctionnalité Affichage du trafic ?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [En quoi la fonctionnalité Affichage du trafic diffère-t-elle des métriques Traffic Manager disponibles dans Azure Monitor ?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [La fonctionnalité Affichage du trafic utilise-t-elle les informations sur le sous-réseau du client EDNS ?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Combien de jours de données la fonctionnalité Affichage du trafic utilise-t-elle ?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [Comment la fonctionnalité Affichage du trafic gère-t-elle les points de terminaison externes ?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [Dois-je activer la fonctionnalité Affichage du trafic pour chaque profil de mon abonnement ?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Comment désactiver la fonctionnalité Affichage du trafic ?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Comment la fonctionnalité Affichage du trafic est-elle facturée ?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-overview.md)
- En savoir plus sur les [méthodes de routage du trafic](traffic-manager-routing-methods.md) prises en charge par Traffic Manager
- En savoir plus sur la [création d’un profil Traffic Manager](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png