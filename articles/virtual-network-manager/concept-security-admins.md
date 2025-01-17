---
title: Règles d’administration de sécurité dans Azure Virtual Network Manager (préversion)
description: Apprenez-en davantage sur les règles d’administration de sécurité dans Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c56df99e68dc40886f01a65fe101fa142a04c43e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097512"
---
# <a name="security-admin-rules-in-azure-virtual-network-manager-preview"></a>Règles d’administration de sécurité dans Azure Virtual Network Manager (préversion)

Azure Virtual Network Manager fournit deux types différents de configurations que vous pouvez déployer dans vos réseaux virtuels. L’un d’eux est une configuration *SecurityAdmin*. Une configuration d’administration de sécurité contient un ensemble de regroupements de règles. Chaque regroupement de règles contient une ou plusieurs règles d’administration de sécurité. Vous associez ensuite le regroupement de règles aux groupes de réseaux auxquels vous souhaitez appliquer les règles d’administration de sécurité.

> [!IMPORTANT]
> Azure Virtual Network Manager est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="security-admin-rules"></a>Règles d’administration de sécurité

Une règle d’administration de sécurité vous permet d’appliquer des critères de stratégie de sécurité correspondant aux conditions définies. Vous ne pouvez définir des règles d’administration de sécurité que pour des ressources au sein de l’étendue de l’instance Azure Virtual Network Manager. Ces règles de sécurité ont une priorité plus élevée que les règles de groupe de sécurité réseau (NSG) et seront évaluées avant les règles NSG. Par exemple, un administrateur peut refuser tous les ports ou protocoles à haut risque en provenance d’Internet avec des règles d’administration de sécurité, et ces règles remplacent les règles de groupe de sécurité réseau autorisées créées au niveau de la machine virtuelle ou du sous-réseau.

> [!IMPORTANT]
> Certains services ont des stratégies d’intention de réseau pour s’assurer que le trafic réseau fonctionne conformément aux besoins de leurs services. Lorsque vous utilisez des règles d’administration de sécurité, vous pouvez rompre les stratégies d’intention de réseau créées pour ces services. Par exemple, la création d’une règle d’administration de refus peut bloquer du trafic autorisé par le service *SQL Managed Instance*, qui est défini par sa stratégie d’intention réseau. Veillez à passer en revue votre environnement avant d’appliquer une configuration d’administration de sécurité. 

Voici les champs que vous pouvez définir dans une règle d’administration de sécurité :

## <a name="required-fields"></a>Champs obligatoires

### <a name="priority"></a>Priority

La priorité de la règle de sécurité est déterminée par un entier compris entre 0 et 99. Plus la valeur est faible, plus la priorité de la règle est élevée. Par exemple, une règle de refus avec de priorité 10 remplace une règle d’autorisation de priorité 20. 

### <a name="action"></a><a name = "action"></a>Action

Vous pouvez définir l’une des trois actions suivantes pour une règle de sécurité :

* **Autoriser** : autoriser le trafic sur le port, le protocole et les préfixes IP source ou de destination spécifiques dans la direction spécifiée.
* **Refuser** : bloquer le trafic sur le port, le protocole et les préfixes IP source ou de destination spécifiés dans la direction spécifiée.
* **Toujours autoriser** : indépendamment des autres règles avec une priorité inférieure ou des groupes de sécurité réseau définis par l’utilisateur, autoriser le trafic sur le port, le protocole et les préfixes IP source/de destination spécifiés dans la direction spécifiée.

### <a name="direction"></a>Sens

Vous pouvez spécifier le direction du trafic auquel s’applique la règle. Vous pouvez définir le trafic entrant ou sortant.

### <a name="protocol"></a>Protocol

Les protocoles actuellement pris en charge par les règles d’administration de sécurité sont les suivants :

* TCP
* UDP
* ICMP
* ESP
* AH
* Tous les protocoles

## <a name="optional-fields"></a>Champs facultatifs

### <a name="source-and-destination-types"></a>Types de sources et de destinations

* **Adresses IP** : vous pouvez fournir des adresses IPv4 ou IPv6, ou des blocs d’adresses en notation CIDR. Pour répertorier plusieurs adresses IP, séparez chaque adresse IP par une virgule.
* **Balise de service** : vous pouvez définir des étiquettes de service spécifiques en fonction de régions ou d’un service entier. Pour obtenir la liste des étiquettes prises en charge, consultez [Étiquettes de service disponibles](../virtual-network/service-tags-overview.md#available-service-tags).

### <a name="source-and-destination-ports"></a>Ports source et de destination

Vous pouvez définir des ports communs spécifiques à bloquer à partir de la source ou vers la destination. Voici une liste des ports TCP courants :

| Ports | Nom du service |
| ----- | ------------ |
| 20, 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |

## <a name="next-steps"></a>Étapes suivantes 

Découvrez comment bloquer le trafic réseau avec une [Configuration de l’administration de la sécurité](how-to-block-network-traffic-portal.md).
