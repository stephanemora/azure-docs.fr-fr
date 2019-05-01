---
title: Considérations de sécurité Azure Container Instances
description: Recommandations pour sécuriser des images et des secrets pour Azure Container Instances et les considérations de sécurité générales pour n’importe quelle plateforme de conteneur
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943995"
---
# <a name="security-considerations-for-azure-container-instances"></a>Considérations de sécurité pour Azure Container Instances

Cet article présente les considérations de sécurité relatives à l’aide d’Azure Container Instances pour exécuter des applications de conteneur. Les sujets abordés sont les suivants :

> [!div class="checklist"]
> * **Recommandations de sécurité** pour la gestion des images et des secrets pour Azure Container Instances
> * **Considérations pour l’écosystème de conteneurs** tout au long du cycle de vie de conteneur, pour n’importe quelle plateforme de conteneur

## <a name="security-recommendations-for-azure-container-instances"></a>Recommandations de sécurité pour Azure Container Instances

### <a name="use-a-private-registry"></a>Utiliser un Registre privé

Les conteneurs sont créés à partir d’images stockées dans un ou plusieurs référentiels. Ces référentiels peuvent appartenir à un registre public, comme [Docker Hub](https://hub.docker.com), ou vers un Registre privé. À l’inverse, [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/) est un registre privé pouvant être installé en local ou dans un cloud privé virtuel. Vous pouvez également utiliser les services de Registre de conteneurs privés basés sur le cloud, y compris [Azure Container Registry](../container-registry/container-registry-intro.md). 

Une image conteneur disponible publiquement ne garantit pas la sécurité. Images de conteneur se composent de plusieurs couches logicielles, et chaque couche logicielle peut avoir des vulnérabilités. Pour réduire les risques d’attaques, vous devez stocker et récupérer des images à partir d’un Registre privé, tel que Azure Container Registry ou Docker Trusted Registry. En plus de fournir un Registre privé géré, Azure Container Registry prend en charge [l’authentification par principal du service](../container-registry/container-registry-authentication.md) via Azure Active Directory pour les flux d’authentification de base. Cette authentification inclut l’accès en fonction du rôle en lecture seule (pull), écriture (push) et les autorisations de propriétaire.

### <a name="monitor-and-scan-container-images"></a>Surveiller et analyser des images de conteneur

Sécurité et l’analyse de solutions comme [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) et [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) sont disponibles via la place de marché Azure. Vous pouvez les utiliser pour analyser les images de conteneur dans un Registre privé et identifier les vulnérabilités potentielles. Il est important de comprendre la profondeur de l’analyse que les différentes solutions fournissent. 

### <a name="protect-credentials"></a>Protéger les informations d’identification

Conteneurs puissent réparties sur plusieurs clusters et les régions Azure. Par conséquent, vous devez sécuriser les informations d’identification requises pour les connexions ou l’accès à l’API, telles que les mots de passe ou des jetons. Assurez-vous que seuls les utilisateurs privilégiés peuvent accéder à ces conteneurs en transit et au repos. Inventorier tous les secrets d’informations d’identification et de demander aux développeurs d’utiliser les outils de gestion des secrets émergentes qui sont conçues pour des plateformes de conteneurs.  Assurez-vous que votre solution inclut des bases de données chiffrées, le chiffrement TLS pour les données de secrets en transit et privilèges [contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md). [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) est un service cloud qui protège les clés de chiffrement et de secrets (tels que les certificats, les chaînes de connexion et les mots de passe) pour les applications en conteneur. Étant donné que ces données sont sensibles et critique pour l’entreprise, un accès sécurisé à votre clé coffres afin que seuls les applications autorisées et les utilisateurs peuvent y accéder.

## <a name="considerations-for-the-container-ecosystem"></a>Considérations pour l’écosystème de conteneurs

Les mesures de sécurité suivantes, bien implémentée et gérés de façon efficace, peuvent vous aider à sécuriser et protéger votre écosystème de conteneurs. Ces mesures s’appliquent tout au long du cycle de vie du conteneur, du développement via le déploiement de production et à un éventail de plates-formes, les hôtes et les orchestrateurs de conteneurs. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Utilisez la gestion des vulnérabilités dans le cadre de votre cycle de vie de développement de conteneur 

À l’aide de gestion des vulnérabilités efficace tout au long du cycle de vie de développement de conteneur, vous améliorer les chances que vous identifier et résolvez les problèmes de sécurité avant qu’ils deviennent un problème plus sérieux. 

### <a name="scan-for-vulnerabilities"></a>Rechercher les vulnérabilités 

De nouvelles vulnérabilités tout le temps, afin de l’analyse et l’identification des vulnérabilités sont un processus continu. Incorporer tout au long du cycle de vie du conteneur d’analyse des vulnérabilités :

* En tant qu’une vérification finale dans votre pipeline de développement, vous devez effectuer une analyse des vulnérabilités sur les conteneurs avant de lui envoyer les images vers un registre public ou privé. 
* Continuer à rechercher des images de conteneur dans le Registre pour identifier tout défaut qui ont été manquées d’une certaine manière pendant le développement et pour résoudre les vulnérabilités récemment découvertes qui peuvent exister dans le code utilisé dans les images de conteneur.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Mapper les vulnérabilités d’image à l’exécution de conteneurs 

Vous devez disposer d’un moyen de vulnérabilités de mappage identifié dans les images de conteneur à l’exécution des conteneurs, donc les problèmes de sécurité peuvent être atténués ou résolus.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>S’assurer que seules des images approuvées sont utilisés dans votre environnement 

Il existe suffisamment de modification et de volatilité dans un écosystème de conteneurs sans autoriser aussi aux conteneurs inconnus. Autoriser les images de conteneur approuvées uniquement. Ont des outils et processus en place pour surveiller et empêcher l’utilisation d’images de conteneur non approuvées. 

Un moyen efficace de réduire la surface d’attaque et empêche les développeurs de commettre des erreurs critiques de sécurité consiste à contrôler le flux d’images de conteneur dans votre environnement de développement. Par exemple, vous pouvez approuver une distribution Linux unique comme une image de base, de préférence un qui est au plus juste (Alpine ou CoreOS plutôt que Ubuntu), pour réduire la surface des attaques potentielles. 

Image de la signature ou empreinte peut fournir une chaîne de responsabilité qui vous permet de vérifier l’intégrité des conteneurs. Par exemple, Azure Container Registry prend en charge de Docker [approbation de contenu](https://docs.docker.com/engine/security/trust/content_trust) de modèle, ce qui permet des éditeurs d’images signer les images qui sont déplacées vers un Registre et consommateurs d’image pour extraire uniquement les images signés.

### <a name="permit-only-approved-registries"></a>Autoriser uniquement les registres approuvées 

Une extension de s’assurer que votre environnement utilise uniquement des images approuvées consiste à autoriser uniquement l’utilisation de registres de conteneurs approuvées. Exiger l’utilisation des registres de conteneurs approuvées réduit votre exposition aux risques en limitant les risques de l’introduction de vulnérabilités inconnues ou des problèmes de sécurité. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Garantir l’intégrité des images tout au long du cycle de vie 

Une partie de la gestion de la sécurité tout au long du cycle de vie du conteneur consiste à garantir l’intégrité des images conteneur dans le Registre et qu’ils sont modifiés ou déployés en production. 

* Images avec des vulnérabilités, même mineures, ne doivent pas être autorisés à exécuter dans un environnement de production. Dans l’idéal, toutes les images déployées en production doivent être enregistrés dans un Registre privé uniquement accessible à quelques. Limitez le nombre d’images de production pour vous assurer qu’ils peuvent être gérés efficacement.

* Comme il est difficile d’identifier l’origine des logiciels à partir d’une image conteneur disponible publiquement, créer des images à partir de la source pour être certain de connaître l’origine de la couche. En cas de vulnérabilités dans une image conteneur auto-créée, les clients peuvent plus rapidement trouver une solution. Avec une image publique, les clients devraient trouver la racine d’une image publique pour résoudre le problème ou obtenir une autre image sécurisée du serveur de publication. 

* Une image soigneusement analysée et déployée en production n’est pas garantie pour être à jour pour la durée de vie de l’application. Des vulnérabilités de sécurité peuvent être signalées pour des couches de l’image qui n’étaient pas connues précédemment ou qui ont été introduites après le déploiement en production. 

  Auditez régulièrement les images déployées en production pour identifier les images qui sont obsolètes ou n’ont pas été mis à jour depuis un certain temps. Vous pouvez utiliser des méthodologies de déploiement bleu-vert et propagée des mécanismes de mise à niveau pour mettre à jour les images de conteneur sans temps d’arrêt. Vous pouvez analyser des images à l’aide des outils décrits dans la section précédente. 

* Utilisez un pipeline d’intégration continue (CI) avec la sécurité intégrée d’analyse pour générer des images sécurisés et les transmettre à votre Registre privé. L’analyse des vulnérabilités intégrée à la solution CI garantit que les images réussissant tous les tests sont transmises au registre privé à partir duquel les charges de travail de production sont déployées. 

  Une erreur de pipeline CI garantit que les images vulnérables ne sont pas transmises au Registre privé qui est utilisé pour les déploiements de charge de travail de production. Elle automatise aussi l’analyse s’il existe un nombre important d’images de la sécurité image. Il s’agit d’une bonne alternative aux audits manuels des images visant à contrôler la présence de vulnérabilités de sécurité, qui sont des processus longs, fastidieux et sujets aux erreurs. 

### <a name="enforce-least-privileges-in-runtime"></a>Appliquer des privilèges minimum dans le runtime 

Le concept des privilèges minimum est une recommandation de sécurité de base qui s’applique également aux conteneurs. Lorsqu’une vulnérabilité est exploitée, il donne généralement l’accès et privilèges égaux à ceux de l’application compromise ou d’un processus. S’assurer que les conteneurs fonctionnent avec le moins de privilèges et accès requis pour faire leur travail réduit votre exposition aux risques. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Réduire la surface d’attaque de conteneur en supprimant les privilèges inutiles 

Vous pouvez également réduire la surface d’attaque potentielle en supprimant tout processus inutiles ou non utilisés ou des privilèges à partir de l’exécution du conteneur. Conteneurs privilégiés sont exécutés en tant que racine. Si un utilisateur malveillant ou la charge de travail d’échappement dans un conteneur privilégié, le conteneur est exécuté en tant que racine sur ce système.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Fichiers de liste verte et les exécutables que le conteneur est autorisé à accéder à ou exécuter 

En réduisant le nombre de variables ou inconnues vous permet de maintenir un environnement stable et fiable. La limitation de conteneurs pour qu’ils peuvent accéder ou exécution préapprouvée uniquement ou les fichiers dans la liste verte et exécutables est une méthode éprouvée de limiter l’exposition aux risques.  

Il est beaucoup plus facile à gérer une liste verte en cas d’implémentation à partir du début. Une liste verte fournit une mesure de contrôle et la facilité de gestion que vous découvrez quels fichiers et les fichiers exécutables sont requis pour l’application fonctionne correctement. 

Une liste d’autorisation non seulement réduit la surface d’attaque mais peut également fournir une ligne de base pour les anomalies et empêcher les cas d’utilisation du « voisin bruyant » et des scénarios de dérivation de conteneur. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Appliquer la segmentation du réseau sur l’exécution de conteneurs  

Pour aider à protéger des conteneurs dans un sous-réseau à partir de risques de sécurité dans un autre sous-réseau, maintenir la segmentation du réseau (ou la segmentation de nano) ou répartition entre les conteneurs en cours d’exécution. Mise à jour la segmentation du réseau peut également être nécessaire pour l’utilisation de conteneurs dans les secteurs qui sont nécessaires pour répondre aux obligations de conformité.  

Par exemple, l’outil de partenaire [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) fournit une approche automatisée pour la segmentation de nano. Aqua surveille les activités de réseau de conteneur dans le runtime. Il identifie toutes les connexions réseau entrantes et sortantes vers/à partir d’autres conteneurs, les services, les adresses IP et l’internet public. NANO-segmentation est automatiquement créée selon le trafic analysé. 

### <a name="monitor-container-activity-and-user-access"></a>Surveiller l’accès utilisateur et d’activité de conteneur 

Comme avec n’importe quel environnement informatique, vous devez régulièrement surveiller activité et l’accès utilisateur à votre écosystème de conteneurs pour rapidement identifier toute activité suspecte ou malveillante. Azure fournit un conteneur, y compris des solutions de surveillance :

* [Azure Monitor pour les conteneurs](../azure-monitor/insights/container-insights-overview.md) pour surveiller les performances de vos charges de travail déployée dans les environnements Kubernetes hébergés sur Azure Kubernetes Service (AKS). Azure Monitor pour les conteneurs vous permet de surveiller les performances en collectant des métriques sur le processeur et la mémoire à partir des contrôleurs, des nœuds et des conteneurs qui sont disponibles dans Kubernetes via l’API Metrics. 

* Le [solution Azure Container Monitoring](../azure-monitor/insights/containers.md) vous permet de consultez et gérez d’autres Docker et Windows des hôtes de conteneur dans un emplacement unique. Par exemple : 

  * Afficher les informations d’audit détaillées qui affiche les commandes utilisées avec des conteneurs. 
  * Résoudre les problèmes de conteneurs à afficher et rechercher des journaux centralisés sans devoir afficher à distance les hôtes Docker ou Windows.  
  * Rechercher des conteneurs qui peuvent être bruyants et consommant des ressources excessives sur un ordinateur hôte.
  * Afficher centralisées sur le processeur, mémoire, stockage et informations de performances et l’utilisation du réseau de conteneurs.  

  La solution prend en charge les orchestrateurs de conteneurs, y compris de Docker Swarm, DC/OS, non managé Kubernetes, Service Fabric et Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Surveiller l’activité des ressources de conteneur 

Surveiller votre activité des ressources, telles que des fichiers, de réseau et d’autres ressources qui accèdent à vos conteneurs. Il est utile pour l’analyse des performances et une mesure de sécurité d’analyser la consommation et l’activité des ressources. 

[Azure Monitor](../azure-monitor/overview.md) active la surveillance globale des services Azure en permettant la collecte de métriques, de journaux d’activité et de journaux de diagnostic. Par exemple, le journal d’activité indique quand des ressources sont créées ou modifiées. 

Les métriques fournissent des statistiques sur les performances pour différentes ressources, voire pour le système d’exploitation à l’intérieur d’une machine virtuelle. Vous pouvez afficher ces données avec un des explorateurs dans le portail Azure et créer des alertes en fonction de leurs mesures. Azure Monitor fournit que les mesures les plus rapides de pipeline (5 minutes à 1 minute), vous devez l’utiliser pour les notifications et alertes critiques. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Connecter tous les accès d’utilisateur administratif de conteneur pour l’audit 

Maintenir une piste d’audit précise d’un accès administratif à votre écosystème de conteneurs, Registre de conteneurs et des images de conteneur. Ces journaux peuvent être nécessaires pour les opérations d’audit et seront utiles en tant que preuves après un incident de sécurité. Vous pouvez utiliser la [solution Azure Container Monitoring](../azure-monitor/insights/containers.md) pour atteindre ce but. 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la gestion des vulnérabilités de conteneur avec les solutions de [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) et [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* En savoir plus sur [sécurité des conteneurs dans Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).