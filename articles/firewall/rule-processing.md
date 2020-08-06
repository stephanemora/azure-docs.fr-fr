---
title: Logique de traitement des règles du service Pare-feu Azure
description: Le service Pare-feu Azure comporte des règles de traduction d’adresses réseau (NAT), des règles de réseau et des règles d’application. Les règles sont traitées selon leur type.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 84110e749dac9267e994385aa5f6d05e3ba224a6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087541"
---
# <a name="configure-azure-firewall-rules"></a>Configurer des règles de pare-feu Azure
Vous pouvez configurer des règles NAT, des règles de réseau et des règles d'application sur Pare-feu Azure. Les collections de règles sont traitées en fonction du type de règle par ordre de priorité, des nombres inférieurs aux nombres supérieurs, compris entre 100 et 65 000. Un nom de règle de collection peut contenir uniquement des lettres, des chiffres, des traits de soulignement, des points ou des traits d’union. Il doit commencer par une lettre ou un chiffre et se terminer par une lettre, un chiffre ou un trait de soulignement. La longueur maximale du nom est limitée à 80 caractères.

Le mieux est de commencer par espacer les numéros de priorité de la collection de règles par incréments de 100 (100, 200, 300, etc.). Vous avez ainsi de l’espace pour ajouter d’autres collections de règles si nécessaire.

> [!NOTE]
> Si vous activez le filtrage basé sur le renseignement sur les menaces (Threat Intelligence), ces règles sont prioritaires et sont toujours traitées en premier. Le filtrage basé sur le renseignement sur les menaces peut refuser le trafic avant que les règles configurées ne soient traitées. Pour plus d’informations, voir [Fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure](threat-intel.md).

## <a name="outbound-connectivity"></a>Connectivité sortante

### <a name="network-rules-and-applications-rules"></a>Règles de réseau et règles d’application

Si vous configurez des règles de réseau et des règles d'application, les règles de réseau sont appliquées par ordre de priorité avant les règles d'application. Ce processus prend fin dès qu’une règle est exécutée. Ainsi, si une correspondance est trouvée dans une règle de réseau, aucune autre règle n'est traitée.  En l’absence de correspondance avec les règles de réseau, si le protocole est HTTP, HTTPS ou MSSQL, le paquet est évalué par les règles d’application par ordre de priorité. Si aucune correspondance n’est trouvée, le paquet est évalué par rapport à la [collection de règles de l’infrastructure](infrastructure-fqdns.md). S’il n’existe toujours pas de correspondance, le paquet est refusé par défaut.

## <a name="inbound-connectivity"></a>Connectivité entrante

### <a name="nat-rules"></a>Règles NAT

Vous pouvez activer la connectivité Internet entrante en configurant le mode DNAT (Destination Network Address Translation), comme décrit dans l’article [Tutoriel : Filtrer le trafic entrant avec pare-feu Azure DNAT via le portail Azure](tutorial-firewall-dnat.md). Les règles NAT sont appliquées en priorité par rapport aux règles de réseau. Si une correspondance est trouvée, une règle de réseau correspondante implicite autorisant le trafic traduit est ajoutée. Vous pouvez remplacer ce comportement en ajoutant explicitement une collection de règles de réseau avec des règles de refus correspondant au trafic traduit.

Aucune règle d’application n’est appliquée pour les connexions entrantes. Si vous souhaitez filtrer le trafic HTTP/S entrant, vous devez donc utiliser Web Application Firewall (WAF). Pour plus d’informations, consultez [Présentation du pare-feu d’applications web](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemples

Les exemples suivants montrent les résultats de certaines de ces combinaisons de règles.

### <a name="example-1"></a>Exemple 1

La connexion à google.com est autorisée en raison d'une règle de réseau correspondante.

**Règle de réseau**

- Action : Allow


|name  |Protocol  |Type de source  |Source  |Type de destination  |Adresse de destination  |Ports de destination|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|Adresse IP|*|Adresse IP|*|80,443

**Règle d’application**

- Action : Deny

|name  |Type de source  |Source  |Protocole:Port|Noms de domaine complets cibles|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Adresse IP|*|http:80,https:443|google.com

**Résultat**

La connexion à google.com est autorisée, car le paquet correspond à la règle de réseau *Allow-web*. Le traitement des règles s’arrête à ce stade.

### <a name="example-2"></a>Exemple 2

Le trafic SSH est refusé, car une collection de règles de réseau *Deny* de priorité supérieure le bloque.

**Collection de règles de réseau 1**

- Nom : Allow-collection
- Priorité : 200
- Action : Allow

|name  |Protocol  |Type de source  |Source  |Type de destination  |Adresse de destination  |Ports de destination|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|Adresse IP|*|Adresse IP|*|22

**Collection de règles de réseau 2**

- Nom : Deny-collection
- Priorité : 100
- Action : Deny

|name  |Protocol  |Type de source  |Source  |Type de destination  |Adresse de destination  |Ports de destination|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Adresse IP|*|Adresse IP|*|22

**Résultat**

Les connexions SSH sont refusées, car une collection de règles de réseau de priorité plus élevée les bloque. Le traitement des règles s’arrête à ce stade.

## <a name="rule-changes"></a>Modification de la règle

Si vous modifiez une règle de façon à refuser du trafic jusqu’ici autorisé, toutes les sessions existantes concernées sont supprimées.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).