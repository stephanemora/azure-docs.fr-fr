---
title: Déployer l’exemple de blueprint Azure Security Benchmark Foundation
description: Étapes de déploiement de l’exemple de blueprint Azure Security Benchmark Foundation et informations détaillées sur les paramètres d’artefact de blueprint.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: af41dd50c976ac6c0570b8a089211fa310ef4ef1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232611"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Déployer l’exemple de blueprint Azure Security Benchmark Foundation

Pour déployer l’exemple de blueprint Azure Security Benchmark Foundation, vous devez effectuer les étapes suivantes :

> [!div class="checklist"]
> - Créer un blueprint à partir de l’exemple
> - Marquer la copie de l’exemple en tant que **Publié**
> - Affecter votre copie du blueprint à un abonnement existant

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free) avant de commencer.

## <a name="create-blueprint-from-sample"></a>Créer un blueprint à partir de l’exemple

Commencez par implémenter l’exemple de blueprint. Pour cela, créez un blueprint dans votre environnement en vous servant de l’exemple comme point de départ.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Dans la page **Démarrage** à gauche, sélectionnez le bouton **Créer** sous _Créer un blueprint_.

1. Recherchez l’exemple de blueprint **Azure Security Benchmark Foundation** sous _Autres exemples_, puis sélectionnez **Utiliser cet exemple**.

1. Entrez les _Fonctions de base_ de l’exemple de blueprint :

   - **Nom du blueprint** : Spécifiez un nom pour votre copie de l’exemple de blueprint Azure Security Benchmark Foundation.
   - **Emplacement de la définition** : utilisez les points de suspension et sélectionnez le groupe d’administration dans lequel vous souhaitez enregistrer votre copie de l’exemple.

1. Sélectionnez l’onglet _Artefacts_ dans le haut de la page ou **Suivant : Artefacts** dans le bas de la page.

1. Passez en revue la liste des artefacts qui composent l’exemple de blueprint. De nombreux artefacts ont des paramètres que nous définirons plus tard. Sélectionnez **Enregistrer comme brouillon** lorsque vous avez terminé de passer en revue l’exemple de blueprint.

## <a name="publish-the-sample-copy"></a>Publier la copie de l’exemple

Votre copie de l’exemple de blueprint est à présent créée dans votre environnement. Elle est créée en mode **Brouillon** et doit être **publiée** avant de pouvoir être attribuée et déployée. Vous pouvez personnaliser la copie de l’exemple de blueprint en fonction de votre environnement et de vos besoins, mais ces modifications peuvent l’éloigner du blueprint Azure Security Benchmark Foundation.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Publier le blueprint** dans le haut de la page. Dans la nouvelle page à droite, indiquez la **Version** de votre copie de l’exemple de blueprint. Cette propriété est utile si vous prévoyez d’effectuer une modification ultérieurement. Dans **Notes de changement**, indiquez par exemple « Première version publiée à partir de l’exemple de blueprint Azure Security Benchmark Foundation ». Sélectionnez ensuite **Publier** en bas de la page.

## <a name="assign-the-sample-copy"></a>Affecter la copie de l’exemple

Une fois que la copie de l’exemple de blueprint a été **publiée**, elle peut être affectée à un abonnement dans le groupe d’administration où elle a été enregistrée. C’est à cette étape que les paramètres sont fournis pour que chaque déploiement de la copie de l’exemple de blueprint soit unique.

1. Sélectionnez **Tous les services** dans le volet gauche. Recherchez et sélectionnez **Blueprints**.

1. Sélectionnez la page **Définitions de blueprint** à gauche. Utilisez les filtres pour rechercher votre copie de l’exemple de blueprint, puis sélectionnez-la.

1. Sélectionnez **Affecter le blueprint** dans le haut de la page.

