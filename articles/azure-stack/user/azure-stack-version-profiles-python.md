---
title: Utilisation de profils de version des API avec Python dans Azure Stack | Microsoft Docs
description: Découvrez comment utiliser des profils de version des API avec Python dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
<!-- dev: viananth -->
ms.openlocfilehash: c7c23352cea4f9e79b371f38112fb66ac31ac849
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242295"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Utiliser des profils de version des API avec Python dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="python-and-api-version-profiles"></a>Python et les profils de version d’API

Le Kit de développement logiciel (SDK) Python prend en charge les profils de version d’API pour cibler différentes plateformes de cloud telles qu’Azure Stack et Azure globale. Vous pouvez utiliser des profils d’API dans la création de solutions pour un cloud hybride. Le Kit de développement logiciel (SDK) Python prend en charge les profils d’API suivants :

- **le plus récent**  
    Ce profil cible les dernières versions d’API de tous les fournisseurs de services de la plateforme Azure.
- **2018-03-01-hybrid**     
    Ce profil cible les dernières versions d’API de tous les fournisseurs de ressources de la plateforme Azure Stack.
- **2017-03-09-profile**  
    Ce profil cible les versions d’API les plus compatibles des fournisseurs de ressources pris en charge par Azure Stack.

   Pour plus d’informations sur les profils d’API et Azure Stack, consultez [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Installer le kit SDK Azure Python

1. Installez Git à partir du [site officiel](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Pour des instructions sur l’installation du SDK Python, consultez [Azure pour développeurs Python](/python/azure/python-sdk-azure-install?view=azure-python).
3. Si vous n’en avez pas de disponible, créez un abonnement et enregistrez l’ID d’abonnement pour l’utiliser plus tard. Pour des instructions sur la création d’un abonnement, consultez [Créer des abonnements pour des offres dans Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).
4. Créez un principal de service et enregistrez son ID et son secret. Pour plus d’informations sur la création d’un principal de service pour Azure Stack, consultez l’article [Fournir l’accès des applications à Azure Stack](../azure-stack-create-service-principals.md).
5. Vérifiez que votre principal de service bénéficie du rôle contributeur/propriétaire sur votre abonnement. Pour obtenir des instructions sur l’attribution de rôle au principal du service, consultez [Fournir l’accès des applications à Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Prérequis

Pour pouvoir utiliser le SDK Azure pour Python avec Azure Stack, vous devez fournir les valeurs suivantes et définir les valeurs avec des variables d’environnement. Consultez les instructions après le tableau pour définir les variables d’environnement en fonction de votre système d’exploitation.

| Valeur | Variables d’environnement | Description |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID client | AZURE_TENANT_ID | La valeur de votre [ID de locataire](../azure-stack-identity-overview.md) Azure Stack. |
| ID client | AZURE_CLIENT_ID | ID d’application du principal de service enregistré lors de la création du principal de service dans la section précédente de cet article. |
| Identifiant d’abonnement | AZURE_SUBSCRIPTION_ID | [L’ID d’abonnement](../azure-stack-plan-offer-quota-overview.md#subscriptions) correspond à la façon dont vous accédez à des offres dans Azure Stack. |
| Clé secrète client | AZURE_CLIENT_SECRET | Le secret d’application du principal de service enregistré lors de la création du principal de service. |
| Point de terminaison Resource Manager | ARM_ENDPOINT | Consultez [Point de terminaison Azure Stack Resource Manager](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Emplacement de la ressource | AZURE_RESOURCE_LOCATION | Emplacement des ressources de votre environnement Azure Stack.

## <a name="python-samples-for-azure-stack"></a>Exemples Python pour Azure Stack

Parmi les exemples de code disponibles pour Azure Stack à l’aide du kit SDK Python, citons :

- [Gérer des ressources et des groupes de ressources](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Gérer un compte de stockage](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Gérer des machines virtuelles](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/).

## <a name="python-manage-virtual-machine-sample"></a>Exemple Python de gestion de machines virtuelles

Vous pouvez utiliser l’exemple de code suivant pour effectuer des tâches de gestion courantes sur les machines virtuelles dans Azure Stack. L’exemple de code vous montre comment :

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

Pour passer en revue le code qui effectue ces opérations, consultez la fonction **run_example()** du script Python **example.py** dans le dépôt GitHub [Hybrid-Compute-Python-Manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Chaque opération est clairement commentée et présente une fonction d’impression. Les exemples ne sont pas forcément fournis dans l’ordre indiqué dans cette liste.

## <a name="run-the-python-sample"></a>Exécuter l’exemple Python

1. Si vous ne l’avez pas déjà fait, [installez Python](https://www.python.org/downloads/). Cet exemple, ainsi que le SDK, sont compatibles avec Python 2.7, 3.4, 3.5 et 3.6.

2. Il est généralement conseillé d’utiliser un environnement virtuel pour le développement Python. Pour plus d’informations, consultez la [documentation de Python](https://docs.python.org/3/tutorial/venv.html).

3. Installez et initialisez l’environnement virtuel avec le module « venv » sur Python 3 (installez [virtualenv](https://pypi.python.org/pypi/virtualenv) sur Python 2.7) :

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Clonez le dépôt :

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Installez les dépendances à l’aide de pip :

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Créez un [principal de service](../azure-stack-create-service-principals.md) à utiliser avec Azure Stack. Ce principal de service doit avoir le [rôle de collaborateur ou propriétaire](../azure-stack-create-service-principals.md#assign-a-role) sur votre abonnement.

7. Définissez les variables d’environnement suivantes, puis exportez-les dans votre environnement actuel :

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Pour que vous puissiez exécuter cet exemple, les images Ubuntu 16.04-LTS et WindowsServer 2012-R2-Datacenter doivent être présentes dans la Place de marché Azure Stack. Vous pouvez les [télécharger à partir d’Azure](../azure-stack-download-azure-marketplace-item.md) ou les ajouter au [dépôt PIR (Platform Image Repository)](../azure-stack-add-vm-image.md).

9. Exécutez l’exemple :

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Étapes suivantes

- [Centre de développement Python pour Azure](https://azure.microsoft.com/develop/python/)
- [Documentation sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Parcours d’apprentissage des machines virtuelles](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Si vous n’avez pas d’abonnement Microsoft Azure, vous pouvez obtenir un compte d’essai GRATUIT [ici](https://go.microsoft.com/fwlink/?LinkId=330212).
