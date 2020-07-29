---
title: Notes de publication d’Azure Stack Edge et d’Azure Data Box Gateway 2007 | Microsoft Docs
description: Décrit les problèmes majeurs existants et les résolutions pour la version 2007 d’Azure Stack Edge et de Data Box Gateway.
services: databox
author: priestlg
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 07/14/2020
ms.author: v-grpr
ms.openlocfilehash: 4964454a5636120840fbdb5f3fbdd3aee30d8e74
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528020"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Notes de publication d’Azure Stack Edge et d’Azure Data Box Gateway 2007

Les notes de publication suivantes identifient les problèmes majeurs existants et les problèmes résolus dans la version 2007 d’Azure Stack Edge et de Data Box Gateway.

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez attentivement les informations contenues dans les notes de publication avant de déployer votre Azure Stack Edge/Data Box Gateway.

Cette version correspond aux versions logicielles :

- **Azure Stack Edge 2007 (1.6.1280.1667)** – KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** – KB 4566550

> [!NOTE]
> La mise à jour 2007 ne peut être appliquée qu’aux appareils disposant de versions du logiciel en disponibilité générale ou ultérieures.

## <a name="whats-new"></a>Nouveautés

Cette version contient le correctif de bogue suivant :

- **Charger le problème** : cette version résout un problème de chargement selon lequel les redémarrages du chargement, causés par des défaillances, ralentissaient l’exécution du chargement. Ce problème était susceptible de se produire lors du chargement d’un jeu de données constitué principalement de fichiers de taille importante par rapport à la bande passante disponible, en particulier lorsque la limitation de bande passante était active. Cette modification donne au chargement la possibilité de se terminer avant qu’il soit redémarré pour un fichier donné.

Cette version contient également les mises à jour suivantes :

- L’image de base du disque dur virtuel Windows a été mise à jour.
- Toutes les mises à jour cumulatives de Windows et de .NET Framework publiées jusqu’en mai 2020 sont incluses.
- Cette version prend en charge IoT Edge 1.0.9.3 sur les appareils Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problèmes connus dans cette version

Aucun nouveau problème n’est indiqué dans les notes de publication de cette version. Tous les problèmes mentionnés dans les notes de publication proviennent des versions précédentes. Pour afficher la liste des problèmes connus, accédez à [Problèmes connus dans la version mise à la disposition générale](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Stack Edge](data-box-edge-deploy-prep.md)
- [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
