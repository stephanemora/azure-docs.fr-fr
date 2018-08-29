---
title: Modifications des journaux de flux du groupe de sécurité réseau Azure | Microsoft Docs
description: En savoir plus sur les modifications des journaux de flux du groupe de sécurité réseau Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180469"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>Journaux de flux du groupe de sécurité réseau -Version 2 – Modifications à venir

Les formats des journaux de flux du groupe de sécurité réseau changeront à partir du 15 septembre 2018. Des champs supplémentaires vous fourniront des informations sur l’état des flux ainsi que sur le nombre incrémentiel d’octets et de paquets transférés dans chaque direction. Les applications qui analysent les journaux de flux seront affectées par cette modification et doivent être mis à jour en conséquence. Cet article décrit les détails de la modification.

## <a name="what-is-changing"></a>Qu’est-ce qui change ?

La version des journaux de flux de groupe de sécurité réseau passera de « Version » : 1 à « Version » : 2, avec des champs supplémentaires ajoutés à la propriété *flowTuples* dans les journaux. Des détails sur le format sont fournis dans [How a flow is modeled in version 2](#how-is-a-flow-modeled-in-version-2) (Modélisation d’un flux dans la version 2).

### <a name="version-1-flow-tuple-format"></a>Format du tuple du flux de la version 1

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>Format du tuple du flux de la version 2

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>Quand cette modification aura-t-elle lieu ?

Cette modification prendra effet à partir du **15 septembre 2018** dans la région USA Centre-Ouest. Le déploiement de la modification dans les régions de cloud public se terminera le 31 novembre 2018, après quoi seulement des journaux de la version 2 seront disponibles.

## <a name="am-i-affected-by-the-change"></a>Suis-je concerné par la modification ?

Vous pouvez être affecté par cette modification, si vous générez ou utilisez une application qui traite les données du journal de flux du groupe de sécurité réseau.

### <a name="if-i-use-traffic-analytics"></a>Et si j’utilise Traffic Analytics ?

Non. Traffic Analytics ne sera pas affecté par le passage de la version 1 à la version 2 de la journalisation des flux. Les prochaines améliorations tireront parti des informations supplémentaires sur la session et la bande passante fournies dans les journaux de flux de la version 2.

### <a name="if-im-using-third-party-tooling"></a>Et si j’utilise des outils de tiers ?

Le changement de format du journal a été communiqué à l’avance à tous les [partenaires Network Watcher](https://azure.microsoft.com/services/network-watcher). Contactez votre fournisseur pour plus d’informations.

### <a name="if-i-have-built-a-custom-application-or-integration"></a>Et si j’ai créé une application ou une intégration personnalisée ?

**Oui**, vous devrez modifier votre application pour traiter les journaux de flux NSG au nouveau format.

## <a name="what-is-the-new-flow-logging-format"></a>Qu’est-ce que le nouveau format de journalisation des flux ?

La version 2 des journaux de flux contient des tuples de flux au format suivant :

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

Le tableau suivant fournit des noms et des descriptions de propriétés pour le tuple des flux de la version 2 du groupe de sécurité réseau. Vous trouverez des exemples d’enregistrements complets dans [Événement d’échantillon de la version 2](#version2sampleevent).

| Nom de la propriété                        | Valeur           | Description                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Horodatage                           | 1493763938      | Horodatage correspondant à l’entrée de flux. Le format d’époque UNIX EPOCH est utilisé.                                                                                       |
| Adresse IP source                    | 185.170.185.105 |                                                                                                                                                             |
| Adresse IP de destination               | 10.2.0.4        |                                                                                                                                                             |
| Port source                          | 35370           |                                                                                                                                                             |
| Port de destination                     | 23              |                                                                                                                                                             |
| Protocole du trafic                     | T               | **T** : TCP et **U** : UDP.                                                                                                                                  |
| Direction du flux de trafic               | I               | **I** : trafic entrant et **O** : trafic sortant.                                                                                                         |
| Décision relative au trafic                     | A               | **A** : le flux a été autorisé et **D** : le flux a été refusé.                                                                                                         |
| État du flux                           | C               | **B** : début, lors de la création d’un flux. Aucune statistique n’est fournie. **C** : continuation d’un flux en cours. Des statistiques sont fournies toutes les 5 minutes. **E** : fin, lorsqu’un flux est arrêté. Des statistiques sont fournies.                                                                                                                                                        |
| Paquets : de la source à la destination      | 1               | Le nombre total de paquets TCP et UDP envoyés de la source à la destination depuis la dernière mise à jour.                                                                  |
| Octets envoyés : de la source à la destination   | 103             | Le nombre total d’octets de paquets TCP et UDP envoyés de la source à la destination depuis la dernière mise à jour. Les octets de paquets incluent l’en-tête et la charge utile du paquet.         |
| Paquets envoyés : de la destination à la source | 1               | Le nombre total de paquets TCP et UDP envoyés de la destination à la source depuis la dernière mise à jour.                                                                  |
| Octets envoyés : de la destination à la source   | 186             | Le nombre total d’octets de paquets TCP et UDP envoyés de la destination à la source depuis la dernière mise à jour. Les octets de paquets incluent l’en-tête et la charge utile du paquet.             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>Comment un flux est-il modélisé dans la version 2 ?

La version 2 des journaux présente l’état du flux. L’état du flux *B* est enregistré lorsqu’un flux est lancé. L’état du flux *C* et l’état du flux *E* sont les états qui marquent respectivement la continuation d’un flux et son arrêt. Les deux états *C* et *E* contiennent des informations sur la bande passante du trafic.

**Exemple** : tuples de flux à partir d’une conversation TCP entre 185.170.185.105:35370 and 10.2.0.4:23 :

« 1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,, » « 1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880 » « 1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072 »

### <a name="how-does-the-format-differ-from-version-1"></a>En quoi le format diffère-t-il de la version 1 ?

Dans la version 1, un tuple de flux [« 1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D »] est créé chaque fois qu’il y a établissement ou tentative d’établissement d’un flux (cas de refus).

### <a name="how-are-bytes-and-packets-accounted-for"></a>Comment les octets et les paquets sont-ils pris en compte ?

Pour la continuation *C* et la fin *E* d’états de flux, le nombre d’octets et de paquets est le nombre cumulé depuis l’enregistrement de tuple de flux précédent. Si l’on se réfère à l’exemple de conversation précédent, le nombre total de paquets transférés est de 1 021 + 52 + 8 005 + 47 = 9 125. Le nombre total d’octets transférés est de 588 096+29 952+4 610 880+27 072 = 5 256 000.

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>Si mon application ne comprend pas les champs de la bande passante du trafic, dans quelle mesure la version 2 affecte-t-elle l’application ?

Les applications utilisant la journalisation des flux de groupe de sécurité réseau de la version 1 comptent généralement le nombre de flux uniques. Si aucune modification de l’analyse n’est effectuée pour prendre en compte l’état du flux, vous pouvez voir une augmentation inexacte du nombre de flux rapporté. Le comptage des flux uniques peut être effectué en ignorant les tuples de flux dans les états *C* et *E*.

## <a name="can-i-control-the-version-format-i-receive"></a>Puis-je contrôler le format de version que je reçois ?

Non. L’activation et la désactivation des journaux de flux n’affecteront pas le format de sortie des journaux. Le passage de la version 1 à la version 2 des journaux se fera région par région. Lorsqu’une région est mise à niveau de la version 1 vers la version 2, vous pouvez voir les journaux de flux NSG dans les deux formats. Une fois le déploiement terminé, seuls les journaux de la version 2 seront disponibles.

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>Pendant la modification se produit, puis-je voir les deux formats du même groupe de sécurité réseau ?

Oui. Au sein d’une région, la transition se produit sur une base d’adresse par MAC. Dans la mesure où un groupe de sécurité réseau peut être appliqué à de nombreuses machines, vous pouvez voir les journaux dans les deux formats écrits pour le stockage. Les journaux seront dans la version 1 ou dans la version 2.

## <a name="will-i-see-duplicate-data"></a>Les données s’afficheront-elles en double ?

Il n’y aura pas de duplication des données de journalisation des flux dans les différents formats. Un flux sera enregistré au format de la version 1 ou de la version 2 pendant le changement.

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>Puis-je tester préalablement le nouveau format ?

Oui. Vous pouvez [télécharger](https://aka.ms/nsgflowlogsv2blobsample) un exemple de version 2 du fichier journal de flux et l’utiliser pour tester votre solution.

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>La configuration et la gestion de la journalisation des flux du groupe de sécurité réseau seront-elles modifiées ?

Non. La prise en charge des comptes de stockage Azure dans des abonnements qui partagent le même locataire Azure Active Directory a été [publiée](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/) plus tôt cette année. Cette modification vous permet de réduire le nombre de comptes de stockage nécessaire pour la gestion des journaux de flux des groupes de sécurité réseau.

## <a name="questions-or-feedback"></a>Avez-vous des questions ou des commentaires ?

Nous attendons avec impatience vos commentaires sur votre expérience avec les journaux de flux des groupe de sécurité réseau et avec Network Watcher. Vous pouvez fournir vos commentaires ou suggestions en ligne, ou par e-mail à l’adresse AzureNetworkWatcher@microsoft.com.

## <a name="version-2-sample"></a>Exemple de version 2

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>Exemple de version 1

```json
{ 

    "records": [ 

        { 

            "time": "2017-02-16T22:00:32.8950000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A", 

                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A", 

                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A", 

                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A" 

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        }, 

        { 

            "time": "2017-02-16T22:01:32.8960000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",

                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",

                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        } 

    ] 
}
```