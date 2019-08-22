---
title: Images Red Hat Enterprise Linux dans Azure | Microsoft Docs
description: En savoir plus sur les images Red Hat Enterprise Linux dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 7d34e480dd3cf90f1948e83ea1d18c04f1dcdce2
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854439"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Images Red Hat Enterprise Linux dans Azure
Cet article décrit les images Red Hat Enterprise Linux (RHEL) disponibles sur la Place de marché Azure, ainsi que les stratégies relatives à leur dénomination et à leur rétention.

Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

>[!Important]
> Les images RHEL actuellement disponibles dans la place de marché Azure prennent en charge les modèles de licence BYOS (Apportez votre propre abonnement) ou Paiement à l’utilisation. [Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md) et le changement dynamique entre BYOS et le paiement à l’utilisation ne sont pas pris en charge. Le changement du mode de licence nécessite le redéploiement de la machine virtuelle à partir de l’image correspondante.

>[!Note]
> Pour tout problème lié aux images RHEL dans la galerie de la Place de marché Azure, ouvrez un ticket de support auprès de Microsoft.

## <a name="images-available-in-the-ui"></a>Images disponibles dans l’interface utilisateur
Lorsque vous recherchez « Red Hat » dans la Place de marché, ou lorsque vous créez une ressource dans l’interface utilisateur du portail Azure, vous voyez un sous-ensemble d’images RHEL disponibles et les produits Red Hat associés. Vous pouvez toujours obtenir l’ensemble des images de machine virtuelle disponibles à l’aide d’Azure CLI, de PowerShell ou de l’API.

Pour afficher l’ensemble des images Red Hat disponibles dans Azure, exécutez la commande suivante

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Conventions d’affectation de noms
Les images de machine virtuelle dans Azure sont organisées par éditeur, offre, référence SKU et version. La combinaison éditeur:offre:référence SKU:version est l’image URN et identifie de façon unique l’image à utiliser.

Par exemple, `RedHat:RHEL:7-RAW:7.6.2018103108` fait référence à une image RHEL 7.6 partitionnée au format brut, créée le 31 octobre 2018.

Vous trouverez ci-dessous un exemple montrant comment créer une machine virtuelle RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Moniker « latest »
L’API REST Azure permet l’utilisation du moniker « latest » comme version au lieu de la version spécifique. Utiliser la version « latest » a pour effet de provisionner la dernière image disponible pour la combinaison éditeur/offre/référence SKU donnée.

