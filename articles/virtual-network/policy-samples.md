---
title: Exemples de modèles de stratégie | Microsoft Docs
description: Exemples de modèles de stratégie Azure pour un réseau virtuel.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779554"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Exemples de modèles de stratégie Azure pour un réseau virtuel

Le tableau suivant contient des liens vers des exemples de modèles [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ces exemples se trouvent dans le [référentiel d’exemples d’Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Réseau**||
| [NSG X sur chaque carte réseau](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque interface de réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [NSG X sur chaque sou-réseau](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque sous-réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Pas de table de routage](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Interdit le déploiement de réseaux virtuels avec une table de routage. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |
|**Surveillance**||
| [Auditer le paramètre de diagnostic](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifie si les paramètres de diagnostic ne sont pas activés pour des types de ressources spécifiés. Spécifiez un tableau de types de ressources pour vérifier si les paramètres de diagnostic sont activés. |
|**Conventions d’affectation de noms et de texte**||
| [Autoriser plusieurs modèles de nom](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Permettre à un des nombreux modèles de nom d’être utilisé pour les ressources. |
| [Exiger le modèle de similitude](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifier que les noms de ressource remplissent la condition *de similitude* d’un modèle. |
| [Exiger le modèle de correspondance](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifier que les noms de ressource correspondent à un modèle d’affectation de noms spécifié. |
| [Exiger le modèle de correspondance de balise](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifier qu’une valeur de balise correspond à un modèle de texte. |
|**Balises**||
| [Initiative de la stratégie de facturation en fonction d’étiquettes](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige des valeurs de balise spécifiées pour le nom de produit et le centre de coût. Utilise des stratégies intégrées pour appliquer et imposer les balises nécessaires. Spécifiez les valeurs nécessaires pour les balises.  |
| [Imposer une balise et sa valeur sur les groupes de ressources](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige une balise et une valeur sur un groupe de ressources. Spécifiez les nom et valeur de balise nécessaires.  |
| [Imposer une balise et sa valeur](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige un nom et une valeur de balise spécifiée. Spécifiez les nom et valeur de balise à imposer.  |
| [Appliquer la balise et sa valeur par défaut](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Ajoute le nom et la valeur d’une balise spécifiée, si cette balise n’est pas fournie. Spécifiez les nom et valeur de balise à appliquer.  |
|**Généralités**||
| [Emplacements autorisés](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que toutes les ressources soient déployées sur les emplacements approuvés. Spécifiez un tableau d’emplacements approuvées.  |
| [Types de ressources autorisés](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garantit que seuls les types de ressources approuvés sont déployés. Spécifiez un tableau de types de ressources qui sont autorisés.  |
| [Types de ressources non autorisés](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Empêche le déploiement de types de ressources spécifiés. Spécifiez un tableau des types de ressources à bloquer.  |