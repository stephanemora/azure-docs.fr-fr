---
title: Notes de version de Microsoft Azure StorSimple Virtual Array Update 1.3 | Microsoft Docs
description: Décrit les problèmes critiques non résolus d’Azure StorSimple Virtual Array Update 1.3 et fournit des solutions.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 02611bdf9689d2f62f661f558fd547ea46bd4d36
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743959"
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

Cette mise à jour contient les améliorations suivantes :

- Transport Layer Security (TLS) 1.2 est une mise à jour obligatoire et doit être installée. À partir de cette version, TLS 1.2 devient le protocole standard pour toutes les communications du Portail Azure.
- Les correctifs de bogues de nettoyage de la mémoire (garbage collection) améliorent les performances du cycle de nettoyage de la mémoire quand l’appareil et le compte de stockage se trouvent dans deux régions distantes.
- Correction des échecs de sauvegarde en raison de délais d’attente d’objets blob.
- Mises à jour des correctifs de sécurité du système d’exploitation/.NET Framework :
  - [KB4540725](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4540725\V1.001\free\NEU\X64) : Mise à jour de la pile de maintenance (SSU) de mars 2020
  - [KB4565541](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Windows6.3\RTM\KB4565541\V1.014\free\NEU\X64) : Correctif cumulatif de juillet 2020
  - [KB4565622](\\winsehotfix.segroup.winse.corp.microsoft.com\hotfixes\Partner\DOTNET47x\KB4565622\V1.000\free\NEU\x64) : Mise à jour .NET Framework de juillet 2020

## <a name="download-update-13"></a>Télécharger Update 1.3

Pour télécharger cette mise à jour, accédez au [ Catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx), puis téléchargez le package KB4575898. Ce package contient les packages suivants :

- **KB4540725**, qui contient des mises à jour cumulatives pour Windows 2012 R2, jusqu’au mois de mars 2020. Pour plus d’informations sur le contenu de ce correctif cumulatif, consultez [Correctif cumulatif mensuel du mois de mars](https://support.microsoft.com/help/4540725).
- **KB4565541**, qui contient des mises à jour cumulatives pour Windows 2012 R2, jusqu’au mois de juillet 2020. Pour plus d’informations sur le contenu de ce correctif cumulatif, consultez [Correctif cumulatif mensuel du mois de février](https://support.microsoft.com/help/4565541).
- **KB4565622**, qui contient les mises à jour cumulatives .NET Framework jusqu’au mois de juillet 2020. Pour plus d’informations sur le contenu de ce correctif cumulatif, consultez [Correctif cumulatif mensuel du mois de février](https://support.microsoft.com/help/4565622).
- **KB3011067**, qui contient les mises à jour du logiciel de l’appareil.

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
- [Notes de publication de StorSimple Virtual Array General Availability](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)