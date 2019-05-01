---
title: FAQ Azure DevTest Labs | Microsoft Docs
description: Trouvez des réponses aux questions les plus fréquentes sur Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: 91c598bde0912cffb8aa1dd7ba022c86a9084faa
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713005"
---
# <a name="azure-devtest-labs-faq"></a>FAQ d’Azure DevTest Labs
Obtenez des réponses aux questions les plus fréquemment posées sur Azure DevTest Labs.

## <a name="blog-post"></a>Billet de blog
Blog de notre équipe de laboratoires DevTest ont été retirée depuis le 20 mars 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Où puis-je suivre dès lors des mises à jour ?
Dès lors, nous allons y publier des mises à jour et billets de blog informatives sur le blog Azure et Azure met à jour. Ces billets de blog seront également liée à notre documentation partout où nécessaire.

S’abonner à la [Blog d’Azure DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) et [DevTest Labs Azure met à jour](https://azure.microsoft.com/updates/?product=devtest-lab) pour rester informé sur les nouvelles fonctionnalités dans DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Que se passe-t-il pour les billets de blog existant ?
Nous travaillons actuellement sur la migration billets de blog existant (à l’exclusion des mises à jour de la panne) pour notre [documentationdev/](devtest-lab-overview.md). Lorsque le blog MSDN est déconseillé, il est redirigé vers la vue d’ensemble de la documentation pour dev/test. Une fois redirigé, vous pouvez rechercher l’article que vous recherchez dans le titre « Filtrer par ». Nous n’avez pas encore effectué la migration toutes ses publications, mais doit être effectuées à la fin du mois en cours. 


### <a name="where-do-i-see-outage-updates"></a>Où voir les mises à jour de la panne ?
Nous allons y publier des mises à jour de panne à l’aide de notre pseudo Twitter à partir de maintenant et versions ultérieures. Suivez-nous sur Twitter pour obtenir les dernières mises à jour sur les pannes et les bogues connus.

### <a name="twitter"></a>Twitter
Notre pseudo Twitter : [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Généralités
### <a name="what-if-my-question-isnt-answered-here"></a>Que dois-je faire si je n’ai pas trouvé de réponse à ma question ici ?
Si votre question n’est pas répertoriée ici, nous dire, donc nous pouvons vous aider à trouver une réponse.

- Postez une question à la fin de ce Forum aux questions. Dialoguez avec l’équipe du Cache Azure et les autres membres de la communauté au sujet de cet article.
- Pour atteindre un public plus large, postez une question sur le [forum MSDN consacré à Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Dialoguez avec l’équipe d’Azure DevTest Labs et les autres membres de la communauté.
- Concernant les demandes et idées de fonctionnalité, envoyez-les à [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Qu’est-ce qu’un compte Microsoft ?
Un compte Microsoft est un compte que vous utilisez pour la plupart des opérations effectuées avec les services et les périphériques Microsoft. Il est une adresse e-mail et le mot de passe que vous utilisez pour vous connecter à Skype, Outlook.com, OneDrive, Windows phone, Azure et Xbox Live. Quand vous utilisez un compte unique, vos fichiers, photos, contacts et paramètres vous suivent sur n’importe quel appareil.
 
> [!NOTE]
> Un compte Microsoft utilisé pour être appelée un Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Pourquoi dois-je utiliser Azure DevTest Labs ?
Azure DevTest Labs peut faire gagner du temps et de l’argent à votre équipe. Les développeurs peuvent créer leurs propres environnements à l’aide de plusieurs bases différentes. Ils peuvent également utiliser des artefacts pour déployer et configurer des applications rapidement. À l’aide de formules et d’images personnalisées, vous pouvez enregistrer des machines virtuelles en tant que modèles et les reproduire facilement au sein de l’équipe. En outre, DevTest Labs offre plusieurs stratégies configurables que les administrateurs de laboratoire peuvent utiliser pour réduire le gaspillage et gérer les environnements d’une équipe. Ces stratégies comprennent l’arrêt automatique, le seuil de coût, le nombre maximal de machines virtuelles par utilisateur et la taille maximale de machine virtuelle. Pour plus de détails sur DevTest Labs, consultez la [présentation](devtest-lab-overview.md) ou la [vidéo d’introduction](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Que signifie « libre-service sans problème » ?
« Libre-service sans problème » signifie que les développeurs et les testeurs créent leurs propres environnements en fonction des besoins. Les administrateurs ont l’assurance que DevTest Labs peut aider à définir l’accès approprié, de réduire le gaspillage et de contrôler les coûts. Les administrateurs peuvent spécifier quelles tailles de machine virtuelle sont autorisées, le nombre maximal de machines virtuelles et le moment où les machines virtuelles sont démarrées et arrêtées. DevTest Labs facilite également la surveillance des coûts et la définition des alertes afin que vous soyez informé sur l’utilisation des ressources du laboratoire.

### <a name="how-can-i-use-devtest-labs"></a>Comment puis-je Azure DevTest Labs ?
Dev/test est utile à chaque fois que vous développement ou environnements de test et souhaitez les reproduire rapidement, ou les gérer à l’aide de stratégies de réduction des coûts.
Voici quelques scénarios pour lesquels nos clients utilisent DevTest Labs :

- Gérer les environnements de développement et de test en un seul emplacement. Utiliser des stratégies pour réduire les coûts et créer des images personnalisées pour partager des builds au sein de l’équipe.
- Développer une application à l’aide d’images personnalisées pour enregistrer l’état du disque tout au long des étapes de développement.
- Suivre les coûts par rapport à la progression.
- Créer des environnements de test en série pour des tests d’assurance qualité.
- Utiliser des artefacts et des formules pour configurer et reproduire facilement une application dans divers environnements.
- Distribuer des machines virtuelles pour des hackathons (travail de test ou de développement collaboratif), puis les déprovisionner facilement quand l’événement se termine.

### <a name="how-am-i-billed-for-devtest-labs"></a>Combien me coûte DevTest Labs ?
DevTest Labs est un service gratuit. La création de laboratoires et la configuration de stratégies, de modèles et d’artefacts dans DevTest Labs ne coûtent rien. Vous payez uniquement pour les ressources Azure utilisées dans vos laboratoires, telles que les machines virtuelles, les comptes de stockage et les réseaux virtuels. Pour plus de détails sur le coût des ressources de laboratoire, consultez la page sur la [tarification Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Sécurité

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quels sont les différents niveaux de sécurité dans DevTest Labs ?
Sécurité d’accès est déterminée par le contrôle d’accès en fonction du rôle (RBAC). Pour savoir comment l’accès est déterminé, vous devez saisir les différences entre une autorisation, un rôle et une étendue, comme défini par RBAC.

- **Permission** : Une autorisation est un accès défini pour une action spécifique. Par exemple, une autorisation peut être en lecture seule pour toutes les machines virtuelles.
- **Rôle** : Un rôle est un jeu d’autorisations qui peuvent être regroupées et attribuées à un utilisateur. Par exemple, un utilisateur détenteur du rôle propriétaire de l’abonnement a accès à toutes les ressources au sein d’un abonnement.
- **Étendue** : Une étendue est un niveau dans la hiérarchie d’une ressource Azure. Par exemple, une étendue peut être un groupe de ressources, un même laboratoire ou l’ensemble de l’abonnement.

Dans l’étendue de DevTest Labs, il existe deux types de rôles qui définissent des autorisations utilisateur :

- **Propriétaire de laboratoire** : Un propriétaire de laboratoire a accès à toutes les ressources du laboratoire. Le propriétaire d’un laboratoire peut modifier les stratégies, lire et écrire sur toutes les machines virtuelles, changer le réseau virtuel et ainsi de suite.
- **Utilisateur de laboratoire** : Un utilisateur de laboratoire peut afficher toutes les ressources de laboratoire, telles que les machines virtuelles, les stratégies et les réseaux virtuels. Toutefois, un utilisateur de laboratoire ne peut pas modifier les stratégies ou toutes les machines virtuelles qui ont été créés par d’autres utilisateurs.

Vous pouvez également créer des rôles personnalisés dans DevTest Labs. Pour découvrir comment créer des rôles personnalisés dans DevTest Labs, consultez [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Étant donné que les étendues sont hiérarchiques, quand un utilisateur dispose d’autorisations pour une certaine étendue, il reçoit automatiquement ces autorisations pour chaque niveau d’étendue inférieur dans l’étendue. Par exemple, si un utilisateur se voit affecter le rôle de propriétaire d’abonnement, il a accès à toutes les ressources dans un abonnement. Ces ressources incluent des machines virtuelles, des réseaux virtuels et des laboratoires. Un propriétaire d’abonnement hérite automatiquement du rôle de propriétaire de laboratoire. Toutefois, l’inverse n’est pas vrai. Un propriétaire de laboratoire a accès à un laboratoire, qui est une étendue inférieure au niveau d’abonnement. Par conséquent, un propriétaire de laboratoire ne peut pas voir les machines virtuelles, réseaux virtuels ou toutes les autres ressources qui sont en dehors du laboratoire.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Comment définir le contrôle d’accès en fonction du rôle pour les environnements DevTest Labs afin de garantir la gouvernance de l’informatique pendant le travail des développeurs/testeurs ?
Il existe un modèle assez large, mais le détail dépend de votre organisation.

Centre informatique doit posséder uniquement ce qui est nécessaire et activer les équipes de projet et d’application pour que le niveau de contrôle nécessaire. En règle générale, cela signifie que le centre informatique possède l’abonnement et gère les fonctions informatiques clés telles que les configurations de mise en réseau. L’ensemble de **propriétaires** pour un abonnement doit être réduit. Ces propriétaires peuvent désigner des propriétaires supplémentaires lorsqu’il est nécessaire, ou appliquer des stratégies de niveau d’abonnement, par exemple « aucune adresse IP publique ».

Un sous-ensemble d’utilisateurs peut nécessiter un accès à un abonnement, comme le support de niveau 1 ou 2. Dans ce cas, nous vous recommandons d’accorder à ces utilisateurs l’accès **contributeur** afin qu’ils puissent gérer les ressources, mais pas attribuer des accès aux utilisateurs ou ajuster les stratégies.

La ressource DevTest Labs doit appartenir à des propriétaires proches de l’équipe de projet/d’application, Il est, car ils comprennent leurs exigences pour les ordinateurs et les logiciels requis. Dans la plupart des organisations, le propriétaire de cette ressource DevTest Labs est souvent le responsable de projet/développement. Ce propriétaire peut gérer les utilisateurs et les stratégies dans l’environnement de laboratoire, ainsi que toutes les machines virtuelles dans l’environnement DevTest Labs.

Membres de l’équipe de projet / d’application doivent être ajoutés à la **utilisateurs DevTest Labs** rôle. Ces utilisateurs peuvent créer des machines virtuelles (alignées sur les stratégies du laboratoire et de l’abonnement). Ils peuvent également gérer leurs propres machines virtuelles. Ils ne peuvent pas gérer les machines virtuelles qui appartiennent à d’autres utilisateurs.

Pour plus d’informations, consultez [structure d’entreprise Azure – documentation de gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Comment créer un rôle pour permettre aux utilisateurs d’effectuer une tâche spécifique ?
Pour savoir comment créer des rôles personnalisés et assigner des autorisations à un rôle, consultez l’article complet [Accorder des autorisations à des utilisateurs sur des stratégies de laboratoire spécifiques](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Voici un exemple de script qui crée le rôle **Utilisateur avancé DevTest Labs**, qui a l’autorisation de démarrer et d’arrêter toutes les machines virtuelles dans le laboratoire :


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Comment une organisation peut-elle veiller à la mise en place des stratégies de sécurité de l’entreprise ?
Une organisation peut s’en charger en prenant les mesures suivantes :

- Élaboration et publication d’une stratégie de sécurité exhaustive. La stratégie énonce les règles d’utilisation acceptable en ce qui a trait à l’utilisation des logiciels et des ressources du cloud. Elle définit également ce qui la contrevient de façon évidente.
- Développer une image personnalisée, des artefacts personnalisés et un processus de déploiement permettant l’orchestration dans le domaine de la sécurité qui est défini avec un répertoire actif. Cette démarche permet de faire respecter les limites de l’entreprise et d’établir un ensemble commun de contrôles en matière d’environnement. Il s’agit des contrôles relatifs à l’environnement qu’un développeur peut prendre en compte dans le cadre de l’élaboration et du suivi d’un cycle de vie de développement sécurisé dans l’ensemble de son processus. L’objectif est également de fournir un environnement qui n’est pas trop restrictif qui peut éventuellement entraver le développement, mais un ensemble raisonnable de contrôles. Les stratégies de groupe de l’unité d’organisation (OU) qui héberge les machines virtuelles du laboratoire peuvent constituer un sous-ensemble de toutes les stratégies de groupe qui se trouvent en production. Elles peuvent également constituer un ensemble supplémentaire permettant de limiter de façon appropriée tous les risques identifiés.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Comment une organisation peut-elle garantir l’intégrité des données de telle sorte que les développeurs distants ne puissent ni supprimer du code ni installer de logiciels malveillants ou non approuvés ?
Il existe plusieurs niveaux de contrôle pour atténuer les risques liés aux consultants externes, aux entrepreneurs ou aux employés qui se connectent à distance afin de collaborer dans DevTest Labs.

Comme indiqué précédemment, la première étape consiste à élaborer et à définir une stratégie d’utilisation acceptable qui décrit clairement les sanctions en cas de violation de celle-ci.

La première couche de contrôles pour l’accès à distance consiste à appliquer une stratégie d’accès à distance via une connexion VPN qui n’est pas connectée directement au laboratoire.

La deuxième couche de contrôles consiste à appliquer un ensemble d’objets de stratégie de groupe qui empêchent de copier-coller sur le bureau distant. Une stratégie de réseau peut être implémentée afin de ne pas autoriser les services sortants de l’environnement comme les services FTP et RDP ne faisant pas partie de l’environnement. Routage défini par l’utilisateur peut forcer tout le trafic réseau Azure sur le site local, mais le routage n’a pas pu compte pour toutes les URL qui peuvent autoriser le téléchargement de données, sauf si contrôlée via un proxy pour analyser le contenu et les sessions. Les adresses IP publiques peuvent être restreintes au sein du réseau virtuel prenant en charge DevTest Labs afin d’éviter le pontage d’une ressource réseau externe.

Au final, le même type de restrictions doit s’appliquer à l’organisation, ce qui expliquerait pour toutes les méthodes possibles d’un support amovible ou d’une URL externes pouvant acceptent une publication de contenu. Consultez votre professionnel de la sécurité afin d’examiner et de mettre en œuvre une stratégie en matière de sécurité. Pour obtenir davantage de suggestions, consultez la section [Cybersécurité Microsoft](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Configuration du laboratoire

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Comment puis-je créer un laboratoire à partir d’un modèle Resource Manager ?
Nous offrons un [dépôt GitHub de modèles Azure Resource Manager de laboratoire](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) que vous pouvez déployer tels quels ou modifier pour créer des modèles personnalisés pour vos laboratoires. Chaque modèle est un lien pour déployer le laboratoire comme il se trouve dans votre propre abonnement Azure. Ou bien, vous pouvez personnaliser le modèle et [effectuer le déploiement à l’aide de PowerShell ou de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Puis-je avoir toutes les machines virtuelles doit être créé dans un groupe de ressources communs ayant à la place de chaque ordinateur dans son propre groupe de ressources ? 
Oui, en tant que propriétaire d’un laboratoire, vous pouvez laisser gérer l’allocation de groupe de ressources pour vous le laboratoire ou tous les ordinateurs virtuels créés dans un groupe de ressources communs que vous spécifiez. 

Scénario de groupe de ressources distincts :
-   DevTest Labs crée un nouveau groupe de ressources pour chaque machine virtuelle IP publique/privée que vous faites tourner
-   DevTest Labs crée un groupe de ressources pour les machines IP partagés qui appartiennent à la même taille.

Scénario courant du groupe de ressources :
-   Toutes les machines virtuelles sont lancées dans le groupe de ressources courantes que vous spécifiez. En savoir plus [allocation de groupe de ressources pour le laboratoire](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Comment gérer une convention d’affectation de noms au sein de mon environnement DevTest Labs ?
Vous souhaitez peut-être appliquer les conventions d’affectation de noms actuelles aux opérations d’Azure, et les rendre cohérentes au sein de l’environnement DevTest Labs. Lors du déploiement de DevTest Labs, nous vous recommandons de disposer de stratégies de démarrage spécifiques. Pour déployer ces stratégies, vous utilisez un script central et des modèles JSON qui permettent d’assurer la cohérence. Les stratégies d’affectation de noms peuvent être implémentées par le biais des stratégies Azure appliquées au niveau de l’abonnement. Pour accéder à des exemples JSON pour Azure Policy, consultez la section [Exemples Azure Policy](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Combien de laboratoires puis-je créer sous le même abonnement ?
Il n’existe pas de limite spécifique concernant le nombre de laboratoires qui peuvent être créés par abonnement. Toutefois, la quantité de ressources utilisées par abonnement est limitée. Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites associés aux abonnements Azure](../azure-subscription-service-limits.md) et sur [la façon d’augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Combien de machines virtuelles puis-je créer par laboratoire ?
Il n’existe aucune limite spécifique concernant le nombre de machines virtuelles qui peuvent être créées par laboratoire. Toutefois, les ressources (cœurs de machines virtuelles, adresses IP publiques, etc.) utilisées sont limitées par abonnement. Vous pouvez consulter des articles pour en savoir plus sur les [quotas et les limites associés aux abonnements Azure](../azure-subscription-service-limits.md) et sur [la façon d’augmenter ces limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Comment déterminer le pourcentage d’utilisateurs par le laboratoire et le nombre total de travaux pratiques qui sont nécessaires dans une organisation ?
Nous recommandons que les unités commerciales et les groupes de développement associés au même projet de développement soient rattachés au même laboratoire. Cela permet d’appliquer les mêmes types de stratégies, d’images et de stratégies d’arrêt aux deux groupes.

Vous serez peut-être amené à prendre en compte des limites géographiques. Par exemple, les développeurs dans le nord East United States (États-Unis) peuvent utiliser un laboratoire de mise en service dans l’est des États-Unis 2. Par ailleurs, les développeurs de Dallas (Texas) et Denver (Colorado) peuvent être redirigés afin d’utiliser une ressource de USA Centre Sud. S’il existe un travail collaboratif avec un tiers externe, les développeurs peuvent être affectés à un laboratoire qui n’est pas utilisé par les développeurs internes.

Vous pouvez également utiliser un laboratoire pour un projet spécifique au sein d’Azure DevOps Projects. Vous appliquez ensuite la sécurité via un groupe Azure Active Directory spécifié, qui autorise l’accès aux deux ensembles de ressources. Le réseau virtuel attribué au laboratoire peut constituer une autre limite permettant de consolider les utilisateurs.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Comment empêcher la suppression des ressources au sein d’un laboratoire ?
Nous vous recommandons de définir des autorisations appropriées au niveau du laboratoire afin que seuls les utilisateurs autorisés puissent supprimer des ressources ou modifier les stratégies de laboratoire. Les développeurs doivent être placés dans le groupe **DevTest Labs Users** (Utilisateurs DevTest Labs). Le développeur en chef ou le responsable de l’infrastructure doit correspondre au titre de **DevTest Labs Owner** (Propriétaire DevTest Labs). Nous recommandons que vous avez uniquement deux propriétaires de laboratoire. Cette stratégie s’étend au référentiel de code pour éviter toute corruption. Les utilisateurs de laboratoire ont des droits d’utilisation des ressources, mais ne peut pas mettre à jour des stratégies de laboratoire. Consultez l’article suivant, qui répertorie les rôles et les droits dont chaque groupe intégré dispose au sein d’un laboratoire : [Ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Comment partager un lien direct vers mon laboratoire ?

1. Dans le [Azure portal](https://portal.azure.com), accédez au laboratoire.
2. Copie le **URL lab** à partir de votre navigateur, puis du partager avec vos utilisateurs de laboratoire.

> [!NOTE]
> Si un utilisateur de laboratoire est un utilisateur externe qui a un compte Microsoft, mais qui n’est pas un membre d’instance d’Active Directory de votre organisation, l’utilisateur peut voir un message d’erreur quand ils tentent d’accéder au lien partagé. Si un utilisateur externe voit un message d’erreur, demandez-lui de sélectionner d’abord son nom dans le coin supérieur droit du portail Azure. Puis, dans la section de répertoire du menu, l’utilisateur peut sélectionner le répertoire où se trouve le laboratoire.

## <a name="virtual-machines"></a>Machines virtuelles

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Pourquoi ne puis-je pas voir les machines virtuelles sur la page de Machines virtuelles que je vois que dans DevTest Labs ?
Lorsque vous créez une machine virtuelle dans DevTest Labs, vous êtes donné l’autorisation d’accéder à cette machine virtuelle. Vous pouvez afficher la machine virtuelle dans la page de laboratoires et dans le **Machines virtuelles** page. Les utilisateurs affectés à la **DevTest Labs propriétaire** rôle peut voir toutes les machines virtuelles qui ont été créés dans le laboratoire dans le laboratoire **toutes les Machines virtuelles** page. Toutefois, les utilisateurs qui ont le **utilisateur de DevTest Labs** rôle ne reçoivent pas automatiquement un accès en lecture aux ressources de machine virtuelle que les autres utilisateurs ont créé. Par conséquent, ces machines virtuelles ne sont pas affichés sur la **Machines virtuelles** page.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Comment puis-je créer plusieurs machines virtuelles à partir du même modèle en une seule fois ?
Vous avez deux options pour créer simultanément plusieurs machines virtuelles à partir du même modèle :

- Vous pouvez utiliser [l’extension Azure DevOps Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Vous pouvez [générer un modèle Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) pendant que vous créez une machine virtuelle, et [déployer le modèle Resource Manager à partir de Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- Vous pouvez également spécifier plusieurs instances d’un ordinateur doit être créé lors de la création de machine virtuelle. Pour en savoir plus sur la création de plusieurs instances de machines virtuelles, consultez la documentation sur [création d’une machine virtuelle de laboratoire](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Comment puis-je déplacer mes machines virtuelles Azure existantes dans mon laboratoire DevTest Labs ?
Pour copier vos machines virtuelles existantes vers DevTest Labs :

1.  Copiez le fichier de disque dur virtuel de votre machine virtuelle existante à l’aide d’un [script Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Créer le [image personnalisée](devtest-lab-create-template.md) dans votre laboratoire DevTest Labs.
3.  Créez une machine virtuelle dans le laboratoire à partir de votre image personnalisée.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Puis-je attacher plusieurs disques à mes machines virtuelles ?

Oui, vous pouvez attacher plusieurs disques à vos machines virtuelles.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Si je souhaite utiliser une image de système d’exploitation Windows pour mon test, dois-je acheter un abonnement MSDN ?
Pour utiliser des images de système d’exploitation client Windows (Windows 7 ou une version ultérieure) pour le développement ou de test dans Azure, effectuez l’une des étapes suivantes :

- [Achetez un abonnement MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Si vous disposez d’un contrat Entreprise, créez un abonnement Azure avec [l’offre Entreprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p).

Pour plus d’informations sur les crédits Azure pour chaque offre de MSDN, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Comment puis-je automatiser le processus de suppression de toutes les machines virtuelles dans mon laboratoire ?
En tant que propriétaire d’un laboratoire, vous pouvez supprimer des machines virtuelles à partir de votre laboratoire dans le portail Azure. Vous pouvez également supprimer toutes les machines virtuelles de votre laboratoire à l’aide d’un script PowerShell. Dans l’exemple suivant, sous le **pour modifier les valeurs** commentaire, modifiez les valeurs de paramètre. Vous pouvez récupérer le subscriptionId, labResourceGroup et les valeurs de labName à partir du volet de laboratoire dans le portail Azure.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Environnements 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Comment puis-utiliser des modèles Resource Manager dans mon environnement DevTest Labs ?
Vous déployez vos modèles Resource Manager dans un environnement de DevTest Labs à l’aide des étapes mentionnées dans le [fonctionnalité environnements dev/test](devtest-lab-test-env.md) article. Globalement, vous intégrez vos modèles Resource Manager dans un référentiel Git (Azure Repos ou GitHub), et ajoutez un [référentiel privé pour vos modèles](devtest-lab-test-env.md) au labo. Ce scénario ne peut pas être utile si vous utilisez DevTest Labs pour héberger des ordinateurs de développement, mais peut être utile si vous créez un environnement intermédiaire, qui est représentatif de la production.

Il est également important de noter que le nombre de machines virtuelles par laboratoire ou par l’option utilisateur limite uniquement le nombre d’ordinateurs créés en mode natif dans le lab proprement dit et non par les environnements (modèles Resource Manager).

## <a name="custom-images"></a>Images personnalisées

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Comment puis-je créer un processus facilement répétable pour intégrer mes images organisationnelles personnalisées dans un environnement DevTest Labs ?
Consultez ce [vidéo sur le modèle de fabrique d’images](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Ce scénario est avancé et les scripts fournis le sont à titre d’exemple. Si des modifications s’imposent, vous devez gérer et actualiser les scripts utilisés dans votre environnement.

Pour plus d’informations sur la création d’une fabrique d’images, consultez [créer une fabrique d’images personnalisées dans Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Quelle est la différence entre une image personnalisée et une formule ?
Une image personnalisée est une image managée. Une formule est une image que vous pouvez configurer avec des paramètres supplémentaires, puis enregistrer et reproduire. Une image personnalisée peut être préférable si vous souhaitez créer rapidement plusieurs environnements à l’aide de la même image de base, immuable. Une formule peut être préférable si vous souhaitez reproduire la configuration de votre machine virtuelle avec les tout derniers bits, dans le cadre d’un sous-réseau ou d’un réseau virtuel ou pour obtenir une machine virtuelle d’une taille spécifique. Pour plus de détails, consultez [Comparaison entre les images personnalisées et les formules dans DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Quand dois-je utiliser une formule ou une image personnalisée ?
En général, les facteurs décisifs dans ce scénario sont le coût et la réutilisation. Si vous avez un scénario où plusieurs utilisateurs/ateliers une image avec un grand nombre de logiciels en plus de l’image de base, vous pouvez réduire les coûts en créant une image personnalisée. Cela signifie que l’image est créée une seule fois. Elle réduit la durée de configuration de la machine virtuelle et le coût lié à l’exécution de la machine virtuelle pendant la configuration.

Cependant, la fréquence des modifications apportées à votre package logiciels est un facteur supplémentaire à prendre en compte. Si vous générez des builds quotidiennes et s’il faut que les logiciels résident sur les machines virtuelles de vos utilisateurs, envisagez d’utiliser une formule au lieu d’une image personnalisée.

Pour obtenir une explication plus détaillée, consultez [comparaison des images personnalisées et formules](devtest-lab-comparing-vm-base-image-types.md) dans DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Comment puis-je automatiser le processus de téléchargement des fichiers de disque dur virtuel pour créer des images personnalisées ?

Pour automatiser le chargement des fichiers de VHD afin de créer des images personnalisées, vous avez deux options :

- Utiliser [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) pour copier ou charger des fichiers de VHD sur le compte de stockage qui est associé au laboratoire.
- Utiliser [l’explorateur de stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). L’Explorateur de stockage est une application autonome qui s’exécute sur Windows, OSX et Linux.

Pour trouver le compte de stockage de destination associé à votre laboratoire :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com).
2.  Dans le menu de gauche, sélectionnez **Groupes de ressources**.
3.  Recherchez et sélectionnez le groupe de ressources associé à votre laboratoire.
4.  Sous **Vue d’ensemble**, sélectionnez l’un des comptes de stockage.
5.  Sélectionnez **Objets Blob**.
6.  Recherchez les téléchargements dans la liste. S’il n’y en a aucun, revenez à l’étape 4 et essayez un autre compte de stockage.
7.  Utilisez **l’URL** en tant que destination dans votre commande AzCopy.

Quand dois-je utiliser une image Place de marché Microsoft Azure ou une image organisationnelle personnalisée ?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Quand dois-je utiliser une image Place de marché Microsoft Azure ou une image organisationnelle personnalisée ?
Par défaut, utilisez une image Place de marché Azure, sauf si vous avez des impératifs particuliers ou des exigences organisationnelles. Voici quelques exemples communs :

- Configuration logicielle complexe qui requiert l’intégration d’une application dans l’image de base.
- Installation et configuration d’une application peut prendre plusieurs heures, ce qui ne sont pas d’une utilisation efficace des temps de calcul doit être ajouté sur une image de place de marché Azure.
- Les développeurs et les testeurs ont besoin d’accéder rapidement à une machine virtuelle et souhaitent minimiser la durée de configuration d’une nouvelle machine virtuelle.
- Conformité ou conditions réglementaires (par exemple, stratégies de sécurité) qui doivent être en place pour toutes les machines.
- Utilisez des images personnalisées ne doivent pas être considéré comme à la légère. Ils introduisent une complexité supplémentaire, car vous devez désormais gérer les fichiers de disque dur virtuel à ces sous-jacents des images de base. Vous devez également appliquer des mises à jour logicielles à ces images de base. Celles-ci incluent les nouvelles mises à jour du système d’exploitation ainsi que toutes les mises à jour ou modifications de configuration nécessaires au package logiciel lui-même.

## <a name="artifacts"></a>Artefacts

### <a name="what-are-artifacts"></a>Que sont les artefacts ?
Les artefacts sont des éléments personnalisables que vous pouvez utiliser pour déployer vos tout derniers bits ou déployer vos outils de développement sur une machine virtuelle. Attachez les artefacts à votre machine virtuelle quand vous créez celle-ci. Une fois la machine virtuelle approvisionnée, les artefacts la déploient et la configurent. Divers artefacts préexistants sont disponibles dans notre [dépôt GitHub public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Vous pouvez également [créer vos propres artefacts](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mon artefact a échoué lors de la création d’une machine virtuelle. Comment puis-je résoudre ce problème ?
Pour savoir comment récupérer les journaux d’activité relatifs à l’échec d’un artefact, consultez [Guide pratique pour diagnostiquer les échecs d’artefacts dans DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Quand une organisation doit-elle utiliser un référentiel d’artefacts public/privé dans DevTest Labs ?
Le [référentiel d’artefacts public](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fournit un ensemble initial de progiciels les plus couramment utilisés. Il permet un déploiement rapide sans avoir besoin de consacrer beaucoup de temps à la reproduction d’outils de développement et de compléments courants. Vous pouvez choisir de déployer leur propre référentiel privé. Vous pouvez utiliser un référentiel public avec un référentiel privé en parallèle. Vous pouvez également choisir de désactiver le référentiel public. Les critères de déploiement d’un référentiel privé doivent reposer sur les questions et les considérations suivantes :

- L’organisation doit-elle disposer de logiciels d’entreprise sous licence dans le cadre de son offre DevTest Labs ? Dans l’affirmative, un référentiel privé doit être créé.
- L’organisation développe-t-elle un logiciel personnalisé assurant une fonction particulière nécessaire dans le cadre du processus général d’approvisionnement ? Dans l’affirmative, un référentiel privé doit être déployé.
- Si la stratégie de gouvernance de l’organisation, une isolation et les dépôts externes ne sont pas sous gestion de la configuration directe par l’organisation, un dépôt d’artefacts privés doit être déployé. Dans le cadre de ce processus, il est possible de copier une version initiale du référentiel public et de l’intégrer au référentiel privé. Le référentiel public peut ensuite être désactivé afin que personne ne puisse plus y accéder au sein de l’organisation. Cette approche oblige tous les utilisateurs de l’organisation à ne disposer que d’un seul référentiel approuvé par l’organisation et à limiter la dérive des configurations.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Une organisation doit-elle planifier un référentiel unique ou autoriser plusieurs référentiels ?
Dans le cadre de la stratégie générale de gouvernance et de la gestion des configurations au sein de votre organisation, nous vous recommandons d’utiliser un référentiel centralisé. Lorsque plusieurs référentiels sont utilisés, ils peuvent au fil du temps se transformer en silos de logiciels non managés. Avec un référentiel central, plusieurs équipes peuvent utiliser les artefacts de ce référentiel pour leurs projets. Il renforce la normalisation, la sécurité et la facilité de gestion. Cela permet également d’éviter de faire double emploi. Dans le cadre de la centralisation, nous recommandons les mesures suivantes pour la gestion et la viabilité à long terme :

- Associez Azure Repos au même locataire Azure Active Directory que celui utilisé par l’abonnement Azure pour l’authentification et l’autorisation.
- Créez un groupe nommé `All DevTest Labs Developers` dans Azure Active Directory qui est géré de manière centralisée. Tous les développeurs qui contribuent au développement d’artefacts doivent être placés dans ce groupe.
- Le même groupe Azure Active Directory peut être utilisé pour donner accès au dépôt Azure Repos et au lab.
- Dans Azure Repos, la création de branches ou la duplication doit permettre de séparer un dépôt de développement du dépôt de production principal. Le contenu n’est ajouté à la branche maître qu’avec une requête d’extraction après un examen approprié du code. Une fois la modification approuvée par l’examinateur du code, le code actualisé est fusionné par un développeur principal responsable de la maintenance de la branche maître.

## <a name="cicd-integration"></a>Intégration CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>DevTest Labs est-il intégré à ma chaîne d’outils CI/CD ?
Si vous utilisez Azure DevOps, vous pouvez utiliser un [extension de DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) pour automatiser votre pipeline de versions dans DevTest Labs. Voici quelques-unes des tâches que vous pouvez effectuer avec cette extension :

- Créer et déployer automatiquement une machine virtuelle. Vous pouvez également configurer la machine virtuelle avec la build la plus récente en utilisant des tâches Copie de fichiers Azure, PowerShell ou Azure DevOps Services.
- Capturer automatiquement l’état d’une machine virtuelle après le test, afin de reproduire un bogue sur la même machine virtuelle pour un examen approfondi.
- Supprimer la machine virtuelle à la fin du pipeline de versions, quand il n’est plus nécessaire.

Les billets de blog suivants offrent des conseils et des informations sur l’utilisation de l’extension Azure DevOps Services :

- [DevTest Labs and the Azure DevOps extension](integrate-environments-devops-pipeline.md) (DevTest Labs et extension Azure DevOps)
- [Deploy a new VM in an existing DevTest Labs lab from Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) (Déploiement d’une nouvelle machine virtuelle dans un labo DevTest Labs à partir d’Azure DevOps Services)
- [Using Azure DevOps Services release management for continuous deployments to DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) (Utilisation de la gestion des mises en production Azure DevOps Services pour des déploiements en continu vers DevTest Labs)

Pour d’autres chaînes d’outils d’intégration continue (CI)/livraison continue (CD), vous pouvez réaliser les mêmes scénarios en déployant des [modèles Azure Resource Manager](https://azure.microsoft.com/resources/templates/) à l’aide des [applets de commande Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) et des [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). De plus, vous pouvez utiliser les [API REST pour DevTest Labs](https://aka.ms/dtlrestapis) pour une intégration à votre chaîne d’outils.

## <a name="networking"></a>Mise en réseau

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Quand dois-je créer un réseau virtuel pour mon environnement DevTest Labs et quand dois-je utiliser un réseau virtuel existant ?
Si vos machines virtuelles doivent interagir avec l’infrastructure existante, envisagez d’utiliser un réseau virtuel existant à l’intérieur de votre environnement de dev/test. Si vous utilisez ExpressRoute, vous souhaiterez réduire la quantité de réseaux virtuels / sous-réseaux afin que vous ne pas fragmenter votre espace d’adressage IP qui est affecté pour une utilisation dans les abonnements. 

Envisagez d’utiliser le modèle de l’homologation de réseau virtuel ici ([Hub-and-Spoke modèle](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) trop. Cette approche permet une communication de réseau virtuel/sous-réseau via les abonnements. Sinon, chaque environnement DevTest Labs pourrait avoir son propre réseau virtuel. 

Il existe [limites](../azure-subscription-service-limits.md) sur le nombre de réseaux virtuels par abonnement. Le nombre par défaut est de 50, mais cette limite peut être portée à 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Quand dois-je utiliser une adresse IP partagée, une adresse IP publique ou une adresse IP privée ?
 
Si vous utilisez un VPN de site à site ou Express Route, privilégiez les adresses IP privées pour que vos machines soient accessibles via votre réseau interne et inaccessibles par l’Internet publiques.

> [!NOTE]
> Les propriétaires de labo peuvent modifier cette stratégie de sous-réseau pour que personne ne crée accidentellement d’adresses IP publiques pour ses machines virtuelles. Le propriétaire de l’abonnement doit créer une stratégie d’abonnement empêchant la création d’adresses IP publiques.

Si vous utilisez des adresses IP publiques partagées, les machines virtuelles d’un labo partagent une adresse IP publique. Cette approche peut être utile pour éviter de dépasser les nombres limites d’adresses IP publiques pour un abonnement donné.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Comment puis-je m’assurer que les machines virtuelles de développement et de test sont incapables d’accéder à l’Internet public ? Y a-t-il des recommandations à suivre pour configurer le réseau ?

Oui. Deux aspects sont à prendre en compte : le trafic entrant et le trafic sortant.

- **Le trafic entrant** : si la machine virtuelle n’a pas une adresse IP publique, puis il n’est pas accessible par internet. Une approche courante consiste à vous assurer qu’une stratégie au niveau de l’abonnement est définie, telles qu’aucun utilisateur ne puisse créer une adresse IP publique.
- **Le trafic sortant** : Si vous souhaitez empêcher les machines virtuelles accéder directement à internet public et forcer le trafic via un pare-feu d’entreprise, puis vous pouvez acheminer le trafic local via express route ou VPN, à l’aide de forcé routage.

> [!NOTE]
> Si vous avez un serveur proxy qui bloque le trafic sans paramètres de proxy, n’oubliez pas d’ajouter des exceptions pour le compte de stockage du laboratoire artefact.

Vous pouvez également utiliser des groupes de sécurité réseau pour les machines virtuelles ou les sous-réseaux. Cette étape ajoute un niveau supplémentaire de protection pour autoriser ou bloquer le trafic.

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Pourquoi mon réseau virtuel existant n’est pas enregistré correctement ?
Il se peut que votre nom de réseau virtuel contienne des points. Dans ce cas, essayez de supprimer les points ou de les remplacer par des traits d’union. Ensuite, réessayez d’enregistrer le réseau virtuel.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Pourquoi l’erreur signalant que la ressource parente est introuvable s’affiche-t-elle quand j’approvisionne une machine virtuelle à partir de PowerShell ?
Quand une ressource est parent d’une autre ressource, la ressource parente doit exister pour que vous puissiez créer la ressource enfant. Si la ressource parente n’existe pas, vous voyez un **ParentResourceNotFound** message. Si vous ne spécifiez pas de dépendance sur la ressource parente, la ressource enfant peut être déployée avant cette dernière.

Les machines virtuelles sont des ressources enfants se trouvant dans un laboratoire d’un groupe de ressources. Quand vous utilisez des modèles Resource Manager pour déployer des machines virtuelles à l’aide de PowerShell, le nom du groupe de ressources fourni dans le script PowerShell doit être le nom du groupe de ressources du laboratoire. Pour plus d’informations, consultez la rubrique [Résolution des erreurs courantes dans des déploiements Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Où puis-je trouver plus d’informations d’erreur si le déploiement d’une machine virtuelle échoue ?
Les erreurs de déploiement de machine virtuelle sont capturées dans des journaux d’activité. Vous pouvez trouver de laboratoire de journaux d’activité de machine virtuelle sous **journaux d’Audit** ou **diagnostics de machine virtuelle** dans le menu de ressources sur la page de machine virtuelle du laboratoire (la page s’affiche après avoir sélectionné la machine virtuelle à partir de la liste Mes machines virtuelles).

Parfois, l’erreur de déploiement se produit avant que ne commence le déploiement de la machine virtuelle. C’est par exemple le cas quand la limite d’abonnement pour une ressource qui a été créée avec la machine virtuelle est dépassée. Dans ce cas, les détails de l’erreur sont capturés dans les journaux d’activité au niveau du laboratoire. Les journaux d’activité sont situés en bas des paramètres **Configuration et stratégies**. Pour plus d’informations sur l’utilisation des journaux d’activité dans Azure, consultez [Afficher les journaux d’activité pour auditer les actions sur les ressources](../azure-resource-manager/resource-group-audit.md).




