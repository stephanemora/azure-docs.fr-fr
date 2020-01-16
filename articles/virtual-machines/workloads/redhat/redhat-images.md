---
title: Images Red Hat Enterprise Linux dans Azure | Microsoft Docs
description: En savoir plus sur les images Red Hat Enterprise Linux dans Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 211ac68fd10cd745faf68a5efae7392345008d7b
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941449"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Vue d’ensemble des images Red Hat Enterprise Linux
Cet article décrit les images Red Hat Enterprise Linux (RHEL) disponibles sur la Place de marché Azure, ainsi que les stratégies relatives à leur dénomination et à leur rétention.

Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata). Pour plus d’informations tarifaires, voir la [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

>[!IMPORTANT]
> Les images RHEL actuellement disponibles dans la place de marché Azure prennent en charge les modèles de licence BYOS (Apportez votre propre abonnement) ou Paiement à l’utilisation. [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) et le changement dynamique entre BYOS et le paiement à l’utilisation ne sont pas pris en charge. Le changement du mode de licence nécessite le redéploiement de la machine virtuelle à partir de l’image correspondante.

>[!NOTE]
> Pour tout problème lié aux images RHEL sur la Place de marché Azure, ouvrez un ticket de support auprès de Microsoft.

## <a name="images-available-in-azure"></a>Images disponibles sur Azure
Lorsque vous recherchez « Red Hat » sur la place de marché, ou que vous créez une ressource dans l’interface utilisateur du Portail Azure, vous ne voyez qu’un sous-ensemble des images RHEL disponibles. Vous pouvez toujours obtenir l’ensemble des images de machine virtuelle disponibles à l’aide d’Azure CLI, de PowerShell ou de l’API.

Pour afficher l’ensemble des images Red Hat disponibles dans Azure, exécutez la commande suivante

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Conventions d’affectation de noms
Les images de machine virtuelle dans Azure sont organisées par éditeur, offre, référence SKU et version. La combinaison éditeur:offre:référence SKU:version est l’image URN et identifie de façon unique l’image à utiliser.

Par exemple, `RedHat:RHEL:7-LVM:7.6.2018103108` fait référence à une image RHEL 7.6 partitionnée au format LVM, créée le 31 octobre 2018.

Vous trouverez ci-dessous un exemple montrant comment créer une machine virtuelle RHEL 7.6.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>Moniker « latest »
L’API REST Azure permet l’utilisation du moniker « latest » comme version au lieu de la version spécifique. Utiliser la version « latest » a pour effet de provisionner la dernière image disponible pour la combinaison éditeur/offre/référence SKU donnée.

Par exemple, `RedHat:RHEL:7-LVM:latest` fait référence à la dernière image partitionnée au format LVM de la famille RHEL 7 disponible.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> En règle générale, la comparaison des versions pour déterminer la dernière version suit les règles de la [méthode CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="rhel-6-image-types"></a>Types d’images RHEL 6
Les types d’images RHEL 6.x sont les suivants :

|Serveur de publication | Offre | Valeur de référence SKU | Version | Détails
|----------|-------|-----------|---------|--------
|Red Hat | RHEL | Version mineure (par exemple, 6.9) | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 6.9.2018010506) | Toutes les images standard RHEL 6.x respectent cette convention
|Red Hat | rhel-byos | rhel-raw69 | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 6.9.20181023) | Cette image est une image RHEL 6.9 BYOS.
|Red Hat | RHEL | RHEL-SAP-APPS | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 6.8.2017053118) | Il s’agit d’une image RHEL 6.8 for SAP Applications. Elle est autorisée à accéder aux référentiels d’applications SAP, ainsi qu’aux référentiels RHEL de base.
|Red Hat | RHEL | RHEL-SAP-HANA | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 6.7.2017053121) | Il s’agit d’une image RHEL 6.7 for SAP HANA. Elle est autorisée à accéder aux référentiels SAP HANA, ainsi qu’aux référentiels RHEL de base.

### <a name="rhel-7-image-types"></a>Types d’images RHEL 7
Il existe différents types d’images RHEL 7.x. Le tableau suivant présente les différents jeux d’images que nous proposons. La commande CLI az `az vm image list --publisher redhat --all` permet d’en afficher la liste complète.

>[!NOTE]
> Sauf indication contraire, toutes les images sont partitionnées au format LVM et se connectent à des référentiels RHEL standard (c’est-à-dire non EUS ni E4S). À l’avenir, nous ne publierons que des images partitionnées au format LVM, mais nous sommes ouverts à tout commentaire sur cette décision. Pour plus d’informations sur le support EUS (Extended Update Support) et les services de mise à jour pour SAP, voir la page [Cycle de vie Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Serveur de publication | Offre | Valeur de référence SKU | Version | Détails
|----------|-------|------------|---------|--------
|Red Hat | RHEL | Version mineure (par exemple, 7.6) | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019102813) | Les images publiées avant avril 2019 seront attachées à des référentiels RHEL standard. Celles qui seront publiées après avril 2019 seront associées aux référentiels EUS (Extended Update Support) de Red Hat pour permettre le verrouillage d’une version mineure spécifique. Les clients qui souhaitent des référentiels standard sont invités à utiliser les images dont la valeur de référence SKU comporte 7-LVM ou 7-RAW (pour plus d’informations, voir ci-dessous). Les images RHEL 7.7 et versions ultérieures seront partitionnées au format LVM. Toutes les autres images de cette catégorie sont à partitions brutes.
|Red Hat | RHEL | 7-RAW | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019102813) | Ces images sont à partitions brutes (c’est-à-dire qu’aucun volume logique n’a été ajouté).
|Red Hat | RHEL | 7-RAW-CI | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019072418) | Ces images sont à partitions brutes (c’est-à-dire qu’aucun volume logique n’a été ajouté) et utilisent cloud-init pour le provisionnement.
|Red Hat | RHEL | 7-LVM | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019062414) | Ces images sont partitionnées au format LVM.
|Red Hat | rhel-byos | rhel-{lvm,raw} | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.7.20190819) | Il s’agit des images RHEL 7 BYOS. Elles ne sont associées à aucun référentiel et ne sont pas soumises aux frais de la prime RHEL. Si les images RHEL BYOS vous intéressent, [faites une demande d’accès](https://aka.ms/rhel-byos). Les valeurs SKU se terminent par la version mineure et indiquent si l’image est à partitions brutes ou partitionnée au format LVM. Par exemple, la valeur SKU rhel-lvm77 indique une image RHEL 7.7 partitionnée au format LVM.
|Red Hat | RHEL | RHEL-SAP | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019071300) | Il s’agit d’images RHEL for SAP. Elles sont autorisées à accéder aux référentiels SAP HANA et aux référentiels d’applications, ainsi qu’aux référentiels RHEL E4S. La facturation comprend la prime RHEL et la prime SAP en plus des frais de calcul de base.
|Red Hat | RHEL | RHEL-SAP-HA | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019062320) | Il s’agit d’images RHEL for SAP avec des services de haute disponibilité et de mise à jour. Elles sont autorisées à accéder aux référentiels SAP HANA, aux référentiels d’applications et aux référentiels de haute disponibilité, ainsi qu’aux référentiels RHEL E4S. La facturation comprend la prime RHEL, la prime SAP et la prime de haute disponibilité en plus des frais de calcul de base.
|Red Hat | RHEL | RHEL-HA | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.6.2019062019) | Il s’agit d’images RHEL qui sont également autorisées à accéder au module complémentaire de haute disponibilité. Elles sont soumises à quelques frais supplémentaires par rapport aux frais de calcul RHEL et de base en raison de la prime du module complémentaire de haute disponibilité.
|Red Hat | RHEL | RHEL-SAP-APPS | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.3.2017053118) | Ces images sont obsolètes, car les référentiels d’applications SAP et les référentiels SAP HANA ont été regroupés dans les référentiels SAP. Il s’agit d’images RHEL for SAP Applications. Elles sont autorisées à accéder aux référentiels d’applications SAP, ainsi qu’aux référentiels RHEL de base.
|Red Hat | RHEL | RHEL-SAP-HANA | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 7.3.2018051421) | Ces images sont obsolètes, car les référentiels d’applications SAP et les référentiels SAP HANA ont été regroupés dans les référentiels SAP. Il s’agit d’images RHEL for SAP HANA. Elles sont autorisées à accéder aux référentiels SAP HANA, ainsi qu’aux référentiels RHEL de base.

