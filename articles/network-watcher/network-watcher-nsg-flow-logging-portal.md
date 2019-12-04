---
title: 'Tutoriel : Journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure'
titleSuffix: Azure Network Watcher
description: Dans ce tutoriel, découvrez comment journaliser le flux du trafic réseau à destination et en provenance d’une machine virtuelle à l’aide de la fonctionnalité des journaux de flux NSG de Network Watcher.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to log the network traffic to and from a VM so I can analyze it for anomalies.
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a5d600e761ce3c3cebbe155c6be7e0f5a377eb32
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419635"
---
# <a name="tutorial-log-network-traffic-to-and-from-a-virtual-machine-using-the-azure-portal"></a>Didacticiel : journaliser le trafic réseau à destination et en provenance d’une machine virtuelle à l’aide du portail Azure

Un groupe de sécurité réseau (NSG) permet de filtrer le trafic entrant vers une machine virtuelle ainsi que le trafic sortant qui en provient. Vous pouvez enregistrer le trafic réseau qui transite par un groupe de sécurité réseau à l’aide de la fonctionnalité des journaux de flux NSG de Network Watcher. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une machine virtuelle avec un groupe de sécurité réseau
> * Activer Network Watcher et inscrire le fournisseur Microsoft.Insights
> * Activer un journal de flux de trafic pour un groupe de sécurité réseau à l’aide de la fonctionnalité des journaux de flux NSG de Network Watcher
> * Télécharger les données enregistrées
> * Afficher les données enregistrées

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Calcul**, puis **Windows Server 2016 Datacenter** ou une version d’**Ubuntu Server**.
3. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Nom|myVm|
    |Nom d'utilisateur| Entrez un nom d’utilisateur de votre choix.|
    |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Sélectionnez votre abonnement.|
    |Groupe de ressources| Sélectionnez **Créer** et entrez **myResourceGroup**.|
    |Location| Sélectionnez **USA Est**.|

4. Choisissez une taille de machine virtuelle, puis cliquez sur **Sélectionner**.
5. Sous **Paramètres**, acceptez toutes les valeurs par défaut, puis cliquez sur **OK**.
6. Sous **Créer** dans le **résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle. Le déploiement de la machine virtuelle ne nécessite que quelques minutes. Attendez la fin du déploiement de la machine virtuelle avant d’effectuer les étapes restantes.

La création de la machine virtuelle ne nécessite que quelques minutes. Ne passez pas aux étapes restantes tant que la création de la machine virtuelle n’est pas terminée. Pendant que le portail crée la machine virtuelle, il crée également un groupe de sécurité réseau nommé **myVm-nsg** et l’associe à l’interface réseau de la machine virtuelle.

## <a name="enable-network-watcher"></a>Activer Network Watcher

