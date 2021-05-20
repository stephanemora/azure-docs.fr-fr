---
author: baanders
description: Fichier include pour la vue d’ensemble des étapes dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 19d7c55a669a3975cb4bc1dce707e2d1165be0b3
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108759919"
---
La configuration complète d’une nouvelle instance Azure Digital Twins se déroule en deux phases :
1. **Création de l’instance**
2. **Configuration d’autorisations d’accès utilisateur** : les utilisateurs Azure doivent avoir le rôle **Propriétaire de données Azure Digital Twins** sur l’instance Azure Digital Twins pour pouvoir gérer celle-ci et ses données. Au cours de cette étape, en tant que Propriétaire/Administrateur de l’abonnement Azure, vous allez attribuer ce rôle à la personne qui doit gérer votre instance Azure Digital Twins. Il peut s’agir de vous-même ou d’une autre personne au sein de votre organisation.
 
>[!IMPORTANT]
>Pour terminer cet article et configurer complètement une instance utilisable, vous devez disposer des autorisations nécessaires pour gérer les ressources et l’accès utilisateur sur l’abonnement Azure. La première étape peut être effectuée par toute personne capable de créer des ressources sur l’abonnement, mais la deuxième étape nécessite des autorisations de gestion des accès utilisateur (ou la collaboration d’une personne disposant de ces autorisations). Pour en savoir plus sur l’étape concernant les autorisations d’accès utilisateur, consultez la [section sur les autorisations nécessaires comme prérequis](#prerequisites-permission-requirements).
