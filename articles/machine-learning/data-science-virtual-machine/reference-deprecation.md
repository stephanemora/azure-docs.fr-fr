---
title: 'Référence : Mise hors service des images Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Détails sur les mises hors service affectant Azure Data Science Virtual Machine
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816318"
---
# <a name="reference-retirements-of-dsvm-images"></a>Référence : Mises hors service d’images DSVM

Vous abordons ci-dessous la mise hors service (dépréciation) des images, et proposons des suggestions pour traiter les retraits à venir sur Data Science Virtual Machine.

## <a name="why-we-retire-dsvm-images"></a>Pourquoi mettre hors service les images DSVM

La machine virtuelle de science des données (DSVM) possède actuellement deux éditions :

* Ubuntu
* Windows Server

Pour ces éditions, l’image DSVM repose sur une version spécifique du système d’exploitation, par exemple Ubuntu 18.04 LTS. Au fil du temps, la fenêtre de maintenance de la _version_ du système d’exploitation prendra fin et/ou les outils de science des données ne prendront plus en charge les versions de système d’exploitation plus anciennes. Pour maintenir à jour l’image DSVM avec les systèmes d’exploitation et les outils de science des données les plus récents, nous sortons une nouvelle image DSVM basée sur des versions de système d’exploitation plus récentes.

## <a name="how-we-retire-dsvm-images"></a>Comment mettre hors service les images DSVM

Notre _annonce de mise hors service_ est adressée par e-mail aux utilisateurs DSVM afin de les informer sur le prochain retrait de l’image DVSM. L’annonce de mise hors service détaillera la _date de suppression_ (après cette date, l’image ne sera plus disponible) et les recommandations d’atténuation (par exemple, la mise à niveau vers une image DSVM plus récente).

À la date de _l’annonce_, nous masquerons l’image sur la place de marché, de sorte que :

1. les nouveaux utilisateurs ne puissent pas approvisionner par inadvertance une image DSVM hors service.
2. les utilisateurs existants puissent utiliser des déploiements ARM jusqu’à la date de mise hors service.

L’image masquée recevra les correctifs du système d’exploitation jusqu’à la _date de mise hors service_ mais elle __ne recevra pas__ les mises à jour des outils et infrastructures de science des données. À la _date de mise hors service_, l’image ne sera pas disponible pour les déploiements ARM.

Toute image DSVM approvisionnée dans votre abonnement continuera à fonctionner après la date de mise hors service. Toutefois, nous vous recommandons de procéder à une mise à niveau vers la dernière image DSVM pour disposer des systèmes d’exploitation et des outils de science des données les plus récents.

## <a name="impact"></a>Impact

Toute image DSVM approvisionnée existant dans votre abonnement continuera à fonctionner après la date de mise hors service. Toutefois, nous recommandons aux utilisateurs de mettre à niveau leur image DSVM vers la version plus récente à l’aide du modèle Portail Azure ou ARM.

> [!WARNING]
> Les images DSVM mises hors service approvisionnées à l’aide de Virtual Machine Scale Sets ne seront pas mises à l’échelle après la date de mise hors service.
>
> Les modèles ARM qui n’ont pas été mis à jour avec les nouveaux détails de l’image DSVM ne peuvent pas être déployés après la date de mise hors service.

## <a name="mitigating-upcoming-retirements"></a>Atténuation des mises hors service à venir

Dans cette section, nous aborderons l’atténuation des mises hors service à venir.

### <a name="upgrade-windows-2016-dsvm"></a>Mettre à niveau Windows 2016 DSVM

Pour migrer un disque de données de votre machine virtuelle DSVM Windows 2016 vers une machine virtuelle DSVM Windows 2019, procédez comme suit :

1. Créez une nouvelle machine virtuelle DSVM Windows 2019, en suivant les instructions indiquées [ici](./provision-vm.md#create-your-dsvm).
1. Détachez les disques de données existants de votre image Windows 2016 en suivant [ces instructions](../../virtual-machines/windows/detach-disk.md).
1. Attachez le disque de l’étape précédente à votre image Windows 2019 en suivant [ces instructions](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

### <a name="upgrade-ubuntu-1604-dsvm"></a>Mettre à niveau Ubuntu 16.04 DSVM

Nous vous recommandons de procéder à la mise à niveau des Data Science VM Ubuntu 16.04 vers l’[édition 18.04 d’Ubuntu DSVM](./dsvm-ubuntu-intro.md).
