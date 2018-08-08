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
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 23b5b5d79f0f905d7c4a173247232ede2cad2877
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412445"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Utiliser des profils de version des API avec Python dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="python-and-api-version-profiles"></a>Python et les profils de version d’API

Le Kit de développement logiciel (SDK) Python prend en charge les profils de version d’API pour cibler différentes plateformes de cloud telles qu’Azure Stack et Azure globale. Vous pouvez utiliser des profils d’API dans la création de solutions pour un cloud hybride. Le Kit de développement logiciel (SDK) Python prend en charge les profils d’API suivants :

1. **le plus récent**  
    Le profil cible les dernières versions d’API de tous les fournisseurs de service dans la plateforme Azure.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    Le profil cible les versions d’API des fournisseurs de ressource pris en charge par Azure Stack.

    Pour plus d’informations sur les profils d’API et Azure Stack, consultez [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Installer le Kit de développement logiciel (SDK) Python

1.  Installez Git à partir du [site officiel](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Pour obtenir des instructions pour installer le Kit de développement logiciel (SDK) Python, consultez [Azure pour développeurs Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour une utilisation ultérieure. Pour obtenir des instructions pour créer un abonnement, consultez [Créer des abonnements pour des offres dans Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Créez un principal de service et enregistrez son ID et son secret. Pour obtenir des instructions pour créer un principal du service pour Azure Stack, consultez [Fournir l’accès des applications à Azure Stack](../azure-stack-create-service-principals.md). 
5.  Vérifiez que votre principal de service a le rôle de contributeur/propriétaire sur votre abonnement. Pour obtenir des instructions sur l’attribution de rôle au principal du service, consultez [Fournir l’accès des applications à Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser le Kit de développement logiciel (SDK) Azure pour Python avec Azure Stack, vous devez fournir les valeurs suivantes et définir les valeurs avec des variables d’environnement. Consultez les instructions après le tableau pour définir les variables d’environnement en fonction de votre système d’exploitation. 

| Valeur | Variables d’environnement | Description |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID client | AZURE_TENANT_ID | La valeur de votre [ID de locataire](../azure-stack-identity-overview.md) Azure Stack. |
| ID client | AZURE_CLIENT_ID | L’ID d’application du principal du service enregistré lors de la création du principal de service dans la section précédente de ce document. |
| Identifiant d’abonnement | AZURE_SUBSCRIPTION_ID | [L’ID d’abonnement](../azure-stack-plan-offer-quota-overview.md#subscriptions) correspond à la façon dont vous accédez à des offres dans Azure Stack. |
| Clé secrète client | AZURE_CLIENT_SECRET | Le secret d’application du principal de service enregistré lors de la création du principal de service. |
| Point de terminaison Resource Manager | ARM_ENDPOINT | Consultez [Point de terminaison Azure Stack Resource Manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


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

5.  Créez un [principal de service](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) à utiliser avec Azure Stack. Ce principal de service doit avoir le [rôle de collaborateur ou propriétaire](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) sur votre abonnement.

6.  Définissez les variables d’environnement suivantes, puis exportez-les dans votre environnement actuel. 

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Pour que vous puissiez exécuter cet exemple, les images Ubuntu 16.04-LTS et WindowsServer 2012-R2-Datacenter doivent être présentes sur la Place de marché Azure Stack. Ces images peuvent être [téléchargées à partir d’Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) ou [ajoutées au référentiel PIR (Platform Image Repository)](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

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
- Si vous n’avez pas d’abonnement Microsoft Azure, vous pouvez obtenir un compte d’essai GRATUIT [ici](http://go.microsoft.com/fwlink/?LinkId=330212).
