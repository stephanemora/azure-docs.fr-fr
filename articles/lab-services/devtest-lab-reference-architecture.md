---
title: Architecture de référence d’entreprise pour Azure DevTest Labs
description: Cet article fournit des conseils d’architecture de référence pour Azure DevTest Labs dans une entreprise.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 73a3d426e9040525b0c631db273e59c49a6a9eb0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705887"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architecture de référence Azure DevTest Labs pour les entreprises
Cet article présente l’architecture de référence pour vous aider à déployer une solution basée sur Azure DevTest Labs dans une entreprise. Il inclut les éléments suivants :
- Connectivité locale via Azure ExpressRoute
- Une passerelle des services Bureau à distance pour vous connecter à distance aux machines virtuelles
- Connectivité à un référentiel d’artefacts pour les artefacts privés
- Autres services PaaS qui sont utilisés dans les laboratoires

![Diagramme d’architecture de référence](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architecture
Voici les éléments clés de l’architecture de référence :

- **Azure Active Directory (Azure AD)**  : DevTest Labs utilise le [service Azure AD pour la gestion des identités](../active-directory/fundamentals/active-directory-whatis.md). Considérez ces deux aspects clés quand vous donnez aux utilisateurs l’accès à un environnement basé sur DevTest Labs :
    - **Gestion des ressources**: Il fournit un accès au portail Azure pour gérer les ressources (créer des machines virtuelles ; créer des environnements ; Démarrer, arrêter, redémarrer, supprimer et appliquer des artefacts ; et ainsi de suite). Gestion des ressources est effectuée dans Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC). Attribuer des rôles aux utilisateurs et de définir des autorisations au niveau de l’accès et ressources.
    - **Machines virtuelles (au niveau du réseau)**: Dans la configuration par défaut, les machines virtuelles utilisent un compte d’administrateur local. Si un domaine est disponible ([Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), un domaine local ou un domaine basé sur le cloud), les machines peuvent être joints au domaine. Les utilisateurs peuvent ensuite utiliser leurs identités basés sur un domaine pour se connecter aux machines virtuelles.
