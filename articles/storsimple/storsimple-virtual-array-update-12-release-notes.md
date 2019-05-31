---
title: Notes de publication Microsoft Azure StorSimple Virtual Array Update 1.2 | Microsoft Docs
description: Décrit les problèmes critiques et résolutions pour StorSimple Virtual Array exécutant Update 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420602"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Notes de publication de StorSimple Virtual Array Update 1.2

Les notes suivantes identifient les problèmes majeurs existants et les problèmes résolus dans les mises à jour Microsoft Azure StorSimple Virtual Array.

Les notes de publication sont continuellement mises à jour. Les problèmes critiques nécessitant une solution de contournement sont découvertes, ils sont ajoutés. Lisez les informations contenues dans les notes de publication avant de déployer votre instance StorSimple Virtual Array.

Update 1.2 correspond à la version du logiciel **10.0.10311.0**.

> [!IMPORTANT]
> - Les mises à jour entraînent des perturbations et redémarrent votre appareil. Si des E/S sont en cours, l’appareil subit des interruptions de service. Pour obtenir des instructions détaillées sur les packages utilisés pour appliquer cette mise à jour, accédez à [télécharger Update 1.2](#download-update-12).
> - Mise à jour 1.2 est disponible via le portail Azure uniquement si votre appareil exécute Update 1.0 ou 1.1.

## <a name="whats-new-in-update-12"></a>Nouveautés d'Update 1.2

Cette mise à jour contient les correctifs suivants :

- Amélioration de la résilience lors du traitement des fichiers supprimés.
- Gestion améliorée des exceptions dans le chemin d’accès de démarrage de code conduisant à réduit les défaillances dans les sauvegardes, de restauration, de lectures cloud et automatisée de récupération de l’espace.

## <a name="download-update-12"></a>Téléchargez la mise à jour 1.2

Pour télécharger cette mise à jour, accédez à la [catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) serveur et package de téléchargement le KB4502035. Ce package contient les packages suivants :

 - **KB4493446** qui contient des mises à jour cumulatives de Windows pour 2012 R2 jusqu'à avril 2019. Pour plus d’informations sur ce qui est inclus dans ce correctif, accédez à [correctif de sécurité mensuel avril](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** qui est un fichier de Package autonome Microsoft WindowsTH-KB3011067-x64. Ce fichier est utilisé pour mettre à jour le logiciel de l’appareil.

Télécharger KB4502035 et suivez ces instructions pour [appliquer la mise à jour via l’interface utilisateur web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problèmes résolus dans Update 1.2

Le tableau suivant récapitule les problèmes corrigés dans cette version.

| Non. | Fonctionnalité | Problème |
| --- | --- | --- |
| 1 |Suppression| Dans les versions précédentes du logiciel, il a été un problème lors de l’utilisation de l’appareil n’a pas changé, même lorsque les fichiers ont été supprimés. Ce problème est résolu dans cette version. Hiérarchisation du chemin d’accès du code a été effectuée plus résiliente lors du traitement des fichiers supprimés.|
| 2 |Gestion des exceptions| Dans les versions précédentes du logiciel, un problème est survenu après le redémarrage du système qui pourrait conduire à des échecs dans les sauvegardes, restauration, la lecture à partir du cloud et automatisée de récupération de l’espace. Cette version contient des modifications quant à la façon dont les exceptions étaient gérées dans le chemin d’accès de démarrage.|

## <a name="known-issues-in-update-12"></a>Problèmes connus dans Update 1.2

Aucun nouveau problème ont été signalés dans Update 1.2 mise en production. Tous les problèmes signalés à la mise en production sont transférés à partir de versions précédentes. Pour afficher le récapitulatif des problèmes connus inclus dans les versions précédentes, accédez à [problèmes connus dans Update 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Étapes suivantes

Télécharger KB4502035 et [appliquer la mise à jour via l’interface utilisateur web locale](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Références

Vous recherchez une note de version antérieure ? Accédez à :
* [StorSimple Virtual Array Update 1.1 Notes de publication](storsimple-virtual-array-update-11-release-notes.md)
* [Notes de version de StorSimple Virtual Array Update 1.0](storsimple-virtual-array-update-1-release-notes.md)
* [Notes de version de StorSimple Virtual Array Update 0.6](storsimple-virtual-array-update-06-release-notes.md)
* [Notes de publication de StorSimple Virtual Array Update 0.5](storsimple-virtual-array-update-05-release-notes.md)
* [Notes de publication de StorSimple Virtual Array Update 0.4](storsimple-virtual-array-update-04-release-notes.md)
* [Notes de publication de StorSimple Virtual Array Update 0.3](storsimple-ova-update-03-release-notes.md)
* [Notes de publication de StorSimple Virtual Array Update 0.1 et 0.2](storsimple-ova-update-01-release-notes.md)
* [Notes de publication de StorSimple Virtual Array General Availability](storsimple-ova-pp-release-notes.md)
