---
title: Vue d'ensemble des balises de service de Pare-feu Azure
description: Cet article offre une vue d'ensemble des balises de service de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450166"
---
# <a name="azure-firewall-service-tags"></a>Balises de service de Pare-feu Azure

Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne peut pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent.

Les balises de service de Pare-feu Azure peuvent être utilisées dans le champ de destination des règles réseau. Vous pouvez les utiliser à la place d'adresses IP spécifiques.

## <a name="supported-service-tags"></a>Balises de service prises en charge

Consultez la section [Groupes de sécurité](../virtual-network/security-overview.md#service-tags) pour obtenir la liste des balises de service disponibles que vous pouvez utiliser dans les règles de réseau de pare-feu Azure.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les règles du service Pare-feu Azure, consultez [Logique de traitement des règles du service Pare-feu Azure](rule-processing.md).