Si vous avez déjà un observateur réseau activé dans la région USA Est, passez à l’étape [Inscription du fournisseur Insights](#register-insights-provider).

1. Dans le portail Azure, sélectionnez **Tous les services**. Dans la zone **Filtre**, entrez *Network Watcher*. Quand la mention **Network Watcher** apparaît dans les résultats, sélectionnez-la.
2. Sélectionnez la zone **Régions** pour la développer, puis sélectionnez **...** à droite de la région **USA Est**, comme illustré dans l’image suivante :

    ![Activer Network Watcher](./media/network-watcher-nsg-flow-logging-portal/enable-network-watcher.png)

3. Sélectionnez **Activer Network Watcher**.

## <a name="register-insights-provider"></a>Inscription du fournisseur Insights

L’enregistrement du flux NSG nécessite le fournisseur **Microsoft.Insights**. Pour inscrire le fournisseur, procédez comme suit :

1. En haut à gauche du portail, sélectionnez **Tous les services**. Dans la zone Filtre, entrez *Abonnements*. Quand la mention **Abonnements** apparaît dans les résultats de la recherche, sélectionnez-la.
2. Dans la liste des abonnements, sélectionnez l’abonnement pour lequel vous souhaitez activer le fournisseur.
3. Sous **PARAMÈTRES**, sélectionnez **Fournisseurs de ressources**.
4. Vérifiez que la zone **ÉTAT** correspondant au fournisseur **microsoft.insights** est définie sur **Inscrit**, comme illustré dans l’image qui suit. Si l’état est **Non inscrit**, sélectionnez **Inscrire** à droite du fournisseur.

    ![Inscrire un fournisseur](./media/network-watcher-nsg-flow-logging-portal/register-provider.png)

## <a name="enable-nsg-flow-log"></a>Activer le journal de flux NSG

1. Les données du journal de flux NSG sont écrites dans un compte Stockage Azure. Pour créer un compte Stockage Azure, sélectionnez **+Créer une ressource** en haut à gauche du portail.
2. Sélectionnez **Stockage**, puis **Compte de stockage - blob, fichier, table, file d’attente**.
3. Entrez ou sélectionnez les informations suivantes, acceptez les autres valeurs par défaut, puis sélectionnez **Créer**.

    | Paramètre        | Valeur                                                        |
    | ---            | ---   |
    | Nom           | Contenant 3 à 24 caractères et uniquement des chiffres et des lettres minuscules, il doit être unique dans tous les comptes Stockage Azure.                                                               |
    | Location       | Sélectionnez **USA Est**.                                           |
    | Resource group | Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**. |

    La création du compte de stockage peut prendre environ une minute. Ne passez pas aux étapes restantes tant que la création du compte de stockage n’est pas terminée. Si vous utilisez un compte de stockage existant au lieu d’en créer un, veillez à en sélectionner un pour lequel l’option **Tous les réseaux** (par défaut) est sélectionnée pour **Pare-feux et réseaux virtuels**, sous **PARAMÈTRES** pour le compte de stockage. Dans tous les cas, le compte de stockage doit se trouver dans la même région que le groupe de sécurité réseau (NSG). 
    
    > [!NOTE]
    > Même si les fournisseurs Microsoft.Insight et Microsoft.Network sont actuellement pris en charge en tant que [services Microsoft approuvés pour Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services), les journaux de flux NSG ne sont toujours pas entièrement intégrés. Pour activer la journalisation de flux NSG, vous devez sélectionner **Tous les réseaux** comme indiqué plus haut.
    
4. En haut à gauche du portail, sélectionnez **Tous les services**. Dans la zone **Filtre**, entrez *Network Watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
5. Sous **JOURNAUX D’ACTIVITÉ**, sélectionnez **Journaux de flux NSG**, comme illustré dans l’image suivante :

    ![Groupes de sécurité réseau](./media/network-watcher-nsg-flow-logging-portal/nsgs.png)

6. Dans la liste des groupes de sécurité réseau, sélectionnez le groupe de sécurité réseau nommé **myVm-nsg**.
7. Sous **Paramètres des journaux de flux**, sélectionnez **Activé**.
8. Sélectionnez la version de journalisation des flux. La version 2 contient des statistiques de session des flux (octets et paquets).

   ![Sélectionner la version des journaux de flux](./media/network-watcher-nsg-flow-logging-portal/select-flow-log-version.png)

9. Sélectionnez le compte de stockage que vous avez créé à l’étape 3.
   > [!NOTE]
   > Les journaux de flux NSG ne fonctionnent pas avec les comptes de stockage dans les cas suivants :
   > * Un pare-feu est activé pour les comptes de stockage.
   > * L’[espace de noms hiérarchique](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) est activé pour les comptes de stockage.
1. En haut à gauche du portail, sélectionnez **Tous les services**. Dans la zone **Filtre**, entrez *Network Watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
10. Définissez le paramètre **Rétention (jours)** sur 5, puis sélectionnez **Enregistrer**.
    > [!IMPORTANT]
    > Un problème empêche actuellement la suppression automatique des [journaux de flux de groupe de sécurité réseau](network-watcher-nsg-flow-logging-overview.md) pour Network Watcher du stockage blob en fonction des paramètres de stratégie de rétention. Si vous avez une stratégie de rétention différente de zéro, nous vous recommandons de supprimer régulièrement les blobs de stockage qui ont dépassé leur période de rétention afin d’éviter des frais supplémentaires. Pour plus d’informations sur la suppression des objets blob de stockage du journal de flux NSG, consultez [Supprimer les objets blob de stockage du journal de flux NSG](network-watcher-delete-nsg-flow-log-blobs.md).

## <a name="download-flow-log"></a>Télécharger le journal de flux

1. Dans Network Watcher, dans le portail, sélectionnez **Journaux de flux NSG** sous **JOURNAUX D’ACTIVITÉ**.
2. Sélectionnez **Vous pouvez télécharger les journaux de flux à partir de comptes de stockage configurés** comme illustré sur l’image suivante :

   ![Télécharger des journaux de flux](./media/network-watcher-nsg-flow-logging-portal/download-flow-logs.png)

3. Sélectionnez le compte de stockage que vous avez configuré à l’étape 2 [Activer le journal de flux NSG](#enable-nsg-flow-log).
4. Sous **Service Blob**, sélectionnez **Objets blob**, puis sélectionnez le conteneur **insights-logs-networksecuritygroupflowevent**.
5. Dans le conteneur, parcourez l’arborescence des dossiers jusqu’à accéder au fichier PT1H.json, comme illustré dans l’image suivante : Les fichiers journaux sont écrits dans une arborescence des dossiers qui respecte la convention de nommage suivante : https://{NomCompteStockage}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{IDAbonnement}/RESOURCEGROUPS/{NomGroupeRessources}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{NomNSG}/y={année}/m={mois}/d={jour}/h={heure}/m=00/macAddress={AdresseMac}/PT1H.json

   ![Journal de flux](./media/network-watcher-nsg-flow-logging-portal/log-file.png)

6. Sélectionnez **...** à droite du fichier PT1H.json, puis **Télécharger**.

## <a name="view-flow-log"></a>Afficher le journal de flux

Le texte JSON suivant est un exemple de ce que vous verrez dans le fichier PT1H.json pour chaque flux pour lequel des données sont enregistrées :

### <a name="version-1-flow-log-event"></a>Événement du journal de flux version 1
```json
{
    "time": "2018-05-01T15:00:02.1713710Z",
    "systemId": "<Id>",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/<Id>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MYVM-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 1,
        "flows": [
            {
                "rule": "UserRule_default-allow-rdp",
                "flows": [
                    {
                        "mac": "000D3A170C69",
                        "flowTuples": [
                            "1525186745,192.168.1.4,10.0.0.4,55960,3389,T,I,A"
                        ]
                    }
                ]
            }
        ]
    }
}
```
### <a name="version-2-flow-log-event"></a>Événement du journal de flux version 2
```json
{
    "time": "2018-11-13T12:00:35.3899262Z",
    "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
    "category": "NetworkSecurityGroupFlowEvent",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
    "operationName": "NetworkSecurityGroupFlowEvents",
    "properties": {
        "Version": 2,
        "flows": [
            {
                "rule": "DefaultRule_DenyAllInBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                            "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                            "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                        ]
                    }
                ]
            },
            {
                "rule": "DefaultRule_AllowInternetOutBound",
                "flows": [
                    {
                        "mac": "000D3AF87856",
                        "flowTuples": [
                            "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                            "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                            "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                            "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                        ]
                    }
                ]
            }
        ]
    }
}
```

La valeur de la zone **mac** dans la sortie précédente est l’adresse MAC de l’interface réseau qui a été créée lors de la création de la machine virtuelle. Les informations séparées par des virgules dans **flowTuples** sont les suivantes :

| Exemple de données | Ce que représentent les données   | Explication                                                                              |
| ---          | ---                    | ---                                                                                      |
| 1542110377   | Horodatage             | Indique la date et l’heure du flux au format UNIX EPOCH. Dans l’exemple précédent, la date est convertie au 1 mai 2018 à 14:59:05 (GMT).                                                                                    |
| 10.0.0.4  | Adresse IP source      | Adresse IP source dont provient le flux. 10.0.0.4 est l’adresse IP privée de la machine virtuelle que vous avez créée à l’étape [Créer une machine virtuelle](#create-a-vm).
| 13.67.143.118     | Adresse IP de destination | Adresse IP de destination du flux.                                                                                  |
| 44931        | Port source            | Port source dont provient le flux.                                           |
| 443         | Port de destination       | Port de destination du flux. Comme le trafic était destiné au port 443, la règle nommée **UserRule_default-allow-rdp** dans le fichier journal a traité le flux.                                                |
| T            | Protocol               | Indique si le protocole du flux était TCP (T) ou UDP (U).                                  |
| O            | Direction              | Indique si le trafic était entrant (I) ou sortant (O).                                     |
| A            | Action                 | Indique si le trafic était autorisé (A) ou refusé (D).  
| C            | État du flux **Version 2 uniquement** | Capture l’état du flux. Les états possibles sont **B** : début, lors de la création d’un flux. Aucune statistique n’est fournie. **C** : continuation d’un flux en cours. Des statistiques sont fournies toutes les 5 minutes. **E** : fin, quand un flux est arrêté. Des statistiques sont fournies. |
| 30 | Paquets envoyés - Source vers destination **Version 2 uniquement** | Nombre total de paquets TCP ou UDP envoyés de la source à la destination depuis la dernière mise à jour. |
| 16978 | Octets envoyés - Source vers destination **Version 2 uniquement** | Nombre total d’octets de paquets TCP ou UDP envoyés de la source vers la destination depuis la dernière mise à jour. Les octets de paquets incluent l’en-tête et la charge utile du paquet. |
| 24 | Paquets envoyés - Destination vers source **Version 2 uniquement** | Nombre total de paquets TCP ou UDP envoyés de la destination vers la source depuis la dernière mise à jour. |
| 14008| Octets envoyés - Destination vers source **Version 2 uniquement** | Le nombre total d’octets de paquets TCP et UDP envoyés de la destination à la source depuis la dernière mise à jour. Les octets de paquets incluent l’en-tête et la charge utile du paquet.|

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à activer la journalisation d’un flux de trafic pour un groupe de sécurité réseau. Vous avez également découvert comment télécharger et afficher les données enregistrées dans un fichier. Les données brutes du fichier JSON peuvent être difficiles à interpréter. Pour visualiser les données, vous pouvez utiliser Network Watcher [Traffic Analytics](traffic-analytics.md), Microsoft [PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) et d’autres outils.
