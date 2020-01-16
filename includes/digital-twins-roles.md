---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044946"
---
Le tableau suivant décrit les rôles qui sont disponibles dans Azure Digital Twins :

| **Rôle** | **Description** | **Identificateur** |
| --- | --- | --- |
| Administrateur de l’espace | Autorisation *CRÉER*, *LIRE*, *METTRE À JOUR* et *SUPPRIMER* pour l’espace spécifié et tous les nœuds en dessous. Autorisation globale. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrateur d'utilisateurs| Autorisation *CRÉER*, *LIRE*, *METTRE À JOUR* et *SUPPRIMER* pour les utilisateurs et leurs objets connexes. Autorisation *LIRE* pour les espaces. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrateur de l’appareil | Autorisations *CRÉER*, *LIRE*, *METTRE À JOUR* et *SUPPRIMER* pour les appareils et leurs objets connexes. Autorisation *LIRE* pour les espaces. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrateur des clés | Autorisation *CRÉER*, *LIRE*, *METTRE À JOUR* et *SUPPRIMER* pour les clés d’accès. Autorisation *LIRE* pour les espaces. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrateur des jetons |  Autorisation *LIRE* et *METTRE À JOUR* pour les clés d’accès. Autorisation *LIRE* pour les espaces. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Utilisateur |  Autorisation *LIRE* pour les espaces, les capteurs et les utilisateurs, incluant leurs objets connexes. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Spécialiste du support |  Autorisation *LIRE* pour tout sauf pour les clés d’accès. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Installateur des appareils | Autorisation *LIRE* et *METTRE À JOUR* pour les appareils et les capteurs, incluant leurs objets connexes. Autorisation *LIRE* pour les espaces. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Appareil de passerelle | Autorisation *CRÉER* pour les capteurs. Autorisation *LIRE* pour les appareils et les capteurs, incluant leurs objets connexes. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |