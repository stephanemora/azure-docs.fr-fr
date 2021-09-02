---
title: Créer des modèles de configuration personnalisée
description: Créer des modèles de configuration personnalisée
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dinethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e7d5d470fd967c9f350fddaae2032085caf17c07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562726"
---
# <a name="create-custom-configuration-templates"></a>Créer des modèles de configuration personnalisée

Cet article explique comment créer un modèle de configuration personnalisée pour le contrôleur de données compatible Azure Arc. 

L’un des paramètres requis lors du déploiement d’un contrôleur de données en mode indirectement connecté est le paramètre `az arcdata dc create --profile-name`. Pour le moment, vous pouvez trouver la liste des profils intégrés disponibles en exécutant la requête :

```azurecli
az arcdata dc config list
```
Ces profils sont des fichiers JSON de modèle qui ont différents paramètres pour le contrôleur de données compatible Azure Arc, comme le registre Docker et les paramètres du référentiel, les classes de stockage pour les données et les journaux, la taille de stockage pour les données et les journaux, la sécurité, le type de service, etc., et peuvent être personnalisés dans votre environnement. 

Toutefois, dans certains cas, vous souhaiterez peut-être personnaliser ces modèles de configuration pour répondre à vos besoins et passer le modèle de configuration personnalisé à l’aide du paramètre `--path` à la commande `az arcdata dc create` au lieu de passer un modèle de configuration préconfiguré à l’aide du paramètre `--profile-name`.

## <a name="create-customjson-file"></a>Créer un fichier custom.json

Exécutez `az arcdata dc config init` pour lancer un fichier control.json avec des paramètres prédéfinis en fonction de votre distribution du cluster Kubernetes.
Par exemple, un fichier control.json pour un cluster Kubernetes basé sur le modèle `azure-arc-kubeadm` dans un sous-répertoire appelé `custom` dans le répertoire de travail actuel peut être créé comme suit :

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
Le fichier control.json créé sur le fichier peut être modifié dans n’importe quel éditeur, comme Visual Studio Code pour personnaliser les paramètres appropriés pour votre environnement.

## <a name="use-custom-controljson-file-to-deploy-azure-arc-enabled-data-controller-using-azure-cli-az"></a>Utiliser un fichier control.js personnalisé pour déployer le contrôleur de données compatible Azure Arc à l’aide d’Azure CLI (az)

Une fois le fichier de modèle créé, il peut être appliqué avec la commande de création de contrôleur de données compatible Azure Arc comme suit :

```azurecli
az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect  --k8s-namespace <namespace> --use-k8s

#Example:
#az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription ID> --resource-group my-resource-group --location eastus --connectivity-mode indirect --k8s-namespace <namespace> --use-k8s
```

## <a name="use-custom-controljson-file-for-deploying-azure-arc-data-controller-using-azure-portal"></a>Utiliser un fichier control.json personnalisé pour déployer un contrôleur de données Azure Arc à l’aide du Portail Azure

Dans l’écran de création de contrôleur de données Azure Arc, sélectionnez « Configurer un modèle personnalisé » sous Modèle personnalisé. Cela appellera un panneau permettant de fournir des paramètres personnalisés. Dans ce panneau, vous pouvez soit taper les valeurs des différents paramètres, soit télécharger un fichier control.json préconfiguré directement. 

Une fois que vous avez vérifié que les valeurs sont correctes, cliquez sur Appliquer pour poursuivre le déploiement du contrôleur de données Azure Arc.

## <a name="next-steps"></a>Étapes suivantes

[Déployer le contrôleur de données - Mode de connexion directe (Prérequis)](create-data-controller-direct-prerequisites.md)

[Créer le contrôleur de données Azure Arc (CLI)](create-data-controller-direct-cli.md)
