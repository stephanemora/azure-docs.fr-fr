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
ms.openlocfilehash: b0b3b65ee2360a5cec32235f2ee5bf8d4839cc8b
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342075"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Exemples de modèles de stratégie Azure pour un réseau virtuel

Le tableau suivant inclut des liens vers des exemples d’[Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ces exemples se trouvent dans le [référentiel d’exemples d’Azure Policy](https://github.com/Azure/azure-policy).

| | |
|---|---|
|**Réseau**||
| [NSG X sur chaque carte réseau](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque interface de réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [NSG X sur chaque sou-réseau](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige qu’un groupe de sécurité réseau spécifique soit utilisé avec chaque sous-réseau virtuel. Spécifiez l’identifiant du groupe de sécurité réseau à utiliser. |
| [Pas de table de routage](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Interdit le déploiement de réseaux virtuels avec une table de routage. |
| [Utiliser un sous-réseau approuvé pour les interfaces réseau de machine virtuelle](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que les interfaces réseau utilisent un sous-réseau approuvé. Spécifiez l’identifiant du sous-réseau approuvé. |
| [Utiliser un réseau virtuel approuvé pour les interfaces réseau de machine virtuelle](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que les interfaces réseau utilisent un réseau virtuel approuvé. Spécifiez l’identifiant du réseau virtuel approuvé. |
|**Surveillance**||
| [Auditer le paramètre de diagnostic](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifie si les paramètres de diagnostic ne sont pas activés pour des types de ressources spécifiés. Spécifiez un tableau de types de ressources pour vérifier si les paramètres de diagnostic sont activés. |
|**Conventions d’affectation de noms et de texte**||
| [Autoriser plusieurs modèles de nom](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Permettre à un des nombreux modèles de nom d’être utilisé pour les ressources. |
| [Exiger le modèle de similitude](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifier que les noms de ressource remplissent la condition *de similitude* d’un modèle. |
| [Exiger le modèle de correspondance](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifier que les noms de ressource correspondent à un modèle d’affectation de noms spécifié. |
| [Exiger le modèle de correspondance de balise](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Vérifier qu’une valeur de balise correspond à un modèle de texte. |
|**Balises**||
| [Initiative de la stratégie de facturation en fonction d’étiquettes](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige des valeurs de balise spécifiées pour le nom de produit et le centre de coût. Utilise des stratégies intégrées pour appliquer et imposer les balises nécessaires. Spécifiez les valeurs nécessaires pour les balises.  |
| [Imposer une balise et sa valeur sur les groupes de ressources](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige une balise et une valeur sur un groupe de ressources. Spécifiez les nom et valeur de balise nécessaires.  |
| [Imposer une balise et sa valeur](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige un nom et une valeur de balise spécifiée. Spécifiez les nom et valeur de balise à imposer.  |
| [Appliquer la balise et sa valeur par défaut](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Ajoute le nom et la valeur d’une balise spécifiée, si cette balise n’est pas fournie. Spécifiez les nom et valeur de balise à appliquer.  |
|**Généralités**||
| [Emplacements autorisés](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Exige que toutes les ressources soient déployées sur les emplacements approuvés. Spécifiez un tableau d’emplacements approuvées.  |
| [Types de ressources autorisés](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Garantit que seuls les types de ressources approuvés sont déployés. Spécifiez un tableau de types de ressources qui sont autorisés.  |
| [Types de ressources non autorisés](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Empêche le déploiement de types de ressources spécifiés. Spécifiez un tableau des types de ressources à bloquer.  |