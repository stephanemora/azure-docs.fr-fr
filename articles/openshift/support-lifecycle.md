---
title: Cycle de vie du support d’Azure Red Hat OpenShift
description: Découvrez le cycle de vie du support et les versions prises en charge d’Azure Red Hat OpenShift.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/06/2021
ms.openlocfilehash: 35cbaf2b6d2618d116d2dd322ae5ac28f578cecc
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303540"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Cycle de vie du support pour Azure Red Hat OpenShift 4

Red Hat publie des versions mineures de Red Hat OpenShift Container Platform (OCP) tous les trois mois environ. Ces versions contiennent de nouvelles fonctionnalités et des améliorations. Les publications de correctifs, plus fréquentes (généralement hebdomadaires), sont destinées seulement aux correctifs de bogues critiques au sein d’une version mineure. Elles peuvent comporter des correctifs pour les failles de sécurité et les bogues majeurs.

Azure Red Hat OpenShift s’appuie sur certaines versions d’OCP. Cet article traite des versions d’OCP prises en charge pour Azure Red Hat OpenShift et donne des informations sur les mises à niveau, les dépréciations et la stratégie de support.

## <a name="red-hat-openshift-versions"></a>Versions de Red Hat OpenShift

Red Hat OpenShift Container Platform utilise la Gestion sémantique de version. Celle-ci consiste à associer un niveau de numéros de version différent pour chaque niveau de contrôle de version. Le tableau suivant illustre les différentes parties d’un numéro de version sémantique, dans ce cas 4.4.11.

|Version majeure (x)|Version mineure (y)|Correctif (z)|
|-|-|-|
|4|4|11|

Chaque chiffre de la version indique la compatibilité générale avec la version précédente :

* **Version majeure** : aucune publication de version majeure de version n’est planifiée pour l’instant. Les versions principales changent lorsque des modifications incompatibles de l’API ou la rétrocompatibilité peuvent être rompues.
* **Version mineure** : elles sont publiées tous les trois mois environ. Les mises à niveau de versions mineures peuvent inclure des ajouts de fonctionnalités, des améliorations, des dépréciations, des suppressions, des correctifs de bogues, des renforcements de la sécurité et d’autres améliorations.
* **Correctifs** : ils sont généralement publiés toutes les semaines, ou selon les besoins. Les mises à niveau de versions correctives peuvent inclure des correctifs de bogues, des renforcements de la sécurité et d’autres améliorations.

Les clients doivent chercher à exécuter la dernière version mineure de la version principale qu’ils possèdent. Par exemple, si votre cluster de production tourne sur la version 4.4 alors que la dernière version mineure en disponibilité générale pour la série 4 est la version 4.5, passez dès que possible à la version 4.5. 

### <a name="upgrade-channels"></a>Canaux de mise à niveau

Les canaux de mise à niveau sont liés à une version mineure de Red Hat OpenShift Container Platform (OCP). Par exemple, ceux d’OCP 4.4 n’incluront jamais de mise à niveau vers une version 4.5. Les canaux de mise à niveau contrôlent uniquement la sélection des publications, sans aucun impact sur la version du cluster.

Azure Red Hat OpenShift 4 ne prend en charge que les canaux stables (par exemple, stable-4.4).

Il est possible d’utiliser le canal stable-4.5 pour effectuer une mise à niveau à partir d’une version mineure précédente d’Azure Red Hat OpenShift. Les clusters mis à niveau à l’aide de canaux rapide, préversion et version finale ne sont pas pris en charge.

Si vous passez à un canal qui n’inclut pas votre version actuelle, une alerte s’affiche et aucune mise à jour ne peut être recommandée. Cependant, vous pouvez à tout moment et sans risque revenir à votre canal d’origine.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Stratégie de prise en charge des versions de Red Hat OpenShift Container Platform

Azure Red Hat OpenShift prend en charge deux versions mineures en disponibilité générale (GA) de Red Hat OpenShift Container Platform :
* la dernière version mineure GA publiée dans Azure Red Hat OpenShift (que nous appellerons N) ;
* une version mineure précédente (N-1).

Si elles sont disponibles dans un canal de mise à niveau stable, les versions mineures plus récentes (N+1, N+2) disponibles dans OCP en amont sont susceptibles d’être prises en charge également.

Les mises à jour correctives critiques sont appliquées automatiquement aux clusters par des ingénieurs SRE (Site Reliability Engineers) Azure Red Hat OpenShift. Les clients qui le souhaitent sont libres d’installer des mises à jour correctives à l’avance.

Par exemple, si Azure Red Hat OpenShift introduit 4.5.z aujourd’hui, la prise en charge est assurée pour les versions suivantes :

|Nouvelle version mineure|Liste des versions prises en charge|
|-|-|
|4.5.z|4.5.z, 4.4.z|

« .z » est représentatif des versions correctives. Si elle est disponible dans un canal de mise à niveau stable, les clients peuvent également passer à la version 4.6. z.

Quand une nouvelle version mineure est introduite, la version mineure la plus ancienne est déconseillée et supprimée. Supposons par exemple que la liste des versions actuellement prises en charge soit 4.5.z et 4.4.z. Quand Azure Red Hat OpenShift publie 4.6.z, la version 4.4.z est supprimée et n’est plus prise en charge dans les 30 jours.

> [!NOTE]
> Les clients qui utilisent une version de Red Hat OpenShift non prise en charge sont invités à effectuer une mise à niveau lorsqu’ils formulent une demande de support pour le cluster. Les clusters exécutant des versions de Red Hat OpenShift non prises en charge ne sont pas couverts par le contrat SLA Azure Red Hat OpenShift.

## <a name="release-and-deprecation-process"></a>Processus de publication et de dépréciation

Pour connaître les publications de versions et les dépréciations à venir, consultez le Calendrier de publication Azure Red Hat OpenShift.

Pour les nouvelles versions mineures de Red Hat OpenShift Container Platform :
* L’équipe SRE Azure Red Hat OpenShift publie une annonce préalable avec la date prévisionnelle de la publication d’une nouvelle version et de la dépréciation de l’ancienne version correspondante dans les [Notes de publication Azure Red Hat OpenShift](https://github.com/Azure/OpenShift/releases) au moins 30 jours avant la suppression.
* L’équipe SRE Azure Red Hat OpenShift publie une notification d’intégrité des services accessible à tous les utilisateurs disposant d’un accès à Azure Red Hat OpenShift et au portail, et envoie aux administrateurs des abonnements un e-mail précisant les dates prévisionnelles de suppression des versions.
* Les clients ont 30 jours à compter de la suppression d’une version pour effectuer une mise à niveau vers une version mineure prise en charge afin de continuer à bénéficier du support.

Pour les nouvelles versions correctives de Red Hat OpenShift Container Platform :
* En raison de leur nature urgente, les versions correctives sont susceptibles d’être introduites dans le service par l’équipe SRE Azure Red Hat OpenShift dès qu’elles sont disponibles.
* En général, l’équipe Azure Red Hat OpenShift SRE n’effectue pas de communications de grande ampleur au sujet de l’installation de nouvelles versions correctives. Toutefois, elle surveille et valide constamment les correctifs CVE disponibles pour les prendre en charge le plus tôt possible. Si une action de leur part est nécessaire, elle informe les clients de la mise à niveau.

## <a name="supported-versions-policy-exceptions"></a>Exceptions de la stratégie de version prise en charge

L’équipe SRE Azure Red Hat OpenShift se réserve le droit d’ajouter ou de supprimer de nouvelles versions, de supprimer des versions existantes ou de retarder la publication de versions mineures s’il a été identifié qu’elles présentent un ou plusieurs problèmes de sécurité ou bogues critiques ayant un impact sur la production, et ce, sans préavis.

Il est possible d’ignorer certaines versions correctives ou d’accélérer le déploiement en fonction de la gravité du bogue ou du problème de sécurité.

## <a name="azure-portal-and-cli-versions"></a>Versions du Portail Azure et de l’interface CLI

Un cluster Azure Red Hat OpenShift déployé sur le portail ou avec Azure CLI est toujours défini par défaut sur la dernière version mineure (N) et le dernier correctif critique. Par exemple, si Azure Red Hat OpenShift prend en charge 4.5.z et 4.4.z, la version par défaut pour les nouvelles installations est 4.5.z. Les clients qui souhaitent utiliser la dernière version mineure d’OCP en amont (N+1, N+2) peuvent à tout moment mettre à niveau leur cluster vers n’importe quelle version disponible dans les canaux de mise à niveau stables.

## <a name="azure-red-hat-openshift-release-calendar"></a>Calendrier de publication Azure Red Hat OpenShift

Consultez le guide suivant pour connaître [l’historique des versions de Red Hat OpenShift Container Platform (en amont)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|Version d’OCP|Sortie en amont|Disponibilité générale d’Azure Red Hat OpenShift|Fin de vie|
|-|-|-|-|
|4.3|Janvier 2020|Avril 2020| Août 2020|
|4.4|Mai 2020|Juillet 2020|4.6 GA|
|4,5|Juillet 2020| Novembre 2020|4.7 GA
|4,6|Octobre 2020| Février 2021|4.8 GA|

## <a name="faq"></a>Questions fréquentes (FAQ)

**Que se passe-t-il quand un utilisateur met à niveau un cluster OpenShift avec une version mineure non prise en charge ?**

Si vous utilisez la version N-2 ou une version antérieure, vous êtes hors support et il vous sera demandé d’effectuer une mise à niveau pour continuer à profiter du support. Une fois votre mise à niveau de la version N-2 à la version N-1 réussie, vous bénéficiez à nouveau du support. La mise à niveau à partir d’une version N-3 ou d’une version antérieure vers une version prise en charge peut s’avérer difficile, voire impossible. Nous vous recommandons de conserver votre cluster avec la dernière version de OpenShift pour éviter d’éventuels problèmes de mise à niveau. Par exemple :
* Si la version d’Azure Red Hat OpenShift la plus ancienne prise en charge est 4.4.z et que vous possédez la version 4.3.z ou une version antérieure, vous êtes hors support.
* Une fois la mise à niveau de la version 4.3.z à la version 4.4.z ou une version ultérieure réussie, vous bénéficiez à nouveau de nos stratégies de support. 

Le retour d’un cluster à une version antérieure, ou restauration, n’est pas pris en charge. Seule la mise à niveau vers une version plus récente est possible.

**Que signifie « être hors support » ?**

Si votre cluster ARO exécute une version d’OpenShift qui ne figure pas dans la liste des versions prises en charge ou qui utilise une [configuration de cluster non prise en charge](https://docs.microsoft.com/azure/openshift/support-policies-v4), votre cluster est « hors support ». Par conséquent :
- Lorsque vous ouvrez un ticket de support pour votre cluster, vous êtes invité à mettre à niveau le cluster vers une version prise en charge. avant de recevoir le support, sauf si vous vous trouvez dans la période de grâce de 30 jours après la fin de la prise en charge de la version. 
- Toutes les garanties de runtime ou de SLA pour les clusters en dehors du support sont annulées.
- Les clusters hors support seront corrigés de manière optimale.
- Les clusters hors support ne seront pas analysés.
