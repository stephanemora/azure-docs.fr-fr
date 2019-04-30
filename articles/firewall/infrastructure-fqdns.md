---
title: Nom de domaine complet d’infrastructure pour le Pare-feu Azure
description: Obtenez des informations sur les noms de domaine complets d’infrastructure dans le Pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61066319"
---
# <a name="infrastructure-fqdns"></a>Noms de domaine complets d’infrastructure

Le Pare-feu Azure comprend un regroupement de règles intégré pour les noms de domaine complets d’infrastructure qui sont autorisés par défaut. Ces noms de domaine complets sont spécifiques à la plateforme et ne peuvent pas être utilisés à d’autres fins. 

Les services suivants sont inclus dans le regroupement de règles intégré :

- Accès Compute au référentiel d’images de la plateforme (PIR) de stockage
- Accès de stockage de l’état des disques managés
- Diagnostics Azure et journalisation (MDS)
- Azure Active Directory

## <a name="overriding"></a>Remplacement 

Vous pouvez remplacer ce regroupement de règles d’infrastructure intégré en créant un regroupement de règles d’application Refuser tout qui sera traité en dernier. Il sera toujours traité avant le groupe de règles d’infrastructure. Tout ce qui ne se trouve pas dans le regroupement de règles d’infrastructure est refusé par défaut.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).