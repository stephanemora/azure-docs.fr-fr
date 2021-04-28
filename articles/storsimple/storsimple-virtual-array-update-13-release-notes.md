---
title: Notes de version de Microsoft Azure StorSimple Virtual Array Update 1.3 | Microsoft Docs
description: Décrit les problèmes critiques non résolus d’Azure StorSimple Virtual Array Update 1.3 et fournit des solutions.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389517"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Notes de version de StorSimple Virtual Array Update 1.3

Les notes suivantes identifient les problèmes majeurs existants et les problèmes résolus dans les mises à jour Microsoft Azure StorSimple Virtual Array.

Les notes de publication sont constamment mises à jour. Les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez les informations contenues dans les notes de publication avant de déployer votre instance StorSimple Virtual Array.

Update 1.3 correspond à la version logicielle 10.0.10319.0.

> [!IMPORTANT]
> - Update 1.3 est une mise à jour critique. Nous vous recommandons vivement de l’installer dès que possible.
> - Vous pouvez installer la mise à jour Update 1.3 uniquement sur les appareils exécutant la mise à jour Update 1.2.
> - Les mises à jour entraînent des perturbations et redémarrent votre appareil. Si des E/S sont en cours, l’appareil subit des interruptions de service. Pour obtenir des instructions détaillées sur les packages qui sont utilisés pour appliquer cette mise à jour, consultez [Télécharger Update 1.3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Nouveautés d’Update 1.3

Cette mise à jour contient les améliorations suivantes : KB4540725

- Transport Layer Security (TLS) 1.2 est une mise à jour obligatoire et doit être installée. À partir de cette version, TLS 1.2 devient le protocole standard pour toutes les communications du Portail Azure.
  
   Si l’avertissement suivant s’affiche, vous devez mettre à jour le logiciel sur l’appareil avant de continuer :

   Un ou plusieurs appareils StorSimple exécutent une version logicielle plus ancienne. La dernière mise à jour disponible pour TLS 1.2 est une mise à jour obligatoire qui doit être installée immédiatement sur ces appareils. Le protocole TLS 1.2 est utilisé pour toutes les communications sur le portail Azure et, sans cette mise à jour, l’appareil ne pourra pas communiquer avec le service StorSimple.

- Les correctifs de bogues de nettoyage de la mémoire (garbage collection) améliorent les performances du cycle de nettoyage de la mémoire quand l’appareil et le compte de stockage se trouvent dans deux régions distantes.
- Correction des échecs de sauvegarde en raison de délais d’attente d’objets blob.
- Mises à jour des correctifs de sécurité du système d’exploitation/.NET Framework :
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86) : Mise à jour de la pile de maintenance (SSU) de mars 2020
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01) : Correctif cumulatif de juillet 2020
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00) : Mise à jour .NET Framework de juillet 2020

## <a name="download-update-13"></a>Télécharger Update 1.3

Pour télécharger cette mise à jour, accédez au [ Catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx), puis téléchargez le package KB4575898. Ce package contient les packages suivants. Installez les packages dans cet ordre :

1. **KB4540725**, qui contient des mises à jour cumulatives pour Windows 2012 R2, jusqu’au mois de mars 2020. Pour plus d’informations sur le contenu de ce correctif cumulatif, consultez [Correctif cumulatif mensuel du mois de mars](https://support.microsoft.com/help/4540725).
1. **KB4565541**, qui contient des mises à jour cumulatives pour Windows 2012 R2, jusqu’au mois de juillet 2020. Pour plus d’informations sur le contenu de ce correctif cumulatif, consultez [Correctif cumulatif de sécurité mensuel du mois de juillet](https://support.microsoft.com/help/4565541).
1. **KB4565622**, qui contient les mises à jour cumulatives .NET Framework jusqu’au mois de juillet 2020. Pour plus d’informations sur le contenu de ce correctif cumulatif, accédez à [KB4565622](https://support.microsoft.com/help/4565622).<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067**, qui contient les mises à jour du logiciel de l’appareil.

Téléchargez KB4575898 et suivez les instructions pour [appliquer la mise à jour via l’interface utilisateur web locale](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Problèmes connus dans Update 1.3
Aucun problème n’a été signalé concernant Update 1.3. Tous les problèmes mentionnés dans les notes de publication proviennent des versions précédentes. Pour voir le récapitulatif des problèmes connus concernant les versions précédentes, accédez à [Problèmes connus dans Update 1.2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Étapes suivantes
Téléchargez KB4575898 et [appliquez la mise à jour via l’interface utilisateur web locale](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Références
Vous recherchez une note de version antérieure ? Accédez à :

- [Notes de version de StorSimple Virtual Array Update 1.2](./storsimple-virtual-array-update-12-release-notes.md)
- [Notes de version de StorSimple Virtual Array Update 1.0](./storsimple-virtual-array-update-1-release-notes.md)
- [Notes de version de StorSimple Virtual Array Update 0.6](./storsimple-virtual-array-update-06-release-notes.md)
- [Notes de publication de StorSimple Virtual Array Update 0.5](./storsimple-virtual-array-update-05-release-notes.md)
- [Notes de publication de StorSimple Virtual Array Update 0.4](./storsimple-virtual-array-update-04-release-notes.md)
- [Notes de publication de StorSimple Virtual Array Update 0.3](./storsimple-ova-update-03-release-notes.md)
- [Notes de publication de StorSimple Virtual Array Update 0.1 et 0.2](./storsimple-ova-update-01-release-notes.md)
- [Notes de publication de StorSimple Virtual Array General Availability](./storsimple-virtual-array-update-06-release-notes.md)