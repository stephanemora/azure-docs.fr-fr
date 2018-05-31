---
title: Utilisation de profils de version des API avec Python dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser des profils de version des API avec Python dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32310297"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Utiliser des profils de version des API avec Python dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="python-samples-for-azure-stack"></a>Exemples Python pour Azure Stack 

Vous pouvez utiliser les exemples de code ci-dessous pour effectuer les tâches de gestion courantes sur les machines virtuelles dans Azure Stack.

Les exemples de code vous montrent comment effectuer les opérations suivantes :

- Création de machines virtuelles :
    - Créer une machine virtuelle Linux
    - Créer une machine virtuelle Windows
- Mise à jour d’une machine virtuelle :
    - Étendre un disque
    - Étiqueter une machine virtuelle
    - Connecter des disques de données
    - Détacher des disques de données
- Utilisation d’une machine virtuelle :
    - Démarrage d'une machine virtuelle
    - Arrêt d’une machine virtuelle
    - Redémarrage d’une machine virtuelle
- Répertorier des machines virtuelles
- Suppression d'une machine virtuelle

Pour revoir le code permettant d’effectuer ces opérations, consultez la fonction **run_example()** dans le script Python **Hybrid/unmanaged-disks/example.py** fourni dans le dépôt GitHub [virtual-machines-python-manage](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Chaque opération est clairement commentée et présente une fonction d’impression.
Les exemples ne sont pas forcément fournis dans l’ordre indiqué dans la liste ci-dessus.


## <a name="run-the-python-sample"></a>Exécuter l’exemple Python

1.  Installez [Python](https://www.python.org/downloads/) si vous ne l’avez pas encore fait.

    Cet exemple, ainsi que le SDK, sont compatibles avec Python 2.7, 3.4, 3.5 et 3.6.

2.  Il est généralement conseillé d’utiliser un environnement virtuel pour le développement Python. 
    Pour plus d’informations, consultez https://docs.python.org/3/tutorial/venv.html
    
    Installez et initialisez l’environnement virtuel avec le module « venv » sur Python 3 (installez [virtualenv](https://pypi.python.org/pypi/virtualenv) sur Python 2.7) :

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Clonez le dépôt.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Installez les dépendances à l’aide de pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Créez un [principal de service](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) à utiliser avec Azure Stack. Ce principal de service doit avoir le [rôle de collaborateur ou propriétaire](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) sur votre abonnement.

6.  Définissez les variables d’environnement suivantes, puis exportez-les dans votre environnement actuel. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Notez que, pour exécuter cet exemple, les images Ubuntu 16.04-LTS et WindowsServer 2012-R2-Datacenter doivent être présentes sur la Place de marché Azure Stack. Ces images peuvent être [téléchargées à partir d’Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) ou [ajoutées au référentiel PIR (Platform Image Repository)](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Exécutez l’exemple.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Notes

Vous pouvez être tenté de récupérer le disque du système d’exploitation d’une machine virtuelle en utilisant `virtual_machine.storage_profile.os_disk`.
Dans certains cas, cela peut donner le résultat escompté, mais vous obtiendrez un objet `OSDisk`.
Pour changer la taille du disque du système d’exploitation, comme le fait `example.py`, il ne faut pas un objet `OSDisk`, mais un objet `Disk`.
`example.py` obtient l’objet `Disk` de cette façon :

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Étapes suivantes

- [Centre de développement Python pour Azure](https://azure.microsoft.com/develop/python/)
- [Documentation sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Parcours d’apprentissage des machines virtuelles](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Si vous n’avez pas d’abonnement Microsoft Azure, vous pouvez demander un compte d’essai GRATUIT [ici](http://go.microsoft.com/fwlink/?LinkId=330212).
