---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b28830ac2634ad2238d834238de83c9184bcd6f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010389"
---
# <a name="enabling-azure-ultra-ssds"></a>Activation d’Azure Ultra SSD

Azure Ultra SSD fournit un stockage de disque avec un haut débit, un nombre d’IOPS élevé et une faible latence homogène pour les machines virtuelles IaaS Azure. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Entre autres avantages, Ultra SSD permet de changer dynamiquement les performances de disque en fonction de vos charges de travail sans avoir à redémarrer les machines virtuelles. L’offre Ultra SSD est adaptée aux charges de travail qui consomment beaucoup de données comme SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions.

Ultra SSD est en préversion et vous devez vous y [inscrire](https://aka.ms/UltraSSDPreviewSignUp) pour y accéder.

Une fois approuvé, exécutez l’une des commandes suivantes pour déterminer dans quelle zone de la région USA Est 2 vous devez déployer votre disque Ultra SSD :

PowerShell : `Get-AzureRmComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

Interface CLI : `az vm list-skus --resource-type disks --query “[?name==’UltraSSD_LRS’]”`

La réponse est similaire au formulaire ci-dessous, où X correspond à la zone à choisir pour le déploiement dans la région USA Est 2. X peut être 1, 2 ou 3.

|ResourceType  |NOM  |Lieu  |Zones  |Restriction  |Fonctionnalité  |Valeur  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

Si aucune réponse n’a été reçue de la commande, cela signifie que votre inscription à la préversion est toujours en attente ou qu’elle n’a pas encore été approuvée.

Maintenant que vous connaissez la zone dans laquelle effectuer le déploiement, suivez les étapes de déploiement de cet article pour déployer vos premières machines virtuelles avec des disques Ultra SSD.

## <a name="deploying-an-ultra-ssd"></a>Déploiement d’un disque Ultra SSD

Tout d’abord, déterminez la taille de la machine virtuelle à déployer. Dans le cadre de cette préversion, uniquement les familles de machines virtuelles DsV3 et EsV3 sont prises en charge. Pour plus d’informations sur les tailles de machine virtuelle, reportez-vous au deuxième tableau de ce [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).
Reportez-vous également à l’exemple de [création d’une machine virtuelle avec plusieurs disques Ultra SSD](https://aka.ms/UltraSSDTemplate).

Les éléments suivants décrivent les nouveautés et les modifications du modèle Resource Manager : **apiVersion** pour `Microsoft.Compute/virtualMachines` et `Microsoft.Compute/Disks` doit être défini sur `2018-06-01` (ou version ultérieure).

Spécifiez la référence SKU UltraSSD_LRS, la capacité du disque, les IOPS et le débit en Mbits/s pour créer un disque Ultra SSD. Voici un exemple qui crée un disque de 1 024 Go (Gio = 2^30 octets), 80 000 IOPS et 1 200 Mbits/s (Mbits/s = 10^6 octets par seconde) :

```json
"properties": {  
    "creationData": {  
    "createOption": "Empty"  
},  
"diskSizeGB": 1024,  
"diskIOPSReadWrite": 80000,  
"diskMBpsReadWrite": 1200,  
}
```

Ajoutez une fonctionnalité supplémentaire dans les propriétés de la machine virtuelle pour indiquer si Ultra SSD est activé (reportez-vous à [cet exemple](https://aka.ms/UltraSSDTemplate) pour le modèle Resource Manager complet) :

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "properties": {
                    "hardwareProfile": {},
                    "additionalCapabilities" : {
                                    "ultraSSDEnabled" : "true"
                    },
                    "osProfile": {},
                    "storageProfile": {},
                    "networkProfile": {}
    }
}
```

Une fois que la machine virtuelle est provisionnée, vous pouvez partitionner et formater les disques de données, puis les configurer pour vos charges de travail.

## <a name="additional-ultra-ssd-scenarios"></a>Autres scénarios Ultra SSD

- Lors de la création d’une machine virtuelle, les disques Ultra SSD peuvent également être implicitement créés. Toutefois, ces disques reçoivent une valeur par défaut pour les IOPS (500) et le débit (8 Mio/s).
- Les disques Ultra SSD supplémentaires peuvent être attachés aux machines virtuelles compatibles avec Ultra SSD.
- Ultra SSD permet la modification des attributs de performances des disques (IOPS et débit) lors de l’exécution, sans détacher le disque de la machine virtuelle. Une fois qu’une opération de redimensionnement de performances de disque est émise sur un disque, elle peut mettre jusqu’à une heure à être appliquée.
- Pour augmenter la capacité du disque, vous devez désallouer une machine virtuelle.

# <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez essayer le nouveau type de disque alors que vous n’êtes pas inscrit à la préversion, [demandez l’accès par le biais de cette enquête](https://aka.ms/UltraSSDPreviewSignUp).