### <a name="rhel-8-image-types"></a>Types d’images RHEL 8
Voici les détails des types d’images RHEL 8.

|Serveur de publication | Offre | Valeur de référence SKU | Version | Détails
|----------|-------|------------|---------|--------
|Red Hat | RHEL | 8 | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 8.0.20191023) | Il s’agit d’images RHEL 8.0 partitionnées au format LVM connectées à des référentiels Red Hat standard.
|Red Hat | RHEL | 8-gen2 | Valeurs concaténées de la version mineure RHEL et de la date de publication (par exemple, 8.0.20191024) | Il s’agit d’images RHEL 8.0 partitionnées au format LVM Hyper-V deuxième génération connectées à des référentiels Red Hat standard. Pour plus d’informations sur les machines virtuelles de deuxième génération dans Azure, [cliquez ici](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="extended-update-support-eus"></a>Extended Update Support (EUS)
Les images RHEL associées aux référentiels Extended Update Support (EUS) par défaut sont disponibles depuis le mois d’avril 2019. Vous trouverez plus d’informations sur la technologie EUS RHEL dans la [documentation de Red Hat](https://access.redhat.com/articles/rhel-eus).

Le basculement vers des référentiels EUS est possible et pris en charge. Découvrez comment basculer votre machine virtuelle vers la technologie EUS et obtenez plus d’informations sur les dates de fin de prise en charge [ici](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> La technologie EUS n’est pas prise en charge par RHEL Extras. Cela signifie que vous ne pourrez pas, sur EUS, installer un package généralement disponible à partir du canal RHEL Extras. Le cycle de vie des produits Red Hat Extras est détaillé [ici](https://access.redhat.com/support/policy/updates/extras/).

### <a name="differentiating-between-regular-and-eus-images"></a>Différence entre les images standard et les images EUS
Les clients qui souhaitent utiliser des images associées à des référentiels EUS doivent utiliser l’image RHEL qui contient un numéro de version mineure RHEL dans la référence (SKU).

Par exemple, vous pouvez voir les deux images RHEL 7.4 suivantes disponibles :
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
Dans ce cas, `RedHat:RHEL:7.6:7.6.2019102813` est associé aux référentiels EUS par défaut (valeur de référence SKU : 7.4), et `RedHat:RHEL:7-LVM:7.6.2019062414` aux référentiels non EUS par défaut (valeur de référence SKU : 7-LVM).

Si vous souhaitez utiliser des référentiels standard (non EUS), effectuez le déploiement à l’aide d’une image dont la référence SKU ne comporte pas de numéro de version mineure.

#### <a name="rhel-images-with-eus"></a>Images RHEL avec EUS
Le tableau suivant s’applique aux images RHEL connectées à des référentiels EUS.

>[!NOTE]
> Au moment de la rédaction, seules les versions mineures RHEL 7.4 et ultérieures sont compatibles EUS. La technologie EUS n’est plus prise en charge pour RHEL version 7.3 et antérieures.
>
> Vous trouverez plus d’informations sur la disponibilité de RHEL EUS [ici](https://access.redhat.com/support/policy/updates/errata).

Version secondaire |Exemple d’image EUS              |État EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Les images publiées en avril 2019 et après cette date seront compatibles EUS par défaut|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Les images publiées en juin 2019 et après cette date seront compatibles EUS par défaut |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Les images publiées en mai 2019 et après cette date seront compatibles EUS par défaut  |
RHEL 8.0      |N/A                            | Aucune image EUS disponible à partir de Red Hat                               |





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
* Consultez la liste complète des [images RHEL dans Azure](./redhat-imagelist.md).
* Apprenez-en davantage sur l’infrastructure Red Hat Update Infrastructure (RHUI) Azure [ici](https://aka.ms/rhui-update).
* Découvrez [l’offre RHEL BYOS](./byos.md).
* Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
