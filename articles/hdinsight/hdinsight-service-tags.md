---
title: Étiquettes de service de groupe de sécurité réseau (NSG) pour Azure HDInsight
description: Utilisez les balises de service HDInsight pour autoriser le trafic entrant vers votre cluster à partir des nœuds des services d’intégrité et de gestion HDInsight, sans ajouter explicitement d’adresses IP à vos groupes de sécurité réseau.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117236"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Étiquettes de service de groupe de sécurité réseau (NSG) pour Azure HDInsight

Les balises de service HDInsight pour les groupes de sécurité réseau sont des groupes d’adresses IP pour les services d’intégrité et de gestion. Ces groupes permettent de réduire la complexité de la création de règles de sécurité. Les [balises de service](../virtual-network/security-overview.md#service-tags) fournissent une autre méthode pour autoriser le trafic entrant à partir d’adresses IP spécifiques sans entrer chacune des [adresses IP de gestion](hdinsight-management-ip-addresses.md) dans vos groupes de sécurité réseau.

Ces balises de service sont créées et gérées par le service HDInsight. Vous ne pouvez pas créer votre propre balise de service ou modifier une balise existante. Microsoft gère les préfixes d’adresse correspondant à la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

## <a name="getting-started-with-service-tags"></a>Bien démarrer avec les balises de service

Vous avez le choix entre deux options pour utiliser les balises de service dans vos groupes de sécurité réseau :

1. Utiliser une seule balise de service HDInsight : cette option permet d’ouvrir votre réseau virtuel pour toutes les adresses IP que le service HDInsight utilise pour surveiller les clusters dans toutes les régions. Cette option est la méthode la plus simple, mais elle peut ne pas être appropriée si vous avez des exigences de sécurité restrictives.

1. Utiliser plusieurs balises de service régional : cette option permet d’ouvrir votre réseau virtuel uniquement pour les adresses IP que HDInsight utilise dans cette région spécifique. Toutefois, si vous utilisez plusieurs régions, vous devez ajouter plusieurs balises de service à votre réseau virtuel.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Utiliser une balise de service HDInsight globale unique

Le moyen le plus simple de commencer à utiliser les balises de service avec votre cluster HDInsight consiste à ajouter la balise globale `HDInsight` à une règle de groupe de sécurité réseau.

1. Dans le [Portail Azure](https://portal.azure.com/), sélectionnez votre groupe de sécurité réseau.

1. Sous **Paramètres**, sélectionnez **Règles de sécurité de trafic entrant**, puis sélectionnez **+ Ajouter**.

1. Dans la liste déroulante **Source**, sélectionnez **Étiquette de service**.

1. Dans la liste déroulante **Étiquette du service source**, sélectionnez **HDInsight**.

    ![Portail Azure – Ajouter une balise de service](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Cette balise contient les adresses IP des services d’intégrité et de gestion pour toutes les régions où HDInsight est disponible, et garantit que votre cluster peut communiquer avec les services d’intégrité et de gestion requis, où qu’il soit créé.

## <a name="use-regional-hdinsight-service-tags"></a>Utiliser les balises de service régional HDInsight

Si l’option 1 ne fonctionne pas car vous avez besoin d’autorisations plus restrictives, vous pouvez autoriser uniquement les balises de service applicables à votre région. Les balises de service applicables peuvent correspondre à une, deux ou trois balises de service, en fonction de la région dans laquelle votre cluster est créé.

Pour connaître les balises de service à ajouter pour votre région, lisez les sections suivantes du document.

### <a name="use-a-single-regional-service-tag"></a>Utiliser une seule balise de service régional

Si vous préférez l’option de balise de service deux et que votre cluster se trouve dans une des régions indiquées dans ce tableau, il vous suffit d’ajouter une seule balise de service régional à votre groupe de sécurité réseau.

| Country | Région | Balise du service |
| ---- | ---- | ---- |
| Australie | Australie Est | HDInsight.AustraliaEast |
| &nbsp; | Sud-Australie Est | HDInsight.AustraliaSoutheast |
| &nbsp; | Centre de l’Australie | HDInsight.AustraliaCentral |
| Chine | Chine orientale 2 | HDInsight.ChinaEast2 |
| &nbsp; | Chine Nord 2 | HDInsight.ChinaNorth2 |
| États-Unis | Centre-Nord des États-Unis | HDInsight.NorthCentralUS |
| &nbsp; | USA Ouest 2 | HDInsight.WestUS2 |
| &nbsp; | Centre-USA Ouest | HDInsight.WestCentralUS |
| Canada | Est du Canada | HDInsight.CanadaEast |
| Brésil | Brésil Sud | HDInsight.BrazilSouth |
| Corée du Sud | Centre de la Corée | HDInsight.KoreaCentral |
| &nbsp; | Corée du Sud | HDInsight.KoreaSouth |
| Inde | Inde centrale | HDInsight.CentralIndia |
| &nbsp; | Inde Sud | HDInsight.SouthIndia |
| Japon | OuJapon Est | HDInsight.JapanWest |
| France | France Centre| HDInsight.FranceCentral |
| Royaume-Uni | Sud du Royaume-Uni | HDInsight.UKSouth |
| Azure Government | US DoD - Centre   | HDInsight.USDoDCentral |
| &nbsp; | Gouvernement des États-Unis - Texas | HDInsight.USGovTexas |
| &nbsp; | US DoD - Est | HDInsight.USDoDEast |
| &nbsp; | Gouvernement des États-Unis - Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Utiliser plusieurs balises de service régional

Si vous préférez l’option de balise de service deux et que la région dans laquelle votre cluster est créé n’est pas indiquée ci-dessus, vous devez autoriser plusieurs balises de service régional. La nécessité d’en utiliser plusieurs est due à des différences de disposition des fournisseurs de ressources pour les différentes régions.

Les régions restantes sont divisées en groupes en fonction des balises de service régional qu’elles utilisent.

#### <a name="group-1"></a>Groupe 1

Si votre cluster est créé dans une des régions du tableau ci-dessous, autorisez les balises de service `HDInsight.WestUS` et `HDInsight.EastUS` en plus de la balise de service régional indiquée. Les régions de cette section nécessitent trois balises de service.

Par exemple, si votre cluster est créé dans la région `East US 2`, vous devez ajouter les balises de service suivantes à votre groupe de sécurité réseau :

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Région | Balise du service |
| ---- | ---- | ---- |
| États-Unis | USA Est 2 | HDInsight.EastUS2 |
| &nbsp; | USA Centre | HDInsight.CentralUS |
| &nbsp; | NorthCentral US | HDInsight. NorthCentralUS |
| &nbsp; | États-Unis - partie centrale méridionale | HDInsight.SouthCentralUS |
| &nbsp; | USA Est | HDInsight.EastUS |
| &nbsp; | USA Ouest | HDInsight.WestUS |
| Japon | Japon Est | HDInsight.JapanEast |
| Europe | Europe Nord | HDInsight.NorthEurope |
| &nbsp; | Europe Ouest| HDInsight.WestEurope |
| Asia | Asie Est | HDInsight.EastAsia |
| &nbsp; | Asie Sud-Est | HDInsight.SoutheastAsia |
| Australie | Australie Est | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Groupe 2

Les clusters dans les régions **Chine Nord** et **Chine Est** doivent autoriser deux balises de service : `HDInsight.ChinaNorth` et `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Groupe 3

Les clusters dans les régions **US Gov Iowa** et **US Gov Virginie** doivent autoriser deux balises de service : `HDInsight.USGovIowa` et `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Groupe 4

Les clusters dans les régions **Allemagne Centre** et **Allemagne Nord-Est** doivent autoriser deux balises de service : `HDInsight.GermanyCentral` et `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Étapes suivantes

- [Groupes de sécurité réseau - Balises de service](../virtual-network/security-overview.md#security-rules)
- [Créer des réseaux virtuels pour les clusters Azure HDInsight](hdinsight-create-virtual-network.md)
