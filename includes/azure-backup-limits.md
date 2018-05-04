---
title: Fichier Include
description: Fichier Include
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
---
Les limites suivantes s’appliquent à la sauvegarde Azure.

| Identificateur de la limite | Limite par défaut |
| --- | --- |
| Nombre de serveurs/machines pouvant être enregistrés pour chaque coffre |50 pour Windows Server/Client/SCDPM  <br/> 1 000 pour les machines virtuelles IaaS |
| Taille d’une source de données pour les données stockées dans le stockage de l’archivage Azure |54 400 Go max<sup>1</sup> |
| Nombre de coffres de sauvegarde pouvant être créés dans chaque abonnement Azure |500 coffres Recovery Services par région |
| Nombre de sauvegardes pouvant être planifiées par jour |3 par jour pour Windows Server/Client  <br/> 2 par jour pour SCDPM <br/> Une fois par jour pour les machines virtuelles IaaS |
| Disques de données attachés à une machine virtuelle Azure pour la sauvegarde |16 |
| Taille du disque de données attaché à une machine virtuelle Azure pour la sauvegarde| 4095 Go <sup>2</sup>|

* <sup>1</sup>La limite de 54 400 Go ne s’applique pas à la sauvegarde de la machine virtuelle IaaS.
 

