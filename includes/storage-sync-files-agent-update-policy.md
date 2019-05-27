---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 4f59f68c1598f737ea7cb3a0e8046fc0779ed9d3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113802"
---
L’agent Azure File Sync est mis à jour régulièrement pour ajouter de nouvelles fonctionnalités et pour résoudre les problèmes. Nous vous recommandons de configurer Microsoft Update pour obtenir les mises à jour de l’agent Azure File Sync lorsqu’elles sont disponibles.

#### <a name="major-vs-minor-agent-versions"></a>Versions d’agent majeures et mineures
* Les versions majeures de l’agent contiennent souvent de nouvelles fonctionnalités, et la première partie du numéro de version est constituée d’un nombre croissant. Exemple : \*2.\*.\*\*
* Les versions mineures de l’agent sont également appelées « correctifs », et sont publiées plus fréquemment que les versions majeures. Elles contiennent souvent des correctifs de bogues et des améliorations mineures, mais pas de nouvelles fonctionnalités. Par exemple, \*\*.3.\*\*

#### <a name="upgrade-paths"></a>Chemins de mise à jour
Il existe quatre moyens testés et approuvés d’installer les mises à jour de l’agent Azure File Sync. 
1. **(Recommandé) Configurez Microsoft Update pour télécharger et installer automatiquement les mises à jour de l’agent.**  
    Il est recommandé d’installer toutes les mises à jour Azure File Sync pour bénéficier des derniers correctifs de l’agent du serveur. Microsoft Update rend ce processus transparent, en téléchargeant et en installant automatiquement les mises à jour.
2. **Utilisez AfsUpdater.exe pour télécharger et installer les mises à jour de l’agent.**  
    AfsUpdater.exe se trouve dans le répertoire d’installation de l’agent. Double-cliquez sur l’exécutable pour télécharger et installer les mises à jour de l’agent. 
3. **Appliquez un correctif à un agent Azure File Sync existant à l’aide d’un fichier de correctif Microsoft Update, ou d’un exécutable .msp. Le dernier package de mise à jour Azure File Sync peut être téléchargé à partir du [catalogue Microsoft Update](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync).**  
    L’exécution d’un exécutable .msp permet de mettre à jour votre installation d’Azure File Sync avec la même méthode que celle utilisée automatiquement par Microsoft Update dans le chemin de mise à jour précédent. L’application d’un correctif Microsoft Update effectue la mise à jour sur place d’une installation Azure File Sync.
4. **Télécharger le programme d’installation de l’agent Azure File Sync plus récente à partir de la [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257).**  
    Pour mettre à niveau une installation existante de l’agent Azure File Sync, désinstallez l’ancienne version, puis installez la dernière version avec le programme d’installation téléchargé. L’inscription du serveur, les groupes de synchronisation et tous les autres paramètres sont gérés par le programme d’installation d’Azure File Sync.

#### <a name="automatic-agent-lifecycle-management"></a>Gestion de cycle de vie d’agent automatique
Avec la version 6 de l’agent, l’équipe de synchronisation de fichiers a introduit une fonctionnalité de mise à niveau automatique de l’agent. Vous pouvez sélectionner une des deux modes et spécifiez une fenêtre de maintenance dans lequel la mise à niveau doit être tentée sur le serveur. Cette fonctionnalité est conçue pour vous aider à la gestion du cycle de vie de l’agent soit en fournissant une barrière de sécurité empêche votre agent à partir de l’expiration ou sans tracas, ce qui permet de rester le paramètre actuel.
1. Le **paramètre par défaut** va tenter d’empêcher l’agent à partir de l’expiration. Sous 21 jours après la date d’expiration publiée d’un agent, l’agent tente de mettre à niveau automatique. Il démarre une tentative de mise à niveau une fois par semaine de 21 jours avant l’expiration et dans la fenêtre de maintenance sélectionné. **Cette option n’élimine pas la nécessité de prendre les correctifs réguliers de Microsoft Update.**
2. Si vous le souhaitez, vous pouvez sélectionner que l’agent met automatiquement à niveau lui-même dès qu’une nouvelle version de l’agent est disponible. Vous également se produire lors de la fenêtre de maintenance sélectionné et votre serveur tirer parti des nouvelles fonctionnalités et améliorations dès qu’elles sont généralement disponibles. Il s’agit du paramètre recommandé, sans souci qui fournira les versions majeures de l’agent, ainsi que des correctifs de mise à jour régulières à votre serveur.

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>Cycle de vie de l’agent et garanties liées à la gestion des changements
Azure File Sync est un service cloud, ce qui introduit en permanence de nouvelles fonctionnalités et améliorations. Cela signifie qu’une version de l’agent Azure File Sync n’est prise en charge que pour une durée limitée. Pour faciliter le déploiement, les règles suivantes vous assurer que suffisamment de temps et notification pour prendre en charge de l’agent mises à jour/les mises à niveau dans votre processus de gestion des changements :

- Les versions majeures et mineures de l’agent sont prises en charge au moins six mois après leur publication initiale.
- Nous garantissons une période de chevauchement de trois mois minimum entre chaque version majeure d’agent. 
- Des avertissements sont émis pour les serveurs inscrits qui utilisent un agent sur le point d’expirer au moins trois mois avant son expiration. Vous pouvez vérifier si un serveur inscrit utilise une version antérieure de l’agent dans la section relative aux serveurs inscrits d’un service de synchronisation de stockage.
- La durée de vie d’une version mineure de l’agent est liée à la version majeure à laquelle elle est associée. Par exemple, lorsque la version 3.0 de l’agent est publiée, les versions 2.\* sont toutes définies pour expirer ensemble.

> [!Note]
> Si vous installez une version de l’agent sur le point d’expirer, un avertissement d’expiration s’affiche, mais ne vous empêche pas de l’installer. En revanche, l’installation et la connexion à une version de l’agent ayant expiré ne sont pas prises en charge et seront bloquées.