1. Indiquez les valeurs des paramètres pour l’affectation du blueprint :

   - Concepts de base
       - **Abonnements** : Sélectionnez un ou plusieurs des abonnements qui font partie du groupe d’administration où vous avez enregistré votre copie de l’exemple de blueprint. Si vous sélectionnez plusieurs abonnements, une affectation est créée pour chacun d’eux à l’aide des paramètres saisis.
     - **Nom de l’affectation** : Le nom est prérempli en fonction du nom du blueprint.
       Changez-le si nécessaire ou laissez-le tel quel.
     - **Emplacement** : Sélectionnez une région dans laquelle créer l’identité managée.
     - Azure Blueprint utilise cette identité managée pour déployer tous les artefacts dans le blueprint affecté.
       Pour en savoir plus, consultez [Identités managées pour les ressources Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version de définition du blueprint** : Choisissez une version **publiée** de votre copie de l’exemple de blueprint.

   - Verrouiller l'affectation

     Sélectionnez le paramètre de verrouillage de blueprint pour votre environnement. Pour plus d’informations, consultez [Verrouillage des ressources des blueprints](../../concepts/resource-locking.md).

   - Identité managée

     Choisissez l’option d’identité managée _affectée par le système_ par défaut ou l’option d’identité _affectée par l’utilisateur_.

   - Paramètres de blueprint

     Les paramètres définis dans cette section sont utilisés par de nombreux artefacts dans la définition du blueprint à des fins de cohérence.
    
     - **Préfixe pour les ressources et groupes de ressources** : cette chaîne est utilisée comme préfixe pour tous les noms de ressources et de groupes de ressources
     - **Hub name** : nom du hub
     - **Rétention du journal (jours)**  : nombre de jours pendant lesquels les journaux sont conservés ; la saisie de « 0 » conserve les journaux indéfiniment
     - **Déployer un hub** : entrez « true » ou « false » pour spécifier si l’attribution déploie les composants hub de l’architecture
     - **Emplacement du hub** : emplacement du groupe de ressources de hub
     - **Adresses IP de destination** : adresses IP de destination pour la connectivité sortante ; liste d’adresses IP séparées par des virgules ou de préfixes de plage d’adresses IP
     - **Nom de Network Watcher** : nom de la ressource Network Watcher
     - **Nom du groupe de ressources Network Watcher** : nom du groupe de ressources Network Watcher
     - **Activer la protection DDos** : entrez « true » ou « false » pour spécifier si la protection DDoS est activée ou non dans le réseau virtuel
     
    > [!NOTE] 
    > Si Network Watcher est déjà activé, il est recommandé d’utiliser le groupe de ressources Network Watcher existant. Vous devez également fournir l’emplacement du groupe de ressources Network Watcher existant pour le paramètre d’artefact **Emplacement du groupe de ressources Network Watcher**.

   - Paramètres d'artefact

     Les paramètres définis dans cette section s’appliquent à l’artefact sous lequel elle est définie. Ces paramètres sont des [paramètres dynamiques](../../concepts/parameters.md#dynamic-parameters) puisqu’ils sont définis lors de l’affectation du blueprint. Pour obtenir la liste complète des paramètres d’artefact et leur description, consultez le [tableau des paramètres d’artefact](#artifact-parameters-table).

1. Une fois tous les paramètres entrés, sélectionnez **Affecter** au bas de la page. L’affectation du blueprint est créée et le déploiement de l’artefact démarre. Le déploiement prend environ une heure. Pour vérifier l’état du déploiement, ouvrez l’affectation du blueprint.

> [!WARNING]
> Le service Azure Blueprints et les exemples de blueprint intégrés sont **gratuits**. Les ressources Azure sont [facturées par produit](https://azure.microsoft.com/pricing/). Utilisez la [calculatrice de prix](https://azure.microsoft.com/pricing/calculator/) pour estimer le coût d’exécution des ressources déployées par cet exemple de blueprint.

## <a name="artifact-parameters-table"></a>Tableau des paramètres d’artefact

Le tableau suivant fournit la liste des paramètres de blueprint :

|Nom de l’artefact|Type d’artefact|Nom du paramètre|Description|
|-|-|-|-|
|Groupe de ressources de hub|Resource group|Nom de groupe ressources|Verrouillé – Concatène le préfixe avec le nom du hub|
|Groupe de ressources de hub|Resource group|Emplacement du groupe de ressources|Verrouillé – Utilise l’emplacement du hub|
|Modèle Pare-feu Azure|Modèle Resource Manager|Adresse IP privée du Pare-feu Azure||
|Modèle de diagnostic et Azure Log Analytics|Modèle Resource Manager|Emplacement de l’espace de travail Log Analytics|Emplacement où l’espace de travail Log Analytics est créé ; exécutez `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` dans Azure PowersShell pour afficher les régions disponibles|
|Modèle de diagnostic et Azure Log Analytics|Modèle Resource Manager|ID de compte Azure Automation (facultatif)|ID de ressource du compte Automation ; utilisé pour créer un service lié entre Log Analytics et un compte Automation|
|Modèle de groupe de sécurité réseau Azure|Modèle Resource Manager|Activer les journaux de flux NSG|Entrez « true » ou « false » pour activer ou désactiver les journaux de flux NSG|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de réseau virtuel|Préfixe d’adresse de réseau virtuel pour le réseau virtuel de hub|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de sous-réseau de pare-feu|Préfixe d’adresse de sous-réseau de pare-feu pour le réseau virtuel de hub|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de sous-réseau Bastion|Préfixe d’adresse de sous-réseau Bastion pour le réseau virtuel de hub|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse du sous-réseau de passerelle|Préfixe d’adresse de sous-réseau de passerelle pour le réseau virtuel de hub|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de sous-réseau de gestion|Préfixe d’adresse de sous-réseau de gestion pour le réseau virtuel de hub|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de sous-réseau de machine de rebond|Préfixe d’adresse de sous-réseau de machine de rebond pour le réseau virtuel de hub|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Noms d’adresses de sous-réseau (facultatif)|Tableau de noms de sous-réseaux à déployer sur le réseau virtuel de hub ; par exemple, « subnet1 », « subnet2 »|
|Modèle de hub de réseau virtuel Azure|Modèle Resource Manager|Préfixes d’adresses de sous-réseau (facultatif)|Tableau de préfixes d’adresses IP pour les sous-réseaux facultatifs pour le réseau virtuel de hub ; par exemple, « 10.0.7.0/24 », « 10.0.8.0/24 »|
|Groupe de ressources de spoke|Resource group|Nom de groupe ressources|Verrouillé – Concatène le préfixe avec le nom du spoke|
|Groupe de ressources de spoke|Resource group|Emplacement du groupe de ressources|Verrouillé – Utilise l’emplacement du hub|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Déployer un spoke|Entrez « true » ou « false » pour spécifier si l’attribution déploie les composants spoke de l’architecture|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|ID d’abonnement hub|ID d’abonnement dans lequel le hub est déployé ; la valeur par défaut est l’abonnement où figure la définition de blueprint|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Nom de spoke|Le nom du spoke|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de réseau virtuel|Préfixe d’adresse de réseau virtuel pour le réseau virtuel de spoke|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Préfixe d’adresse de sous-réseau|Préfixe d’adresse de sous-réseau pour le réseau virtuel de spoke|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Noms d’adresses de sous-réseau (facultatif)|Tableau de noms de sous-réseaux à déployer sur le réseau virtuel de spoke ; par exemple, « subnet1 », « subnet2 »|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Préfixes d’adresses de sous-réseau (facultatif)|Tableau de préfixes d’adresses IP pour les sous-réseaux facultatifs pour le réseau virtuel de spoke ; par exemple, « 10.0.7.0/24 », « 10.0.8.0/24 »|
|Modèle de spoke de réseau virtuel Azure|Modèle Resource Manager|Déployer un spoke|Entrez « true » ou « false » pour spécifier si l’attribution déploie les composants spoke de l’architecture|
|Modèle Azure Network Watcher|Modèle Resource Manager|Emplacement Network Watcher|Emplacement de la ressource Network Watcher|
|Modèle Azure Network Watcher|Modèle Resource Manager|Emplacement du groupe de ressources Network Watcher|Si Network Watcher est déjà activé, la valeur de ce paramètre **doit** correspondre à l’emplacement du groupe de ressources Network Watcher existant.|

## <a name="troubleshooting"></a>Dépannage

Si vous rencontrez l’erreur `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.`, vérifiez que le paramètre de blueprint **Nom du groupe de ressources Network Watcher** spécifie le nom du groupe de ressources Network Watcher existant et que le paramètre d’artefact **Emplacement du groupe de ressources Network Watcher** spécifie l’emplacement du groupe de ressources Network Watcher existant.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de passer en revue les étapes à suivre pour déployer l’exemple de blueprint Azure Security Benchmark Foundation. Consultez à présent l’article suivant pour en apprendre davantage sur l’architecture :

> [!div class="nextstepaction"]
> [Blueprint Azure Security Benchmark Foundation – Vue d’ensemble](./index.md)

Autres articles sur les blueprints et la manière de les utiliser :

- En savoir plus sur le [cycle de vie des blueprints](../../concepts/lifecycle.md)
- Comprendre comment utiliser les [paramètres statiques et dynamiques](../../concepts/parameters.md).
- Apprendre à personnaliser l’[ordre de séquencement des blueprints](../../concepts/sequencing-order.md).
- Découvrir comment utiliser le [verrouillage de ressources de blueprint](../../concepts/resource-locking.md).
- Découvrir comment [mettre à jour des affectations existantes](../../how-to/update-existing-assignments.md).
