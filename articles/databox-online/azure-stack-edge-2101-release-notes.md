---
title: Notes de publication d’Azure Stack Edge Pro avec FPGA 2101 | Microsoft Docs
description: Décrit les problèmes majeurs existants et les résolutions possibles pour la version 2101 d’Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727547"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Notes de publication d’Azure Stack Edge Pro avec FPGA 2101

Les notes de publication suivantes identifient les problèmes critiques, résolus ou non, de la version 2101 d’Azure Stack Edge Pro avec un field programmable gate array (FPGA) intégré.

Les notes de publication sont constamment mises à jour. Les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Avant de déployer votre appareil Azure Stack Edge, lisez attentivement les informations contenues dans les notes de publication.  

Cette version correspond à la version logicielle :

- **Azure Stack Edge 2101 (1.6.1475.2528)** – KB 4599267

> [!NOTE]
> La mise à jour 2101 ne peut être appliquée qu’aux appareils disposant de versions du logiciel en disponibilité générale ou ultérieures.

## <a name="whats-new"></a>Nouveautés

Cette version contient le correctif de bogue suivant :

- **Charger le problème** : cette version résout un problème de chargement, où les redémarrages du chargement causés par une défaillance ralentissaient l’exécution du chargement. Ce problème est susceptible de se produire lors du chargement d’un jeu de données constitué principalement de fichiers volumineux par rapport à la bande passante disponible, notamment lorsque la limitation de bande passante est active. Cette modification donne au chargement la possibilité de se terminer avant qu’il soit redémarré pour un fichier donné.

Cette version contient également les mises à jour suivantes :

- Toutes les mises à jour cumulatives de Windows et de .NET Framework publiées jusqu’en octobre 2020.
- La version du microprogramme du contrôleur de gestion de la carte de base (BMC) est mise à niveau de 3.32.32.32 vers 3.36.36.36 lors de l’installation en usine pour résoudre les problèmes d’incompatibilité avec les unités d’alimentation électrique Dell plus récentes.
- Cette version prend en charge IoT Edge 1.0.9.3 sur les appareils Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problèmes connus dans cette version

Aucun nouveau problème n’est indiqué dans les notes de publication de cette version. Tous les problèmes mentionnés dans les notes de publication proviennent des versions précédentes. Pour afficher la liste des problèmes connus, accédez à [Problèmes connus dans la version mise à la disposition générale](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)