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
ms.openlocfilehash: d5f541dec14eebc944e4eac11dbe569b38cb277e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001618"
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