- **Connectivité locale** : Dans notre diagramme d’architecture, [ExpressRoute](../expressroute/expressroute-introduction.md) est utilisé. Mais vous pouvez également utiliser un [VPN de site à site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Même si ExpressRoute n’est pas requis pour DevTest Labs, il est couramment utilisé dans les entreprises. ExpressRoute est requis uniquement si vous devez accéder aux ressources d’entreprise. Scénarios courants sont :
    - Vous avez des données localement ne peuvent pas être déplacées vers le cloud.
    - Vous souhaitez joindre des machines virtuelles du laboratoire au domaine local.
    - Voulez-vous forcer tout le trafic réseau entrant et sortant de l’environnement de cloud via un pare-feu local pour la sécurité et de conformité.
- **Groupes de sécurité réseau** : Une méthode courante pour limiter le trafic vers l’environnement de cloud (ou dans l’environnement de cloud) en fonction de la source et d’adresses IP de destination consiste à utiliser un [groupe de sécurité réseau](../virtual-network/security-overview.md). Par exemple, vous souhaitez autoriser uniquement le trafic en provenance du réseau d’entreprise dans des réseaux du laboratoire.
- **Passerelle des services Bureau à distance**: En général, les entreprises bloquent sortant les connexions Bureau à distance au niveau du pare-feu d’entreprise. Il existe plusieurs options pour activer la connectivité à l’environnement en nuage dans DevTest Labs, y compris :
  - Utilisez un [passerelle des services Bureau à distance](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)et liste blanche l’adresse IP statique de la passerelle de l’équilibreur de charge.
  - [Dirige tout le trafic RDP entrant](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) via la connexion ExpressRoute/site à site VPN. Cette fonctionnalité est une considération courantes lorsque les entreprises planifier un déploiement de dev/test.
- **Services (réseaux virtuels, sous-réseaux) réseau**: Le [mise en réseau Azure](../networking/networking-overview.md) topologie est un autre élément clé dans l’architecture de dev/test. Elle contrôle si les ressources de laboratoire peuvent communiquer et avoir accès en local et à internet. Notre diagramme d’architecture inclut des méthodes les plus courantes que les clients utilisent DevTest Labs : Tous les laboratoires se connectent via [homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md) en utilisant un [modèle hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) à la connexion ExpressRoute/site à site VPN en local. Mais DevTest Labs utilise réseau virtuel Azure directement, il existe donc aucune restriction sur la configuration de l’infrastructure réseau.
- **Dev/test**:  Dev/test est une partie essentielle de l’architecture globale. Pour en savoir plus sur le service, consultez [sur dev/test](devtest-lab-overview.md).
- **Machines virtuelles et autres ressources (SaaS, PaaS, IaaS)**:  Les machines virtuelles sont une charge de travail clé DevTest Labs prend en charge, ainsi que d’autres ressources Azure. Dev/test rend facile et rapide pour une entreprise fournir l’accès aux ressources Azure (y compris les machines virtuelles et autres ressources Azure). En savoir plus sur l’accès à Azure pour [développeurs](devtest-lab-developer-lab.md) et [testeurs](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité
Bien que dev/test ne dispose pas des limites ou quotas intégrés, les autres ressources Azure qui sont utilisés dans l’opération classique d’un laboratoire ont-elles [quotas du niveau d’abonnement](../azure-subscription-service-limits.md). Dans un déploiement standard de l’entreprise, vous devez donc plusieurs abonnements Azure pour couvrir un large déploiement de dev/test. Les quotas pour les entreprises d’atteindre plus couramment sont :

- **Groupes de ressources** : Dans la configuration par défaut, DevTest Labs crée un groupe de ressources pour chaque nouvel ordinateur virtuel, ou l’utilisateur crée un environnement à l’aide du service. Les abonnements peuvent contenir [980 jusqu'à groupes de ressources](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Par conséquent, c’est la limite de machines virtuelles et d’environnements dans un abonnement. Il existe deux autres configurations que vous devez prendre en compte :
    - **[Accédez de toutes les machines virtuelles au même groupe de ressources](resource-group-control.md)**: Bien que cette configuration vous permet de respecter la limite de groupe de ressources, elle affecte la limite de ressource-type-par-resource-group.
    - **À l’aide de partagé à des adresses IP publiques**: Toutes les machines virtuelles de la même taille et la même région placés dans le même groupe de ressources. Cette configuration est « milieu » entre les quotas de groupes de ressources et les quotas de ressources-type-par-resource-group, si les machines virtuelles sont autorisés à avoir des adresses IP publiques.
- **Groupement de ressources par ressource par type de ressource**: La limite par défaut pour [ressources par groupe de ressources par type de ressource est de 800](../azure-subscription-service-limits.md#resource-group-limits).  Lorsque vous utilisez le *aller de toutes les machines virtuelles au même groupe de ressources* configuration, les utilisateurs atteint cet abonnement limiter beaucoup plus rapidement, surtout si les machines virtuelles présentent de nombreux disques supplémentaires.
- **Comptes de stockage** : Un laboratoire dans DevTest Labs est fourni avec un compte de stockage. Le quota Azure pour [nombre de comptes de stockage par région et par abonnement est de 250](../azure-subscription-service-limits.md#storage-limits). Le nombre maximal de DevTest Labs dans la même région est également 250.
- **Attributions de rôles**: Une attribution de rôle est comment vous procurer un utilisateur ou principal de l’accès à une ressource (propriétaire, ressource, niveau d’autorisation). Dans Azure, il existe un [limite de 2 000 des affectations de rôle par abonnement](../azure-subscription-service-limits.md#role-based-access-control-limits). Par défaut, le service DevTest Labs crée un groupe de ressources pour chaque machine virtuelle. Le propriétaire est accordé *propriétaire* autorisation pour la machine virtuelle DevTest Labs et *lecteur* autorisation au groupe de ressources. De cette façon, chaque nouvelle machine virtuelle que vous créez utilise deux attributions de rôles en plus des affectations qui sont utilisées lorsque vous autorisez les utilisateurs à l’atelier.
- **API lectures/écritures**: Il existe différentes façons d’automatiser Azure et dev/test, y compris les API REST, PowerShell, Azure CLI et Kit de développement. Grâce à l’automatisation, vous risquez d’atteindre une autre limite de demandes d’API : Chaque abonnement autorise un maximum de [lire de 12 000 demandes et 1 200 demandes par heure d’écriture](../azure-resource-manager/resource-manager-request-limits.md). N’oubliez pas de cette limite lors de l’automatisation de DevTest Labs.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion
Dev/test possède une interface utilisateur d’administration approprié pour travailler avec un même laboratoire. Mais dans une entreprise, vous avez probablement plusieurs abonnements Azure et nombreux laboratoires. Apporter des modifications de manière cohérente à tous vos laboratoires nécessite l’écriture de scripts/automation. Voici quelques exemples et les meilleures pratiques de gestion pour un déploiement de dev/test :

- **Modifications apportées aux paramètres de lab**: Un scénario courant consiste à mettre à jour d’un paramètre de laboratoire spécifiques sur tous les laboratoires dans le déploiement. Par exemple, une nouvelle taille d’instance de machine virtuelle est disponible, et tous les laboratoires doivent être mis à jour pour l’autoriser. Il est préférable automatiser ces modifications à l’aide de scripts PowerShell, l’interface CLI ou l’API REST.  
- **Jeton d’accès personnel artefact référentiel**:  En règle générale, les jetons d’accès personnels pour un référentiel Git expirent dans 90 jours, 1 an ou deux ans. Pour garantir la continuité des activités, il est important étendre le jeton d’accès personnel ou créez-en un. Ensuite, utiliser automation pour appliquer le nouveau jeton d’accès personnel à tous les laboratoires.
- **Limiter les modifications à un paramètre de laboratoire**: Souvent, un paramètre particulier doit être limité (par exemple, permettant l’utilisation des images marketplace). Vous pouvez utiliser Azure Policy pour empêcher des modifications à un type de ressource. Ou vous pouvez créer un rôle personnalisé et accorder aux utilisateurs de ce rôle à la place de la *propriétaire* rôle pour le laboratoire. Vous pouvez le faire pour la plupart des paramètres dans le laboratoire (prise en charge interne, annonce du lab, autorisées tailles de machine virtuelle et ainsi de suite).
- **Exiger des machines virtuelles de suivre une convention d’affectation de noms**: Gestionnaires sont souvent amenés à identifier facilement les machines virtuelles qui font partie d’un environnement de test et développement basé sur le cloud. Vous pouvez le faire à l’aide de [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Il est important de noter que DevTest Labs utilise les ressources Azure sous-jacentes qui sont gérés de la même façon : mise en réseau, disques, le calcul et ainsi de suite. Par exemple, Azure stratégie s’applique aux ordinateurs virtuels qui sont créés dans un laboratoire. Azure Security Center peut signaler sur la conformité de la machine virtuelle. Et le service de sauvegarde Azure peut fournir des sauvegardes régulières pour les machines virtuelles dans le laboratoire.

## <a name="security-considerations"></a>Considérations relatives à la sécurité
Azure DevTest Labs utilise les ressources existantes dans Azure (calcul, mise en réseau et ainsi de suite). Par conséquent, il bénéficie automatiquement dans les fonctionnalités de sécurité qui sont intégrées à la plateforme. Par exemple, pour exiger des connexions Bureau à distance entrantes afin qu’il provienne uniquement du réseau d’entreprise, ajoutez simplement un groupe de sécurité réseau au réseau virtuel sur la passerelle des services Bureau à distance. La considération de sécurité supplémentaire uniquement est le niveau d’autorisations que vous accordez aux membres d’équipe qui utilisent les laboratoires au quotidien. Les autorisations les plus couramment sont [ *propriétaire* et *utilisateur*](devtest-lab-add-devtest-user.md). Pour plus d’informations sur ces rôles, consultez [ajouter des propriétaires et des utilisateurs dans Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Étapes suivantes
Voir l’article suivant de cette série : [Mettre à l’échelle de votre infrastructure Azure DevTest Labs](devtest-lab-guidance-scale.md).