Par exemple, `RedHat:RHEL:7-RAW:latest` fait référence à la dernière image partitionnée au format brut RHEL 7 disponible.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> En règle générale, la comparaison des versions pour déterminer la dernière version suit les règles de la [méthode CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convention d’affectation de noms actuelle
Toutes les images RHEL actuellement publiées utilisent le modèle de paiement à l’utilisation et sont connectées à [Red Hat Update Infrastructure (RHUI) dans Azure](https://aka.ms/rhui-update). Une nouvelle convention d’affectation de noms a été adoptée pour les images de la famille RHEL 7 dans lesquelles le schéma de partitionnement de disque (raw, LVM) est spécifié dans la référence (SKU) au lieu de la version. La version de l’image RHEL contiendra 7-RAW ou 7-LVM. La dénomination de la famille RHEL 6 n’a pas changé pour l’instant.

Il y aura 2 types de références (SKU) d’images RHEL 7 dans cette convention d’affectation de noms : Références (SKU) qui indiquent la version mineure et références (SKU) qui ne le font pas. Si vous souhaitez utiliser une référence (SKU) 7-RAW ou 7-LVM , vous pouvez spécifier la version mineure RHEL que vous souhaitez déployer dans la version. Si vous choisissez la « dernière » version, la dernière version mineure de RHEL sera approvionnée.

>[!NOTE]
> Dans RHEL pour l’ensemble SAP d’images, la version RHEL reste fixe. Par conséquent, leur convention d’affectation de noms inclut une version particulière dans la référence SKU.

>[!NOTE]
> L’ensemble RHEL 6 d’images n’a pas été déplacé vers la nouvelle convention d’affectation de noms.

## <a name="extended-update-support-eus"></a>Extended Update Support (EUS)
Les images RHEL associées aux référentiels Extended Update Support (EUS) par défaut sont disponibles depuis le mois d’avril 2019. Vous trouverez plus d’informations sur la technologie EUS RHEL dans la [documentation de Red Hat](https://access.redhat.com/articles/rhel-eus).

Découvrez comment basculer votre machine virtuelle vers la technologie EUS et obtenez plus d’informations sur les dates de fin de prise en charge [ici](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> La technologie EUS n’est pas prise en charge par RHEL Extras. Cela signifie que vous ne pourrez pas, sur EUS, installer un package généralement disponible à partir du canal RHEL Extras. Le cycle de vie des produits Red Hat Extras est détaillé [ici](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Pour les clients qui veulent utiliser des images EUS :
Les clients qui souhaitent utiliser des images associées à des référentiels EUS doivent utiliser l’image RHEL qui contient un numéro de version mineure RHEL dans la référence (SKU). Ces images seront partitionnées au format brut (autrement dit, non LVM).

Par exemple, vous pouvez voir les 2 images RHEL 7.4 suivantes disponibles :
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Dans ce cas, `RedHat:RHEL:7.4:7.4.2019041718` est associé aux référentiels EUS par défaut, et `RedHat:RHEL:7-RAW:7.4.2018010506` aux référentiels non EUS par défaut.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Pour les clients qui ne veulent pas utiliser d’images EUS :
Si vous ne souhaitez pas utiliser d’image connectée à EUS par défaut, effectuez le déploiement à l’aide d’une image qui ne contient pas de numéro de version mineure dans la référence (SKU).

#### <a name="rhel-images-with-eus"></a>Images RHEL avec EUS
Le tableau suivant s’applique aux images RHEL qui contiennent une version mineure dans la référence (SKU).

>[!NOTE]
> Au moment de la rédaction, seules les versions mineures RHEL 7.4 et ultérieures sont compatibles EUS. La technologie EUS n’est plus prise en charge pour RHEL version 7.3 et antérieures.

Version secondaire |Exemple d’image EUS              |État EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Les images publiées en avril 2019 et après cette date seront compatibles EUS par défaut|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Les images publiées en juin 2019 et après cette date seront compatibles EUS par défaut |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Les images publiées en mai 2019 et après cette date seront compatibles EUS par défaut  |
RHEL 8.0      |N/A                            | Aucune image actuellement au format EUS n’est actuellement disponible                 |


## <a name="list-of-rhel-images-available"></a>Liste des images RHEL disponibles
Les offres suivantes sont les références SKU actuellement disponibles pour une utilisation générale :

Offre| SKU | Partitionnement | Approvisionnement | Notes
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Agent Linux | Famille d’images RHEL 7. <br> Pas associée à des référentiels EUS par défaut.
|             | 7-LVM    | LVM    | Agent Linux | Famille d’images RHEL 7. <br> Pas associé à des référentiels EUS par défaut.
|             | 7-RAW-CI | RAW-CI | Cloud-Init  | Famille d’images RHEL 7. <br> Pas associé à des référentiels EUS par défaut.
|             | 6.7      | RAW    | Agent Linux | Images RHEL 6.7, ancienne convention d’affectation de noms
|             | 6.8      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 6.8
|             | 6.9      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 6.9
|             | 6.10     | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 6.10
|             | 7,2      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 7.2
|             | 7.3      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 7.3
|             | 7.4      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 7.4 <br> Attaché à des référentiels EUS par défaut à compter d’avril 2019
|             | 7.5      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 7.5. <br> Attaché à des référentiels EUS par défaut à compter de juin 2019
|             | 7.6      | RAW    | Agent Linux | Identique à ce qui précède pour RHEL 7.6. <br> Attaché à des référentiels EUS par défaut à compter de mai 2019
RHEL-SAP      | 7.4      | LVM    | Agent Linux | RHEL 7.4 pour SAP HANA et les applications métiers
|             | 7.5      | LVM    | Agent Linux | RHEL 7.5 pour SAP HANA et les applications métiers
RHEL-SAP-HANA | 6.7      | RAW    | Agent Linux | RHEL 6.7 pour SAP HANA
|             | 7,2      | LVM    | Agent Linux | RHEL 7.2 pour SAP HANA
|             | 7.3      | LVM    | Agent Linux | RHEL 7.3 pour SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Agent Linux | RHEL 6.8 pour applications métiers SAP
|             | 7.3      | LVM    | Agent Linux | RHEL 7.3 pour applications métiers SAP
RHEL-HA       | 7.4      | RAW    | Agent Linux | RHEL 7.4 avec add-on HA
|             | 7.5      | RAW    | Agent Linux | RHEL 7.5 avec add-on HA
|             | 7.6      | RAW    | Agent Linux | RHEL 7.6 avec add-on HA
RHEL-SAP-HA   | 7.4      | RAW    | Agent Linux | RHEL 7.4 pour SAP avec add-on HA
|             | 7.5      | RAW    | Agent Linux | RHEL 7.5 pour SAP avec add-on HA
|             | 7.6      | RAW    | Agent Linux | RHEL 7.6 pour SAP avec add-on HA

### <a name="old-naming-convention"></a>Ancienne convention d’affectation de noms
La famille RHEL 7 d’images et la famille RHEL 6 d’images utilisaient des versions spécifiques dans leurs références SKU jusqu’à l’application du changement de convention d’affectation de noms expliqué ci-dessus.

Vous trouverez des références SKU numériques dans la liste complète des images. Microsoft et Red Hat vont créer de nouvelles références SKU numériques lors de la publication d’une nouvelle version mineure.

### <a name="other-available-offers-and-skus"></a>Autres offres et références SKU disponibles
La liste complète des offres et références SKU disponibles peut inclure des images supplémentaires au-delà de ce qui est indiqué dans le tableau ci-dessus, par exemple, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Ces offres peuvent être utilisées pour la prise en charge de solutions spécifiques de la Place de marché ou elles peuvent être publiées pour les préversions et à des fins de tests. Elles peuvent être modifiées ou supprimées à tout moment sans avertissement. Ne les utilisez pas, sauf si leur présence a été décrite publiquement par Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Politique de publication
Microsoft et Red Hat mettent à jour les images à mesure que de nouvelles versions mineures sont publiées, en fonction des besoins pour résoudre des CVE spécifiques, ou pour des modifications/mises à jour de configuration occasionnelles. Nous tentons de fournir des images mises à jour dès que possible, dans les trois jours ouvrables suivant la publication d’une nouvelle version ou la disponibilité d’un correctif CVE.

Nous ne mettons à jour que la version mineure en cours dans une famille d’images donnée. Avec la publication d’une version mineure plus récente, nous arrêtons la mise à jour de l’ancienne version mineure. Par exemple, avec la publication des images RHEL 7.6, les images RHEL 7.5 ne sont plus mises à jour.

>[!NOTE]
> Les machines virtuelles Azure actives provisionnées à partir d’images assorties d’un paiement à l’utilisation sont connectées à Azure RHUI et peuvent recevoir des mises à jour et des correctifs dès leur publication par Red Hat et la réplication vers Azure RHUI (généralement dans les 24 heures qui suivent la version officielle par Red Hat). Ces machines virtuelles ne nécessitent pas de nouvelle image publiée pour obtenir des mises à jour et les clients ont un contrôle total sur le moment où initier la mise à jour.

## <a name="image-retention-policy"></a>Stratégie de rétention d’image
Notre stratégie actuelle consiste à conserver toutes les images précédemment publiées. Nous nous réservons le droit de supprimer des images qui sont connues pour provoquer des problèmes de toutes sortes. Par exemple, les images avec des configurations incorrectes en raison de mises à jour de composant ou de plateforme ultérieures peuvent être supprimées. Les images susceptibles d’être supprimées respectent la politique de la Place de marché en cours qui est de fournir des notifications jusqu’à 30 jours avant la suppression de l’image.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez-en davantage sur l’infrastructure Red Hat Update Infrastructure (RHUI) Azure [ici](https://aka.ms/rhui-update).
* Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
