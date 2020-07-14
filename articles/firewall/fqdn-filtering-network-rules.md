---
title: Filtrage de nom de domaine complet Pare-feu Azure dans les règles de réseau (préversion)
description: Utiliser le filtrage de nom de domaine complet Pare-feu Azure dans des règles de réseau
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: aef6fb52aaad3dd20db63f1d9023745169e84fcb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567883"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtrage de nom de domaine complet dans les règles de réseau (préversion)

> [!IMPORTANT]
> Le filtrage de nom de domaine complet dans les règles de réseau est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un nom de domaine complet (FQDN) représente le nom de domaine d’un hôte. Un nom de domaine est associé à une ou plusieurs adresses IP. Vous pouvez autoriser ou bloquer les noms de domaine complet et les balises de nom de domaine complet dans les règles d’application. À l’aide des paramètres de proxy DNS et DNS personnalisés, vous pouvez également utiliser le filtrage de nom de domaine complet dans les règles de réseau.

## <a name="how-it-works"></a>Fonctionnement

Le Pare-feu Azure convertit le nom de domaine complet en adresse(s) IP à l’aide de ses paramètres DNS et effectue le traitement des règles en fonction d’Azure DNS ou d’une configuration DNS personnalisée.

Pour utiliser des noms de domaine complet (FQDN) dans des règles de réseau, vous devez activer le proxy DNS. Si vous n’activez pas le proxy DNS, la fiabilité du traitement des règles est menacée. Lorsqu’il est activé, le trafic DNS est dirigé vers le Pare-feu Azure, où vous pouvez configurer votre serveur DNS personnalisé. Ensuite, le pare-feu et les clients utilisent le même serveur DNS configuré. Si le proxy DNS n’est pas activé, le Pare-feu Azure peut produire une réponse différente, car le client et le pare-feu peuvent utiliser des serveurs différents pour la résolution de noms. Le filtrage de nom de domaine complet dans les règles de réseau peut être défectueux ou incohérent si le client et le pare-feu reçoivent des réponses DNS différentes.

Vous pouvez choisir de remplacer cette exigence en reconnaissant le risque avant de sélectionner **Enregistrer** dans le regroupement de règles.

## <a name="next-steps"></a>Étapes suivantes

[Paramètres DNS du Pare-feu Azure](dns-settings.md)
