---
author: baanders
description: Fichier include pour la vue d’ensemble des étapes dans le programme d’installation d’Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205481"
---
>[!NOTE]
>Ces opérations sont destinées à être effectuées par un utilisateur ayant le pouvoir de gérer à la fois les ressources et l’accès des utilisateurs sur l’abonnement Azure. Bien que certaines étapes puissent être effectuées avec des autorisations inférieures, la collaboration d’une personne disposant de ces autorisations sera nécessaire pour configurer complètement une instance utilisable. Pour plus d’informations à ce sujet, consultez la section [*Conditions préalables : autorisations requises*](#prerequisites-permission-requirements) section ci-dessous.

La configuration complète d’une nouvelle instance Azure Digital Twins se déroule en deux phases :
1. **Création de l’instance**
2. **Configuration d’autorisations d’accès utilisateur**  : les utilisateurs Azure doivent avoir le rôle *Propriétaire Azure Digital Twins (préversion)* sur l’instance Azure Digital Twins pour pouvoir gérer celle-ci et ses données. Au cours de cette étape, en tant que Propriétaire/Administrateur de l’abonnement Azure, vous allez attribuer ce rôle à la personne qui doit gérer votre instance Azure Digital Twins. Il peut s’agir de vous-même ou d’une autre personne au sein de votre organisation.