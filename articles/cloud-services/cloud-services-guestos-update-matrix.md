---
title: En savoir plus sur les dernières publications du SE invité de Microsoft Azure | Microsoft Docs
description: Dernières informations de publication et de compatibilité du Kit de développement logiciel (SDK) pour le SE invité de Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/08/2018
ms.author: raiye
ms.openlocfilehash: 35888b205822e5d7c9576c9855610bad814901b6
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2018
ms.locfileid: "51346165"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure
Fournit des informations récentes sur les dernières publications du SE (système d’exploitation) invité de Microsoft Azure pour Cloud Services. Ces informations vous permettent de planifier votre mise à niveau avant la désactivation d’un SE invité. Si vous configurez vos rôles pour utiliser les mises à jour *automatiques* d’un SE invité comme cela est décrit dans [Paramètres de mise à jour du SE invité Azure][Azure Guest OS Update Settings], il n’est pas indispensable que vous lisiez cette page.

> [!IMPORTANT]
> Le contenu de cette page s’applique aux rôles web et de travail Cloud Services, qui s’exécutent sur un SE invité. Il **ne s’applique pas** aux machines virtuelles IaaS.
>
>


> [!TIP]
>  Abonnez-vous au [Flux RSS de mise à jour du SE invité] pour recevoir en temps voulu les notifications relatives à toutes les modifications du SE invité.
>
>

> [!IMPORTANT]
> Seules les 2 dernières versions du système d’exploitation invité seront prises en charge et disponibles dans le portail Microsoft Azure.
>
>

Vous ne savez pas comment mettre à jour votre système d’exploitation invité ? Consultez [cet article][cloud updates].

## <a name="news-updates"></a>Nouvelles mises à jour

###### <a name="november-8-2018"></a>**8 novembre 2018**
Publication de l’OS invité octobre.

###### <a name="october-12-2018"></a>**12 octobre 2018**
Publication du système d’exploitation invité du mois de septembre.

###### <a name="september-12-2018"></a>**12 septembre 2018**
Publication du système d’exploitation invité du mois d’août.

###### <a name="august-3-2018"></a>**3 août 2018**
Le système d’exploitation invité de juillet a été publié.

###### <a name="july-3-2018"></a>**3 juillet 2018**
Le système d’exploitation invité de juin a été publié.

###### <a name="june-1-2018"></a>**1er juin 2018**
Le système d’exploitation invité de mai a été publié.

###### <a name="may-4-2018"></a>**4 mai 2018**
Le système d’exploitation invité d’avril a été publié.


## <a name="releases"></a>Publications
## <a name="family-5-releases"></a>Publications de famille 5
**Windows Server 2016**

.NET Framework installé : 3.5, 4.6.2

> [!NOTE]
> Le mot de passe RDP pour la famille 5 de SE doit comporter au moins 10 caractères.
>

| Chaîne de configuration | Date de lancement | Date de désactivation |
| --- | --- | --- |
| WA-GUEST-OS-5.24_201810-01 |8 novembre 2018 |Post 5.26 |
| WA-GUEST-OS-5.23_201809-01 |12 octobre 2018 |Post 5.25 |
|~~WA-GUEST-OS-5.22_201808-01~~ |12 septembre 2018 |8 novembre 2018 |
|~~WA-GUEST-OS-5.21_201807-02~~ |3 août 2018 |12 octobre 2018 |
|~~WA-GUEST-OS-5.20_201806-01~~ |3 juillet 2018 |12 septembre 2018 |
|~~WA-GUEST-OS-5.19_201805-01~~ |1er juin 2018 |3 août 2018 |
|~~WA-GUEST-OS-5.18_201804-01~~ |4 mai 2018 |3 juillet 2018 |

## <a name="family-4-releases"></a>Publications de famille 4
**Windows Server 2012 R2**

.NET Framework installé : 3.5, 4.5.1

| Chaîne de configuration | Date de lancement | Date de désactivation |
| --- | --- | --- |
| WA-GUEST-OS-4.59_201810-01 |8 novembre 2018 |POST 4.61 |
| WA-GUEST-OS-4.58_201809-01 |12 octobre 2018 |Post 4.60 |
|~~WA-GUEST-OS-4.57_201808-01~~ |12 septembre 2018 |8 novembre 2018 |
|~~WA-GUEST-OS-4.56_201807-02~~ |3 août 2018 |12 octobre 2018 |
|~~WA-GUEST-OS-4.55_201806-01~~ |3 juillet 2018 |12 septembre 2018 |
|~~WA-GUEST-OS-4.54_201805-01~~ |1er juin 2018 |3 août 2018 |
|~~WA-GUEST-OS-4.53_201804-01~~ |4 mai 2018 |3 juillet 2018 |

## <a name="family-3-releases"></a>Publications de famille 3
**Windows Server 2012**

.NET Framework installé : 3.5, 4.5

| Chaîne de configuration | Date de lancement | Date de désactivation |
| --- | --- | --- |
| WA-GUEST-OS-3.66_201810-01 |8 novembre 2018 |Post 3.68 |
| WA-GUEST-OS-3.65_201809-01 |12 octobre 2018 |Post 3.67 |
|~~WA-GUEST-OS-3.64_201808-01~~ |12 septembre 2018 |8 novembre 2018 |
|~~WA-GUEST-OS-3.63_201807-02~~ |3 août 2018 |12 octobre 2018 |
|~~WA-GUEST-OS-3.62_201806-01~~ |3 juillet 2018 |12 septembre 2018 |
|~~WA-GUEST-OS-3.61_201805-01~~ |1er juin 2018 |3 août 2018 |
|~~WA-GUEST-OS-3.60_201804-01~~ |4 mai 2018 |3 juillet 2018 |

## <a name="family-2-releases"></a>Publications de famille 2
**Windows Server 2008 R2 SP1**

.NET Framework installé : 3.5 (comprend 2.0 et 3.0)

| Chaîne de configuration | Date de lancement | Date de désactivation |
| --- | --- | --- |
| WA-GUEST-OS-2.79_201810-01 |8 novembre 2018 |Post 2.81 |
| WA-GUEST-OS-2.78_201809-01 |12 octobre 2018 |Post 2.80 |
|~~WA-GUEST-OS-2.77_201808-01~~ |12 septembre 2018 |8 novembre 2018 |
|~~WA-GUEST-OS-2.76_201807-02~~ |3 août 2018 |12 octobre 2018 |
|~~WA-GUEST-OS-2.75_201806-01~~ |3 juillet 2018 |12 septembre 2018 |
|~~WA-GUEST-OS-2.74_201805-01~~ |1er juin 2018 |3 août 2018|
|~~WA-GUEST-OS-2.73_201804-01~~ |4 mai 2018 |3 juillet 2018 |

## <a name="msrc-patch-updates"></a>Mises à jour correctives MSRC
La liste des correctifs inclus dans chaque publication mensuelle du SE invité est disponible [ici][patches].

## <a name="sdk-support"></a>Prise en charge des Kits de développement logiciel (SDK)
La [stratégie de déclassement pour le Kit de développement logiciel (SDK) Azure][retire policy sdk] indique que seules les versions postérieures à la version 2.2 sont prises en charge. Cependant, certaines familles de SE invités permettent l’utilisation de versions antérieures. Vous devez toujours utiliser le Kit de développement logiciel (SDK) pris en charge le plus récent.

| Famille de système d’exploitation invité | Versions de kit SDK compatibles |
| --- | --- |
| 5. |Versions 2.9.5.1 et ultérieures |
| 4 |Versions 2.1 et ultérieures |
| 3 |Versions 1.8 et ultérieures |
| 2 |Versions 1.3 et ultérieures |
| 1 |Versions 1.0 et ultérieures |

## <a name="guest-os-release-information"></a>Informations de publication du système d’exploitation invité
Trois dates sont importantes à propos des publications du SE invité : la date de **publication**, la date de **désactivation** et la date **d’expiration**. Un SE invité est considéré comme disponible quand il est proposé dans le portail et qu’il peut être sélectionné comme SE invité cible. Quand un SE invité atteint sa date de **désactivation**, il est supprimé d’Azure. Toutefois, tous les services cloud qui ciblent ce SE invité continuent de fonctionner normalement.

Durant la période entre la date de **désactivation** et la date **d’expiration**, vous pouvez facilement passer de votre SE invité actuel à une version plus récente. Si vous utilisez la mise à jour *automatique* pour votre SE invité, vous disposez toujours de la version la plus récente. Vous n’avez donc pas à vous soucier de la date d’expiration.

