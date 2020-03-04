---
title: Fichier include
description: Fichier include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597798"
---
Azure Files offre deux niveaux de stockage, Premium et Standard, pour vous permettre de personnaliser vos partages de fichiers en fonction des exigences de performances et de budget de votre scénario :

- **Partages de fichiers Premium** : Les partages de fichiers Premium sont sauvegardés sur des disques SSD et déployés dans le type **Compte de stockage FileStorage**. Les partages de fichiers Premium offrent de façon constante des performances élevées et une faible latence (à un chiffre en millisecondes pour la plupart des opérations d’E/S) pour les charges de travail gourmandes en E/S. Ils sont ainsi adaptés à un vaste éventail de charges de travail, telles que les bases de données, l’hébergement de site web et les environnements de développement. Les partages de fichiers Premium sont disponibles uniquement dans le cadre d’un modèle de facturation avec approvisionnement. Pour plus d’informations sur le modèle de facturation avec approvisionnement pour les partages de fichiers Premium, consultez [Comprendre l’approvisionnement des partages de fichiers Premium](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares).
- **Partages de fichiers Standard** : Les partages de fichiers Standard sont sauvegardés sur des lecteurs de disque dur (HDD) et déployés dans le type **Compte de stockage v2 universel (GPv2)** . Les partages de fichiers Standard offrent des performances fiables pour les charges de travail d’E/S moins sensibles à la variabilité des performances, telles que les partages de fichiers à usage général et les environnements de Dev/Test. Les partages de fichiers standard sont disponibles uniquement dans le cadre d’un modèle de facturation avec paiement à l’utilisation.