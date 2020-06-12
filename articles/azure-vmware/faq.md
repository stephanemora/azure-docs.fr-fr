---
title: Forum aux questions
description: Apporte des réponses à des questions récurrentes à propos de la solution Azure VMware (AVS).
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 1649b5649bd18b7ab53f3cc0196d7dff0f6f5b2c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112683"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Forum aux questions sur la préversion de la solution VMware Azure (AVS)

Réponses à des questions fréquemment posées sur la solution VMware Azure (AVS)

## <a name="general"></a>Général

**Qu’est-ce que la solution Azure VMware (AVS) ?**

Tandis que les entreprises mettent en œuvre des stratégies de modernisation informatique pour améliorer la réactivité, réduire les coûts et accélérer l’innovation, les plateformes cloud hybrides apparaissent comme des activateurs clés de la transformation numérique des clients. AVS combine le logiciel SDDC (Software Defined Data Center) de VMware avec l’écosystème du service cloud global de Microsoft Azure. La solution est managée pour satisfaire à une série d’exigences en matière de performances, de disponibilité, de sécurité et de conformité.

## <a name="avs-service"></a>Service AVS

**Où la solution AVS est-elle disponible aujourd’hui ?**

Durant la période de préversion, elle est disponible dans la région USA Est en Amérique du Nord et à Amsterdam en Europe de l’Ouest.

**Des charges de travail exécutées dans une instance AVS peuvent-elles utiliser des services Azure ou s’intégrer avec ceux-ci ?**

Tous les services Azure sont à la disposition des clients de la solution AVS. Les limitations de performances et de disponibilité pour des services spécifiques doivent être traitées au cas par cas.

**Dois-je utiliser les mêmes outils que ceux que j’utilise actuellement pour gérer des ressources de cloud privé ?**

Oui. Le portail Azure est utilisé pour le déploiement et un certain nombre d’opérations de gestion. vCenter et NSX Manager sont utilisés pour gérer les ressources vSphere et NSX-T.

**Puis-je gérer un cloud privé avec mon serveur VMware vCenter local ?**

Au lancement, AVS ne prend pas en charge une expérience de gestion unique dans des environnements de cloud local et privé. Les clusters de clouds privés sont gérés avec les logiciels vCenter et NSX Manager locaux d’un cloud privé.

**Puis-je exécuter la solution vRealize Suite en local ?** 

Les intégrations et cas d’usage spécifiques peuvent être évalués individuellement.

**Puis-je migrer des machines virtuelles vSphere à partir d’environnements locaux vers des clouds privés AVS ?**