Après la date **d’expiration** du SE invité, tous les services cloud qui utilisent encore ce SE invité sont arrêtés, supprimés ou mis à niveau de manière forcée. Des informations supplémentaires sur la stratégie de déclassement sont fournies [ici][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Informations sur les versions des familles de système d’exploitation invité
Les familles de SE invité sont basées sur les versions publiées de Microsoft Windows Server. Le SE invité est le système d’exploitation sous-jacent sur lequel s’exécute Azure Cloud Services. Chaque SE invité possède un numéro de famille, de version et de publication.

* **Guest OS family**  
   Version du système d’exploitation Windows Server sur laquelle est basé un SE invité. Par exemple, la *famille 3* est basée sur Windows Server 2012.
* **Version de SE invité**  
  Correspond à l’image de la famille de SE invité et aux correctifs [Microsoft Security Response Center (MSRC)][msrc] appropriés qui sont disponibles à la date de production de la nouvelle version du SE invité. Il est possible que les correctifs ne soient pas tous inclus.

    Les numéros commencent à 0 et augmentent de 1 chaque fois qu’un nouvel ensemble de mises à jour est ajouté. Les zéros à droite sont uniquement affichés s’ils sont importants. Autrement dit, la version 2.10 est une version différente, bien plus récente, que la version 2.1.
* **Publication de SE invité**  
   Nouvelle publication d’une version de SE invité. Une nouvelle publication est produite si Microsoft détecte des problèmes pendant les tests nécessitant des modifications. La publication la plus récente remplace toujours les publications précédentes, publiques ou non. Le portail Azure permet uniquement aux utilisateurs de choisir la publication la plus récente pour une version donnée. Les déploiements s’exécutant sur une publication antérieure ne sont généralement pas mis à niveau de force en fonction de la gravité du bogue.

Dans l’exemple ci-dessous, 2 est la famille, 12 est la version et « rel2 » est la publication.

**Publication du SE invité** - 2.12 rel2

**Chaîne de configuration pour cette publication** - WA-GUEST-OS-2.12_201208-02

Ces informations sont intégrées dans la chaîne de configuration d’un SE invité, ainsi qu’une date indiquant les correctifs MSRC envisagés pour cette publication. Dans cet exemple, les correctifs MSRC produits pour Windows Server 2008 R2 jusqu’à août 2012 inclus ont été envisagés pour l’inclusion. Seuls les correctifs s’appliquant spécifiquement à cette version de Windows Server sont inclus. Par exemple, si un correctif logiciel de MSRC s’applique à Microsoft Office, il ne sera pas inclus, car ce produit ne fait pas partie de l’image de base de Windows Server.

## <a name="guest-os-system-update-process"></a>Processus de mise à jour du système d’exploitation invité
Cette page contient des informations sur les prochaines publications de SE invités. Les clients ont indiqué qu’ils souhaitent être informés lors d’une publication, car leurs rôles de service cloud redémarreront s’ils sont définis pour la mise à jour « Automatique ». Les publications de systèmes d’exploitation invités ont généralement lieu 2 à 3 jours au moins après la publication de mise à jour MSRC survenant le deuxième mardi de chaque mois. Les nouvelles publications incluent tous les correctifs MSRC adéquats pour chaque famille de SE invité.

Microsoft Azure publie constamment des mises à jour. Le SE invité n’est qu’une mise à jour parmi d’autres. Une version peut être affectée par de nombreux facteurs trop nombreux pour être répertoriés ici. En outre, Azure s’exécute sur des centaines de milliers d’ordinateurs. Cela signifie qu’il est impossible de donner une date et une heure précises pour le redémarrage de vos rôles. Nous travaillons sur un plan visant à limiter ou minuter les redémarrages.

La propagation complète sur Azure d’une nouvelle publication de SE invité peut prendre du temps. Lorsque les services sont mis à jour pour le nouveau SE invité, ils sont redémarrés en respectant les domaines de mise à jour. Les services définis pour utiliser des mises à jour « Automatiques » obtiennent une publication en premier. Après la mise à jour, la nouvelle version du SE invité est répertoriée pour votre service dans le portail Azure. De nouvelles publications peuvent se produire pendant cette période. Il est possible que certaines versions soient déployées sur de longues périodes et donc que des redémarrages de mise à niveau automatiques ne se produisent pas pendant plusieurs semaines après la date officielle de la publication. Une fois qu’un SE invité est disponible, vous pouvez choisir explicitement cette version à partir du portail ou dans votre fichier de configuration.

Pour consulter un grand nombre d’informations utiles sur les redémarrages et des orientations vers d’autres informations techniques sur les mises à jour de SE invités et hôtes, consultez le blog de MSDN intitulé [Redémarrage d’instance de rôle en raison d’une mise à niveau du SE][restarts].

Si vous procédez à une mise à jour manuelle de votre SE invité, consultez la page [Stratégie de suppression du SE invité][retirepolicy] pour plus d’informations.

## <a name="guest-os-supportability-and-retirement-policy"></a>Stratégie de prise en charge et de suppression du système d’exploitation invité
La prise en charge et la stratégie de suppression du SE invité sont détaillées [ici][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Flux RSS de mise à jour du SE invité]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
