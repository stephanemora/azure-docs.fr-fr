---
title: Déployer OpenShift Container Platform autogérées place de marché offre dans Azure | Microsoft Docs
description: Déployer OpenShift Container Platform autogérées place de marché offre dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/18/2019
ms.author: haroldw
ms.openlocfilehash: 1228c770799de37c85b8a48b1dc923ac8294eeca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773463"
---
# <a name="configure-prerequisites"></a>Configuration préalable requise

Avant de déployer un cluster OpenShift Container Platform autogéré dans Azure à l’aide de l’offre de la place de marché, quelques conditions préalables doivent être configurés.  Lire le [conditions préalables de OpenShift](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) pour obtenir des instructions créer un ssh clé (sans une phrase secrète), coffre de clés Azure, clé secrète de coffre de clés et un principal de service.

 
## <a name="deploy-using-the-marketplace-offer"></a>Déployer à l’aide de l’offre de la place de marché

La façon la plus simple pour déployer un cluster OpenShift Container Platform autogéré dans Azure consiste à utiliser le [offre place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Cette option est la plus simple, mais il a également limité des fonctionnalités de personnalisation. L’offre de la place de marché déploie OpenShift Container Platform 3.11.82 et inclut les options de configuration suivantes :

- **Nœuds master** : trois (3) nœuds master avec le type d’instance configurable.
- **Nœuds infrastructure** : trois (3) nœuds infrastructure avec le type d’instance configurable.
- **Nœuds** : Le nombre de nœuds (entre 1 et 9) et le type d’instance est configurable.
- **Type de disque** : La fonctionnalité Disques managés est utilisée.
- **Réseau** : Prise en charge de réseau nouveau ou existant et la plage CIDR personnalisée.
- **CNS** : peut être activé.
- **Métriques** : peuvent être activées.
- **Journalisation** : peut être activée.
- **Fournisseur de cloud Azure** : Activé par défaut, peut être désactivée.

Dans le coin supérieur gauche du portail Azure, cliquez sur **créer une ressource**, entrez « openshift container platform » dans la zone de recherche et appuyez sur ENTRÉE.

   ![Nouvelle recherche dans les ressources](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

La page de résultats s’ouvre avec **Red Hat OpenShift Container Platform autogérées** dans la liste. 

   ![Autre résultat de recherche de ressources](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Cliquez sur l’offre pour afficher les détails de l’offre. Pour déployer cette offre, cliquez sur **créer**. L’interface utilisateur à entrer les paramètres nécessaires s’affiche. Le premier écran est la **notions de base** panneau.

   ![Page de titre de l’offre](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Concepts de base**

Pour obtenir une aide sur un des paramètres d’entrée, survolez le ***je*** en regard du nom de paramètre.

Entrez les valeurs des paramètres d’entrée et cliquez sur **OK**.

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| Nom d’utilisateur administrateur de machine virtuelle | L’utilisateur administrateur doit être créé sur toutes les instances de machine virtuelle |
| SSH clé publique pour l’utilisateur Admin | Clé publique SSH utilisé pour vous connecter à la machine virtuelle - ne doit pas avoir une phrase secrète |
| Abonnement | Abonnement Azure pour déployer le cluster dans |
| Groupe de ressources | Créer un nouveau groupe de ressources ou sélectionnez un groupe de ressources vide existant pour les ressources de cluster |
| Lieu | Région Azure pour déployer le cluster dans |

   ![Panneau de base offre](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Paramètres d’infrastructure**

Entrez les valeurs des paramètres d’entrée et cliquez sur **OK**.

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| Préfixe du nom de Cluster OCP | L’utilisateur administrateur doit être créé sur toutes les instances de machine virtuelle |
| Taille du nœud master | Acceptez la taille de machine virtuelle par défaut ou cliquez sur **modifier la taille** pour sélectionner une autre taille de machine virtuelle.  Sélectionnez la taille de machine virtuelle appropriée pour votre charge de travail |
| Taille de nœud d’infrastructure | Acceptez la taille de machine virtuelle par défaut ou cliquez sur **modifier la taille** pour sélectionner une autre taille de machine virtuelle.  Sélectionnez la taille de machine virtuelle appropriée pour votre charge de travail |
| Nombre de nœuds d’Application | Acceptez la taille de machine virtuelle par défaut ou cliquez sur **modifier la taille** pour sélectionner une autre taille de machine virtuelle.  Sélectionnez la taille de machine virtuelle appropriée pour votre charge de travail |
| Taille de nœud d’application | Acceptez la taille de machine virtuelle par défaut ou cliquez sur **modifier la taille** pour sélectionner une autre taille de machine virtuelle.  Sélectionnez la taille de machine virtuelle appropriée pour votre charge de travail |
| Taille de l’hôte bastion | Acceptez la taille de machine virtuelle par défaut ou cliquez sur **modifier la taille** pour sélectionner une autre taille de machine virtuelle.  Sélectionnez la taille de machine virtuelle appropriée pour votre charge de travail |
| Réseau virtuel nouveau ou existant | Créer un nouveau réseau virtuel (par défaut) ou utiliser un réseau virtuel existant |
| Choisissez les paramètres par défaut de CIDR ou personnaliser la plage IP (CIDR) | Accepter les plages d’adresses CIDR par défaut ou sélectionnez **plage IP personnalisée** et entrez les informations de routage CIDR personnalisées.  Paramètres par défaut créer de réseau virtuel avec CIDR de 10.0.0.0/14, sous-réseau maître avec 10.1.0.0/16, infrastructure sous-réseau avec 10.2.0.0/16 et un sous-réseau de calcul et les noms communs avec 10.3.0.0/16 |
| Nom du groupe de ressources KEY Vault | Le nom du groupe de ressources qui contient le coffre de clés |
| Nom du coffre de clés | Le nom du coffre de clés qui contient la clé secrète avec le ssh clé privée.  Uniquement des caractères alphanumériques et des tirets sont autorisés et être comprise entre 3 et 24 caractères |
| Nom du secret | Le nom de la clé secrète qui contient le ssh clé privée.  Uniquement des caractères alphanumériques et des tirets sont autorisés. |

   ![Panneau de l’infrastructure offre](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Modifier la taille**

Pour sélectionner une autre taille de machine virtuelle, cliquez sur ***modifier la taille***.  La fenêtre de sélection de machine virtuelle s’ouvre.  Sélectionnez la taille de machine virtuelle souhaitée, puis cliquez **sélectionnez**.

   ![Sélectionnez la taille de machine virtuelle](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Réseau virtuel existant**

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| Nom de réseau virtuel existant | Nom du réseau virtuel existant |
| Nom du sous-réseau pour les nœuds principaux | Nom du sous-réseau existant pour les nœuds principaux.  Doit contenir au moins 16 adresses IP et suivez le document RFC 1918 |
| Nom de sous-réseau pour infra nœuds | Nom d’existant de sous-réseau pour les nœuds infrarouge.  Doit contenir au moins 32 adresses IP et suivez le document RFC 1918 |
| Nom du sous-réseau pour les nœuds de calcul et les noms communs | Nom du sous-réseau existant pour les nœuds de calcul et les noms communs.  Doit contenir au moins 32 adresses IP et suivez le document RFC 1918 |
| Groupe de ressources pour le réseau virtuel existant | Nom du groupe de ressources qui contient le réseau virtuel existant |

   ![Offre de réseau virtuel existant d’infrastructure](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Plage d’adresses IP personnalisé**

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| Plage d’adresses pour le réseau virtuel | Custom CIDR pour le réseau virtuel |
| Plage d’adresses du sous-réseau contenant les nœuds principaux | Custom CIDR pour le sous-réseau principal |
| Plage d’adresses du sous-réseau contenant les nœuds d’infrastructure | Custom CIDR de sous-réseau d’infrastructure |
| Plage d’adresses de sous-réseau qui contient les nœuds de calcul et les noms communs | CIDR personnalisée pour les nœuds de calcul et les noms communs |

   ![Offre la plage d’IP infrastructure personnalisée](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Entrez des valeurs pour les paramètres d’entrée et cliquez sur **OK**

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| Mot de passe utilisateur Admin de OpenShift | Mot de passe pour l’utilisateur OpenShift initiale.  Cet utilisateur sera également l’administrateur de cluster |
| Confirmer le mot de passe utilisateur Admin de OpenShift | Retapez le mot de passe utilisateur Admin de OpenShift |
| Nom d’utilisateur de Red Hat Gestionnaire d’abonnements | Nom d’utilisateur pour accéder à votre abonnement Red Hat ou ID d’organisation.  Ces informations d’identification sont utilisée pour inscrire l’instance RHEL à votre abonnement et ne sont pas stockées par Microsoft ou Red Hat |
| Mot de passe de Red Hat Gestionnaire d’abonnements utilisateur | Mot de passe pour accéder à votre abonnement Red Hat ou la clé d’Activation.  Ces informations d’identification sont utilisée pour inscrire l’instance RHEL à votre abonnement et ne sont pas stockées par Microsoft ou Red Hat |
| ID du Pool de OpenShift Red Hat Gestionnaire d’abonnements | ID du pool qui contient les droits d’OpenShift Container Platform. Assurez-vous de qu'avoir suffisamment de droits d’OpenShift Container Platform pour l’installation du cluster |
| ID de Pool de OpenShift Red Hat abonnement Manager pour service Broker / principale des nœuds | Pool d’ID qui contient les droits d’OpenShift Container Platform pour service Broker / nœuds Master. Assurez-vous de qu'avoir suffisamment de droits d’OpenShift Container Platform pour l’installation du cluster. Si vous n’utilisez ne pas service broker / master ID du pool, entrez l’ID de pool de nœuds d’Application |
| Configurer le fournisseur de Cloud Azure | Configurer OpenShift pour utiliser le fournisseur de Cloud Azure. Nécessaire si s’attache à l’aide de disque Azure pour les volumes persistants.  Valeur par défaut est Yes |
| GUID de l’ID Client principal du Service AD Azure | Principal de Service AD Client ID GUID Azure - également appelé AppID. Nécessaire uniquement si le fournisseur de Cloud Azure de configurer la valeur **Oui** |
| Clé secrète Azure ID Client de principal du Service AD | Secret d’ID Client principal du Service AD Azure. Nécessaire uniquement si le fournisseur de Cloud Azure de configurer la valeur **Oui** |
 
   ![Panneau de OpenShift offre](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**Paramètres supplémentaires**

Le panneau des paramètres supplémentaires permet de configurer des noms communs pour le stockage de glusterfs, journalisation, mesures et routeur Sub de domaine.  La valeur par défaut ne s’installe pas ces options et utilisera nip.io le sous-domaine de routeur à des fins de test. L’activation de noms communs installe trois nœuds de calcul supplémentaires avec trois disques attachés supplémentaires qui hébergeront glusterfs pods.  

Entrez des valeurs pour les paramètres d’entrée et cliquez sur **OK**

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| Configurer le conteneur de stockage natif (CNS) | Installations noms communs dans le OpenShift du cluster et activer en tant que stockage. Par défaut est si le fournisseur Azure est désactivé |
| Configurer la journalisation de Cluster | Installe les fonctionnalités de journalisation EFK dans le cluster.  Dimensionner infra nœuds correctement avec les pods d’EFK hôte |
| Configurer les métriques pour le Cluster | Installe Hawkular métriques dans le cluster OpenShift.  Dimensionner infra nœuds correctement avec les pods de métriques Hawkular hôte |
| Domaine de Sub de routeur par défaut | Sélectionnez nipio de test ou personnalisé pour entrer votre propre sous-domaine pour la production |
 
   ![Offre un autre panneau](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**Paramètres supplémentaires - paramètres supplémentaires**

| Paramètre d’entrée | Description du paramètre |
|-----------------------|-----------------|
| (CNS) Taille du nœud | Acceptez la taille de nœud par défaut ou sélectionnez **modifier la taille** pour sélectionner une nouvelle taille de machine virtuelle |
| Entrez votre sous-domaine personnalisé | Le domaine de routage personnalisé à utiliser pour exposer les applications via le routeur sur le cluster OpenShift.  Veillez à créer l’entrée DNS de caractère générique approprié] |
 
   ![Proposer des noms communs supplémentaires installer](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Résumé**

La validation se produit à ce stade pour vérifier le quota de cœurs est suffisant pour déployer le nombre total de machines virtuelles sélectionnées pour le cluster.  Passez en revue tous les paramètres qui ont été entrés.  Si les entrées sont acceptables, cliquez sur **OK** pour continuer.

   ![Panneau de synthèse d’offre](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Acheter**

Vérifiez les informations de contact sur la page acheter et cliquez sur **achat** pour accepter les conditions d’utilisation et de démarrer le déploiement du cluster OpenShift Container Platform.

   ![Panneau d’achat offre](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Se connecter au cluster OpenShift

Une fois le déploiement terminé, récupérez la connexion à partir de la section de sortie du déploiement. Se connecter à la console OpenShift dans votre navigateur à l’aide de la **URL de la Console OpenShift**. Vous pouvez également exécuter SSH à l’hôte Bastion. Dans l’exemple suivant, le nom d’utilisateur administrateur est clusteradmin et le nom de domaine complet du DNS d’adresses IP publiques Bastion est bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com :

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Utilisez la commande [az group delete](/cli/azure/group) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées quand vous n’en avez plus besoin.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Étapes suivantes

- [Tâches de post-déploiement](./openshift-post-deployment.md)
- [Résoudre les problèmes de déploiement d’OpenShift dans Azure](./openshift-troubleshooting.md)
- [Prise en main d’OpenShift Container Platform](https://docs.openshift.com/container-platform)

