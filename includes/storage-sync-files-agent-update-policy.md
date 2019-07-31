---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 9b8812b1fca6a72a69f06a6c0278da8ee4d4c852
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841529"
---
L’agent Azure File Sync est mis à jour régulièrement pour ajouter de nouvelles fonctionnalités et pour résoudre les problèmes. Nous vous recommandons de configurer Microsoft Update pour obtenir les mises à jour de l’agent Azure File Sync lorsqu’elles sont disponibles.

#### <a name="major-vs-minor-agent-versions"></a>Versions d’agent majeures et mineures
* Les versions majeures de l’agent contiennent souvent de nouvelles fonctionnalités, et la première partie du numéro de version est constituée d’un nombre croissant. Par exemple :  \*2.\*.\*\*
* Les versions mineures de l’agent sont également appelées « correctifs », et sont publiées plus fréquemment que les versions majeures. Elles contiennent souvent des correctifs de bogues et des améliorations mineures, mais pas de nouvelles fonctionnalités. Par exemple, \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Chemins de mise à jour
Il existe quatre moyens testés et approuvés d’installer les mises à jour de l’agent Azure File Sync. 
1. **(Recommandé) Configurez Microsoft Update pour télécharger et installer automatiquement les mises à jour de l’agent.**  
    Il est recommandé d’installer toutes les mises à jour Azure File Sync pour bénéficier des derniers correctifs de l’agent du serveur. Microsoft Update rend ce processus transparent, en téléchargeant et en installant automatiquement les mises à jour.
2. **Utilisez AfsUpdater.exe pour télécharger et installer les mises à jour de l’agent.**  
    AfsUpdater.exe se trouve dans le répertoire d’installation de l’agent. Double-cliquez sur l’exécutable pour télécharger et installer les mises à jour de l’agent. 
3. **Appliquez un correctif à un agent Azure File Sync existant à l’aide d’un fichier de correctif Microsoft Update, ou d’un exécutable .msp. Le dernier package de mise à jour Azure File Sync peut être téléchargé à partir du [catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    L’exécution d’un exécutable .msp permet de mettre à jour votre installation d’Azure File Sync avec la même méthode que celle utilisée automatiquement par Microsoft Update dans le chemin de mise à jour précédent. L’application d’un correctif Microsoft Update effectue la mise à jour sur place d’une installation Azure File Sync.
4. **Téléchargez le programme d’installation de l’agent Azure File Sync le plus récent à partir du [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Pour mettre à niveau une installation existante de l’agent Azure File Sync, désinstallez l’ancienne version, puis installez la dernière version avec le programme d’installation téléchargé. L’inscription du serveur, les groupes de synchronisation et tous les autres paramètres sont gérés par le programme d’installation d’Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Gestion automatique du cycle de vie des agents
Avec la version 6 de l’agent, l’équipe de synchronisation des fichiers a introduit une fonctionnalité de mise à niveau automatique de l’agent. Vous pouvez sélectionner l’un des deux modes et spécifier un intervalle de maintenance durant lequel une tentative de mise à niveau sur le serveur sera effectuée. Cette fonctionnalité est conçue pour vous aider à gérer le cycle de vie des agents en fournissant une barrière de sécurité qui empêche votre agent d’expirer ou en permettant une configuration qui vous tient informé en toute simplicité.
1. Le **paramètre par défaut** tente d’empêcher l’agent d’expirer. Sous 21 jours après la publication de la date d’expiration d’un agent, l’agent tentera de se mettre à niveau automatiquement. Il essaye de se mettre à niveau une fois par semaine pendant les 21 jours qui précèdent l’expiration et au cours de l’intervalle de maintenance sélectionné. **Même avec cette option, il est toujours nécessaire d’installer les correctifs réguliers de Microsoft Update.**
2. Si vous le souhaitez, vous pouvez configurer une mise à niveau automatique de l’agent dès qu’une nouvelle version est disponible (actuellement non applicable aux serveurs en cluster). Cette mise à jour se produira au cours de l’intervalle de maintenance sélectionné et permettra à votre serveur de profiter des nouvelles fonctionnalités et améliorations dès qu’elles sont mises à la disposition générale. Il s’agit du paramètre recommandé et sans encombre. Il fournira les versions majeures de l’agent ainsi que des correctifs de mise à jour réguliers à votre serveur. Chaque agent publié offre une qualité de niveau GA. Si vous optez pour la mise à jour automatique lorsqu’une nouvelle version est disponible, il n'est pas certain que la mise à jour vous soit immédiatement proposée après sa publication. Les nouveaux agents se voient d'abord proposer un petit nombre de serveurs, après quoi l'offre est progressivement étendue. Une fois la version d’évaluation terminée, l’agent sera également disponible sur Microsoft Update et le [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257).

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Cycle de vie de l’agent et garanties liées à la gestion des changements
Azure File Sync est un service cloud qui permet l’introduction continue de nouvelles fonctionnalités et améliorations. Cela signifie qu’une version de l’agent Azure File Sync n’est prise en charge que pour une durée limitée. Pour faciliter le déploiement, appliquez les règles suivantes pour être sûr de disposer de suffisamment de temps et d’informations pour les mises à jour ou mises à niveau lors de la gestion des modifications :

- Les versions majeures et mineures de l’agent sont prises en charge au moins six mois après leur publication initiale.
- Nous garantissons une période de chevauchement de trois mois minimum entre chaque version majeure d’agent. 
- Des avertissements sont émis pour les serveurs inscrits qui utilisent un agent sur le point d’expirer au moins trois mois avant son expiration. Vous pouvez vérifier si un serveur inscrit utilise une version antérieure de l’agent dans la section relative aux serveurs inscrits d’un service de synchronisation de stockage.
- La durée de vie d’une version mineure de l’agent est liée à la version majeure à laquelle elle est associée. Par exemple, lorsque la version 3.0 de l’agent est publiée, les versions 2.\* sont toutes définies pour expirer ensemble.

> [!Note]
> Si vous installez une version de l’agent sur le point d’expirer, un avertissement d’expiration s’affiche, mais ne vous empêche pas de l’installer. En revanche, l’installation et la connexion à une version de l’agent ayant expiré ne sont pas prises en charge et seront bloquées.