Oui. Vous pouvez utiliser une migration de machine virtuelle et vMotion pour déplacer des machines virtuelles vers un cloud privé si les configurations requises standard croisées de vCenter et de [vMotion] [https://kb.vmware.com/s/article/210695 ] sont réunies.

**Une version spécifique de vSphere est-elle requise dans des environnements locaux ?**

Dans la mesure où tous les environnements cloud sont fournis avec HCX, le logiciel vSphere 5.5 ou version ultérieure est requis dans des environnements locaux pour vMotion.

**À quoi ressemble le processus de contrôle des modifications ?**

Les mises à jour apportées au service proprement dit suivent le processus de gestion des modifications standard de Microsoft Azure. Les clients sont responsables des tâches d’administration de la charge de travail, ainsi que des processus de gestion des modifications associés.

**Quelle est la différence avec Azure VMware Solution by CloudSimple ?**

Avec la nouvelle solution Azure VMware, Microsoft et VMware sont partenaires directs pour la prestation de services cloud. La nouvelle solution est entièrement conçue et prise en charge par Microsoft, et approuvée par VMware. Du point de l’architecture, les solutions sont cohérentes, la pile de technologies VMware s’exécutant sur une infrastructure dédiée Azure.

**Si je suis déjà client de la solution Azure VMware, quelles sont les implications de cette préversion ?**

La solution Azure VMware Solution by CloudSimple ne subit aucune modification. Nous continuons à la prendre en charge sur Azure. La solution Azure VMware s’appuie sur notre contrat de niveau de service ([contrat SLA](https://aka.ms/CSVMwareSLA)). Les clients doivent continuer à utiliser le service pour les charges de travail de production ; il s’agit d’une solution disponible régie par les [conditions du service Microsoft](https://azure.microsoft.com/support/legal/).

**Puis-je migrer de la solution Azure VMware Solution by CloudSimple vers cette nouvelle solution ?**

Oui, la solution Azure VMware prend en charge la migration à l’aide d’outils VMware bien connus comme HCX. Si la migration vers la nouvelle solution vous intéresse, contactez votre équipe de compte Microsoft pour explorer les options et le support disponible.



## <a name="compute-network-and-storage"></a>Calcul, réseau et stockage

**Plusieurs types d’hôtes sont-ils disponibles ?**

Il n’existe qu’un seul type d’hôte disponible.

**Quelles sont les spécifications d’UC dans chaque type d’hôte ?**

Les serveurs sont équipés de deux processeurs Intel 18 cœurs cadencés à 2,3 GHz.

**Quelle est la quantité de mémoire de chaque hôte ?**

Les serveurs disposent de 576 Go de RAM.

**Quelle est la capacité de stockage de chaque hôte ?**

Chaque hôte ESXi est doté de deux groupes de disques VSAN d’une capacité de 15,2 To, et d’un cache NVMe de 3,2 To (1,6 To dans chaque groupe de disques).

**Quelle est la quantité de bande passante réseau disponible dans chaque hôte ESXi ?**

Les hôtes ESXi prennent en charge une bande passante de connectivité jusqu’à 25 Gbit/s.

**Les données stockées dans les magasins de données VSAN sont-elles chiffrées au repos ?**

Oui, toutes les données VSAN sont chiffrées par défaut à l’aide de clés stockées dans Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hôtes, clusters et clouds privés

**L’infrastructure sous-jacente est-elle partagée ?**

Non, les clusters et hôtes de cloud privé sont dédiés, et effacés en toute sécurité avant et après usage.

**Quels sont les nombres minimal et maximal d’hôtes par cluster ?**

L’échelle possible des clusters est comprise entre 3 et 16 hôtes ESXi. Les clusters d’évaluation sont limités à trois hôtes.

**Puis-je mettre à l’échelle mes clusters de clouds privés ?**

Oui, les clusters se mettent à l’échelle entre les nombres minimal et maximal d’hôtes ESXi. Les clusters d’évaluation sont limités à trois hôtes.

**Que sont les clusters d’évaluation ?**

Les clusters d’évaluation sont des clusters de trois hôtes utilisés pour effectuer des évaluations d’un mois de clouds privés AVS.

**Puis-je utiliser des hôtes haut de gamme pour des clusters d’évaluation ?**

Non. Les hôtes ESXi haut de gamme sont réservés à une utilisation dans des clusters de production.

## <a name="avs-and-vmware-software"></a>Logiciel AVS et VMware

**Quelles sont les versions des logiciels VMware utilisées dans les clouds privés ?**

Les clouds privés utilisent vSphere 6.7, vSAN 6.7, HCX et NSX-T version 2.5.  

**Les clouds privés utilisent-ils VMware NSX ?**

Oui, NSX-T 2.5 est utilisé pour la mise en réseau définie par logiciel dans les clouds privés AVS.

**Puis-je utiliser VMware NSX-V dans un cloud privé ?**

Non. NSX-T est la seule version prise en charge de NSX.

**NSX est-il requis dans des environnements locaux ou des réseaux qui se connectent à un cloud privé.**

Non, vous n’êtes pas obligé d’utiliser NSX localement.

**Quelle est la planification de mise à niveau et de mise à jour des logiciels VMware dans un cloud privé ?**

Les mises à niveau de l’offre logicielle groupée de Cloud privé sont effectuées de façon à conserver une version unique de l’offre logicielle groupée la plus récente de VMware. Les versions des logiciels de cloud privé peuvent différer des versions les plus récentes des composants logiciels individuels (ESXi, NSX-T, vCenter, VSAN).

**À quelle fréquence la pile logicielle du cloud privé sera-t-elle mise à jour ?**

Les logiciels du cloud privé sont mis à niveau selon une planification qui suit celle des mises en production de l’offre logicielle groupée de VMware. Votre cloud privé ne nécessite pas de temps d’arrêt pour les mises à niveau.

## <a name="connectivity"></a>Connectivité

**Quelle est la planification d’adresse IP réseau requise pour incorporer des clouds privés avec des environnements locaux ?**

Un espace d’adressage /22 de réseau privé est requis pour déployer un cloud privé AVS. Cet espace d’adressage privé ne doit pas chevaucher d’autres réseaux virtuels dans un abonnement ou des réseaux locaux.
 
**Comment me connecter à partir d’environnements locaux à un cloud privé AVS ?**

Vous pouvez vous connecter au service de deux façons : 

- Avec une machine virtuelle ou une passerelle d’application déployée sur un réseau virtuel Azure appairé au cloud privé via ExpressRoute.
- Via ExpressRoute Global Reach, à partir de votre centre de données local vers un circuit Azure ExpressRoute.

**Comment connecter une machine virtuelle de charge de travail à Internet ou à un point de terminaison de service Azure ?**

Dans le portail Azure, activez la connectivité Internet pour un cloud privé. Avec le gestionnaire NSX-T, créez un routeur NSX-T T1 et un commutateur logique. Utilisez ensuite vCenter pour déployer une machine virtuelle sur le segment réseau défini par le commutateur logique. Cette machine virtuelle aura ainsi un accès réseau à Internet et aux services Azure.

**Dois-je restreindre l’accès à partir d’Internet à des machines virtuelles sur des réseaux logiques dans un cloud privé ?**

Non. Le trafic réseau entrant directement à partir d’Internet dans des clouds privés n’est pas autorisé.

**Dois-je restreindre l’accès à Internet de machines virtuelles sur des réseaux logiques ?**

Oui. Vous devez utiliser le gestionnaire NSX-T pour créer un pare-feu restreignant l’accès des machines virtuelles à Internet.

## <a name="accounts-and-privileges"></a>Comptes et privilèges

**Quels comptes et privilèges vais-je obtenir avec mon nouveau cloud privé AVS ?**

Vous recevez des informations d’identification d’utilisateur de CloudAdmin dans vCenter et un accès administrateur sur le gestionnaire NSX-T. Il existe également un groupe CloudAdmin qui peut être utilisé pour incorporer Azure Active Directory. Pour plus d’informations, consultez [Concepts d’accès et d’identité](concepts-identity.md).

**Un administrateur peut-il accéder à des hôtes ESXi ?**

Non, l’accès administrateur à ESXi est restreint conformément aux exigences de sécurité de la solution.

**Quels sont les privilèges et les autorisations dont j’ai besoin dans vCenter ?**

Vous disposez de privilèges de groupe CloudAdmin. Pour plus d’informations, consultez [Concepts d’accès et d’identité](concepts-identity.md).

**Quels sont les privilèges et autorisations dont j’ai besoin sur le gestionnaire NSX-T ?**

Vous disposerez de privilèges d’administrateur complets sur NSX-T et pourrez gérer le contrôle d’accès en fonction du rôle comme vous le feriez avec un centre de données NSX-T local. Pour plus d’informations, consultez [Concepts d’accès et d’identité](concepts-identity.md).

> [!NOTE]
> Un routeur T0 est créé et configuré dans le cadre d’un déploiement de cloud privé. Toute modification apportée à ce routeur logique ou aux machines virtuelles du nœud de périmètre NSX-T peut affecter la connectivité à votre cloud privé.

## <a name="billing-and-support"></a>Facturation et support

**Comment suis-je facturé pendant la période de préversion d’AVS ?**

La facturation d’AVS pendant la période de préversion est mensuelle et basée sur l’utilisation. Des options supplémentaires seront disponibles au moment de la mise en disponibilité générale.

**Comment la facturation est-elle structurée pendant la période de préversion d’AVS ?**

Pour toute question générale concernant la tarification, consultez la page de [tarification](https://azure.microsoft.com/pricing/details/azure-vmware) de la solution Azure VMware. La tarification de la préversion étant disponible sur demande, contactez votre équipe de compte ou cliquez sur le lien de la page de tarification pour contacter le service Ventes.

**Qui assure le support d’AVS ?**

Le support d’AVS est fourni par Microsoft. Veuillez noter que, conformément à nos directives concernant les préversions, nous assurons un support pendant les heures d’ouverture, du lundi au vendredi de 9 h à 17 h PST. Pour ouvrir un ticket de support, suivez [ce lien](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

**De quels comptes ai-je besoin pour créer un cloud privé AVS ?**

Vous devez disposer d’un compte Azure dans un abonnement Azure.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Comment faire une requête d’augmentation du quota d’hôtes pour une solution Azure VMware existante ?**

Pour demander une augmentation de quota, [envoyez une demande de support](..\azure-portal\supportability\how-to-create-azure-support-request.md). L’équipe de gestion des quotas évalue la demande et l’approuve dans un délai de trois jours ouvrés.  

> [!IMPORTANT]
> Avant de faire une requête d’augmentation de quota, veillez à inscrire le fournisseur de ressources **Microsoft.AVS** sur le Portail Azure.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Pour d’autres façons d’inscrire un fournisseur de ressources, consultez [Fournisseurs et types de ressources Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

1. Sur votre Portail Azure, créez une **Nouvelle demande de support** sous **Aide + Support** et indiquez les informations suivantes pour le ticket :
   - **Type de problème :** Techniques
   - **Abonnement :** Votre ID d’abonnement
   - **Service :**  Azure VMware Solution 
   - **Résumé :** Augmentation de quota
   - **Type de problème :** Problèmes de gestion de la capacité
   - **Sous-type de problème :** Demande client de capacité/quota d’hôtes supplémentaire

1. Dans la Description du ticket de support, indiquez les informations suivantes dans l’onglet Détails :
   - Nombre de nœuds supplémentaires   
   - Référence SKU de nœud
   - Région

   > [!NOTE] 
   > Par défaut, un minimum de quatre nœuds est accordé.

1. Cliquez sur **Vérifier + Créer** pour envoyer la demande.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
