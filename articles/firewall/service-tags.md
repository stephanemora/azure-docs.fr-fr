---
title: Vue d'ensemble des balises de service de Pare-feu Azure
description: Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031577"
---
# <a name="azure-firewall-service-tags"></a>Balises de service de Pare-feu Azure

Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne peut pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Les balises de service de Pare-feu Azure peuvent être utilisées dans le champ de destination des règles réseau. Vous pouvez les utiliser à la place d'adresses IP spécifiques.

## <a name="supported-service-tags"></a>Balises de service prises en charge

Consultez la section [Balises de service du réseau virtuel](../virtual-network/service-tags-overview.md#available-service-tags) pour obtenir la liste des balises de service disponibles que vous pouvez utiliser dans les règles de réseau de pare-feu Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les règles du service Pare-feu Azure, consultez [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).
