---
title: Installer Event Grid sur un cluster Kubernetes avec Azure Arc
description: Cet article décrit les étapes à suivre pour installer Event Grid sur un cluster Kubernetes avec Azure Arc.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 43879b5124c49ba3d0c4494e2d3a1eba95e617ff
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385460"
---
# <a name="install-event-grid-extension-on-azure-arc-enabled-kubernetes-cluster"></a>Installer l’extension Event Grid sur un cluster Kubernetes avec Azure Arc
Cet article vous guide tout au long des étapes d’installation d’Event Grid sur un cluster [Kubernetes avec Azure Arc](../../azure-arc/kubernetes/overview.md).

Par souci de concision, cet article nomme l’« extension Event Grid sur Kubernetes » « Event Grid sur Kubernetes » ou simplement « Event Grid ».

[!INCLUDE [event-grid-preview-feature-note.md](../../../includes/event-grid-preview-feature-note.md)]


## <a name="supported-kubernetes-distributions"></a>Distributions Kubernetes prises en charge
Voici les distributions Kubernetes prises en charge sur lesquelles Event Grid peuvent être déployé et exécuté.

1. [Distributions Kubernetes prises en charge](../../aks/supported-kubernetes-versions.md) par Azure AKS.
1. [OpenShift Container Platform](https://www.openshift.com/products/container-platform) RedHat .

D’autres distributions seront intégrées en fonction des [commentaires des utilisateurs](https://feedback.azure.com/forums/909934-azure-event-grid) et de leur [prise en charge Kubernetes avec Azure Arc](../../azure-arc/kubernetes/validation-program.md).

## <a name="event-grid-extension"></a>Extension Event Grid
L’opération qui installe une instance de service Event Grid sur un cluster Kubernetes est la création d’une extension de cluster Azure Arc, qui déploie un **répartiteur Event Grid** et un **opérateur Event Grid**. Pour plus d’informations sur les fonctions respectives du répartiteur et de l’opérateur, consultez [Composants d’Event Grid sur Kubernetes](concepts.md#event-grid-on-kubernetes-components). La fonctionnalité d’[extension de cluster Azure Arc](../../azure-arc/kubernetes/conceptual-extensions.md) assure une gestion du cycle de vie à l’aide d’opérations de plan de contrôle d’Azure Resource Manager (ARM) pour Event Grid déployées sur des clusters Kubernetes avec Azure Arc.

> [!NOTE]
> La préversion du service ne prend en charge qu’une seule instance de l’extension Event Grid sur un cluster Kubernetes, car l’extension Event Grid est actuellement définie en tant qu’extension dans l’étendue du cluster. Il n’existe pas encore de prise en charge des déploiements dans l’étendue de l’espace de noms pour Event Grid, qui permettraient le déploiement de plusieurs instances sur un cluster.  Pour plus d’informations sur les étendues d’extension, consultez [Créer une instance d’extension](../../azure-arc/kubernetes/extensions.md#create-extensions-instance) et recherchez ``scope``.

## <a name="prerequisites"></a>Prérequis
Avant de procéder à l’installation d’Event Grid, assurez-vous que les conditions préalables suivantes sont remplies. 

1. Un cluster s’exécutant sur l’une des [distributions Kubernetes prises en charge](#supported-kubernetes-distributions).
1. [Un abonnement Azure](https://azure.microsoft.com/en-us/free/).
1. Des [certificats PKI](#pki-certificate-requirements) à utiliser pour établir une connexion HTTPS avec le répartiteur Event Grid.
1. [Connectez votre cluster à Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md).

## <a name="getting-support"></a>Obtention d’une assistance
Si vous rencontrez un problème, consultez la section [Résolution des problèmes](#troubleshooting) afin d’obtenir de l’aide sur les conditions courantes. Si vous rencontrez toujours des problèmes, [créez une demande de support Azure](get-support.md#how-to-create-a-support-request).

## <a name="pki-certificate-requirements"></a>Exigences des certificats PKI
Le répartiteur (serveur) Event Grid sert deux types de clients. L’authentification du serveur s’effectue à l’aide de certificats. L’authentification du client s’effectue à l’aide de certificats ou de clés SAP en fonction du type de client.

- Les opérateurs Event Grid qui envoient des demandes de plan de contrôle au répartiteur Event Grid sont authentifiés à l’aide de certificats.
- Les éditeurs Event Grid qui publient des événements dans une rubrique Event Grid sont authentifiés à l’aide des clés SAP de la rubrique.

Afin d’établir une communication HTTPS sécurisée avec le répartiteur Event Grid et l’opérateur Event Grid, nous utilisons des certificats PKI lors de l’installation d’une extension Event Grid. Les exigences générales pour ces certificats PKI sont les suivantes :

1. Les certificats et les clés doivent être des certificats [X.509](https://en.wikipedia.org/wiki/X.509) et encodés PEM [Privacy-Enhanced Mail](https://en.wikipedia.org/wiki/Privacy-Enhanced_Mail).
1. Pour configurer le certificat du répartiteur (serveur) Event Grid lors de l’installation, vous devez fournir les éléments suivants :
    1. Un certificat d’autorité de certification
    1. Un certificat public
    1. Une clé privée
1. Pour configurer le certificat de l’opérateur (client) Event Grid, vous devez fournir :
    1. Un certificat d’autorité de certification
    1. Un certificat public
    1. Une clé privée

    Les clients de publication peuvent utiliser le certificat d’autorité de certification du répartiteur Event Grid pour valider le serveur lors de la publication d’événements dans une rubrique.

    > [!IMPORTANT]
    > Alors qu’un domaine associé au client peut avoir plusieurs certificats publics émis par différentes autorités de certification, Event Grid sur Kubernetes autorise uniquement le chargement d’un seul certificat d’autorité de certification pour les clients lors de l’installation d’Event Grid. Par conséquent, les certificats de l’opérateur Event Grid doivent être émis (signés) par la même autorité de certification afin que la validation de la chaîne de certificats réussisse et qu’une session TLS soit correctement établie.
1. Quand vous configurez le nom commun (CN) pour les certificats de serveur et de client, assurez-vous qu’ils sont différents du CN fourni pour le certificat d’autorité de certification.

    > [!IMPORTANT] 
    > Pour les premières étapes de preuve de concept, des certificats auto-signés pourraient être une option mais, en général, des certificats PKI appropriés signés par une autorité de certification doivent être obtenus et utilisés.

## <a name="install-event-grid-on-kubernetes-extension"></a>Installer l’extension Event Grid sur Kubernetes

1. Sur le portail Azure, recherchez (champ en haut) **Azure Arc**
1. Sélectionnez **Cluster Kubernetes** dans le menu de gauche de la section **Infrastructure**.
1. Dans la liste des clusters, recherchez celui sur lequel vous souhaitez installer Event Grid, puis sélectionnez-le. La page **Vue d’ensemble** du cluster s’affiche.
1. Dans le menu de gauche, dans le groupe **Paramètres**, sélectionnez **Extensions**.
1. Sélectionnez **Ajouter**. Une page affichant les extensions Kubernetes Azure Arc disponibles s’affiche.
1. Sélectionnez **Grille d’événement**.
1. Dans la page Event Grid sur Kubernetes avec Azure Arc, sélectionnez **Créer**.
1. L’onglet **De base** sur la page **Installer Event Grid** s’affiche. La section **Détails du projet** affiche des valeurs d’abonnement et de groupe de ressources en lecture seule, car les extensions Azure Arc sont déployées sous les mêmes abonnement et groupe de ressources que le cluster connecté sur lequel elles sont installées.
1. Entrez un nom dans le champ **Nom de l’extension Event Grid**. Ce nom doit être unique parmi les extensions Azure Arc déployées sur le même cluster Azure Arc connecté.
1. Pour **Espace de noms de mise en production**, vous pouvez fournir le nom d’un espace de noms Kubernetes dans lequel les composants Event Grid seront déployés. La valeur par défaut est **eventgrid-system**. Si l’espace de noms entré n’existe pas, il est créé pour vous.
1. Dans la section des détails du **répartiteur Event Grid**, le type de service est affiché. Le répartiteur Event Grid, qui est le composant qui expose les points de terminaison de rubrique auxquels des événements sont envoyés, est exposé en tant que type de service Kubernetes **ClusterIP**. Par conséquent, les adresses IP attribuées à toutes les rubriques utilisent l’espace d’adressage IP privé configuré pour le cluster.
1. Entrez le **nom de classe de stockage** que vous souhaitez utiliser pour le répartiteur et que votre distribution Kubernetes prend en charge. Par exemple, si vous utilisez AKS, vous pouvez utiliser `azurefile` qui utilise le Stockage Standard Azure. Pour plus d’informations sur les classes de stockage prédéfinies qu’AKS prend en charge, consultez [Classes de stockage dans AKS](../../aks/concepts-storage.md#storage-classes). Si vous utilisez d’autres distributions Kubernetes, consultez la documentation de votre distribution Kubernetes pour connaître les classes de stockage prédéfinies prises en charge ou la manière dont vous pouvez fournir les vôtres.
1. **Taille de stockage**. Par défaut, 1 Gio. Lors de la détermination de la taille de votre stockage, tenez compte du taux d’ingestion. Le taux d’ingestion en Mio/seconde, mesuré en tant que taille de vos événements, multiplié par le taux de publication (événements par seconde) dans toutes les rubriques sur le répartiteur Event Grid est un facteur clé lors de l’allocation de stockage. Les événements étant temporaires par nature, une fois livrés, ils ne consomment plus de stockage. Bien que le taux d’ingestion soit facteur essentiel pour l’utilisation du stockage, il n’est pas le seul. Les métadonnées contenant la configuration de la rubrique et de l’abonnement à l’événement consomment également de l’espace de stockage, mais normalement en quantité moindre que les événements ingérés et livrés par Event Grid.
1. **Limite de mémoire**. Par défaut, 1 Gio. 
1. **Demande de mémoire**. Par défaut, 200 Mio. Ce champ n’est pas modifiable.

    :::image type="content" source="./media/install-k8s-extension/basics-page.png" alt-text="Installer une extension Event Grid – Page De base":::
1. Sélectionnez **Suivant : Configuration** au bas de la page.
1. **Activez la communication HTTP (non sécurisée)** . Cochez cette case si vous souhaitez utiliser un canal non sécurisé lorsque les clients communiquent avec le répartiteur Event Grid.

    > [!IMPORTANT]
    > L’activation de cette option a pour effet que toutes les communications avec le répartiteur Event Grid utilisent le protocole HTTP pour le transport. De ce fait, un client de publication et l’opérateur Event Grid ne communiqueront pas avec le répartiteur Event Grid en toute sécurité. Vous ne devez utiliser cette option que pendant les premières phases de développement.
1. Si vous n’avez pas activé la communication HTTP, sélectionnez chacun des fichiers de certificat PKI que vous avez obtenus, et conformez-vous aux [Exigences des certificats PKI](#pki-certificate-requirements).

    :::image type="content" source="./media/install-k8s-extension/configuration-page.png" alt-text="Installer une extension Event Grid – Page Configuration":::
1. Sélectionnez **Suivant : Surveillance** au bas de la page.
1. **Activez les métriques** en sélectionnant cette option. Event Grid sur Kubernetes expose les métriques pour les rubriques et les abonnements aux événements au [format d’exposition Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/).

    :::image type="content" source="./media/install-k8s-extension/monitoring-page.png" alt-text="Installer une extension Event Grid – Page Surveillance":::    
1. Sélectionnez **Suivant : Étiquettes** pour accéder à la page **Étiquettes**. Définir des [étiquettes](/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging), si nécessaire.

    :::image type="content" source="./media/install-k8s-extension/tags-page.png" alt-text="Installer une extension Event Grid – Page Étiquettes":::
1. Au bas de la page, sélectionnez **Examiner et créer**.
1. Sous l’onglet **Review + create (Vérifier + créer)** , sélectionnez **Créer**.
    
    :::image type="content" source="./media/install-k8s-extension/review-create-page.png" alt-text="Installer une extension Event Grid – Page Vérifier et créer":::   
    
    > [!IMPORTANT]
    > L’installation d’Event Grid est une opération asynchrone qui peut prendre plus de temps sur le cluster Kubernetes que le temps d’affichage d’une notification sur le portail Azure indiquant que le déploiement est terminé. Attendez au moins 5 minutes après avoir vu une notification indiquant que le déploiement est terminé, avant de tenter de créer un emplacement personnalisé (étape suivante). Si vous avez accès au cluster Kubernetes, dans une session bash, vous pouvez exécuter la commande suivante pour vérifier si les pods répartiteur Event Grid et opérateur Event Grid sont dans l’état En cours d’exécution, ce qui indique que l’installation est terminée :

    ```bash
    kubectl get pods -n \<release-namespace-name\>
    ```
    > [!IMPORTANT]
    > Vous devez créer un emplacement personnalisé avant de tenter de déployer des rubriques Event Grid. Pour créer un emplacement personnalisé, vous pouvez sélectionner la page **Contexte** en base 5 minutes après l’affichage de la notification de fin du déploiement. Vous pouvez également créer un emplacement personnalisé à l’aide du [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ExtendedLocation%2FCustomLocations).
1. Une fois le déploiement réussi, vous pouvez voir une entrée sur la page **Extensions** avec le nom que vous avez donné à votre extension Event Grid.

## <a name="troubleshooting"></a>Dépannage

### <a name="azure-arc-connect-cluster-issues"></a>Problèmes de connexion de cluster à Azure Arc

**Problème** : quand j’accède à **Azure Arc** et clique sur **Cluster Kubernetes** dans le menu de gauche, la page affichée n’affiche pas le cluster Kubernetes dans lequel j’ai l’intention d’installer Event Grid.

**Résolution** : votre cluster Kubernetes n’est pas inscrit auprès d’Azure. Suivez les étapes décrites dans l’article [Connecter un cluster Kubernetes existant à Azure Arc](../../azure-arc/kubernetes/quickstart-connect-cluster.md). Si vous rencontrez un problème lors de cette étape, introduisez une [demande de support](#getting-support) auprès de l’équipe Kubernetes avec Azure Arc.

### <a name="event-grid-extension-issues"></a>Problèmes d’extension Event Grid

**Problème** : lorsque vous tentez d’installer une « extension Event Grid », vous recevez le message suivant : « **Opération non valide** -Une instance d’Event Grid a déjà été installée sur ce cluster Kubernetes connecté. L’extension Event Grid est définie au niveau du cluster, ce qui signifie qu’une seule instance peut être installée sur un cluster. »
    
**Explication** : Event Grid est déjà installé. La préversion d’Event Grid ne prend en charge qu’une seule instance d’extension Event Grid déployée sur un cluster.


## <a name="next-steps"></a>Étapes suivantes
Consultez le démarrage rapide [Router des événements cloud vers des Webhooks avec Azure Event Grid sur Kubernetes](create-topic-subscription.md).

