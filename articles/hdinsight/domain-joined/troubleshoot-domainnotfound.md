---
title: Échec de création du cluster avec l’erreur DomainNotFound dans Azure HDInsight
description: Étapes de détection de problèmes et résolutions possibles pour les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: adcdafbc07fa0a8cc6970ab227b52aee798b084f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76776050"
---
# <a name="scenario-cluster-creation-fails-with-domainnotfound-error-in-azure-hdinsight"></a>Scénario : Échec de création du cluster avec l’erreur DomainNotFound dans Azure HDInsight

Cet article décrit les éventuelles solutions à appliquer pour résoudre les problèmes rencontrés lors d’interactions avec des clusters Azure HDInsight.

## <a name="issue"></a>Problème

La création de cluster HDI Secure (Pack Sécurité Entreprise) échoue avec le message d’erreur `DomainNotFound`.

## <a name="cause"></a>Cause

Paramètres DNS incorrects.

## <a name="resolution"></a>Résolution

Lors du déploiement des clusters joints à un domaine, HDI crée un nom d’utilisateur et un mot de passe internes dans AAD DS (pour chaque cluster) et joint tous les nœuds de cluster à ce domaine. La jonction de domaine s’effectue à l’aide d’outils Samba. Vérifiez que les prérequis suivants sont remplis :

* Le nom de domaine doit être résolu via DNS.
* L’adresse IP des contrôleurs de domaine doit être définie dans les paramètres DNS du réseau virtuel sur lequel le cluster est déployé.
* Si le réseau virtuel est pairé avec le réseau virtuel d’AAD DS, vous devrez le faire manuellement.
* Si vous utilisez des redirecteurs DNS, le nom de domaine doit être résolu correctement dans le réseau virtuel.
* Les stratégies de sécurité (NSG) ne doivent pas bloquer la jonction de domaine.

### <a name="additional-debugging-steps"></a>Étapes de débogage supplémentaires

* Déployer une machine virtuelle Windows dans le même sous-réseau, joindre le domaine à l’ordinateur à l’aide d’un nom d’utilisateur et d’un mot de passe (cette opération peut être effectuée via l’interface utilisateur du Panneau de configuration), ou

* Déployer une machine virtuelle Ubuntu dans le même sous-réseau et joindre le domaine à l’ordinateur
  * Se connecter avec SSH à l’ordinateur
  * sudo su
  * Exécuter le script avec le nom d’utilisateur et le mot de passe
  * Le script effectue un test ping et crée les fichiers config requis, puis le domaine. En cas de succès, vos paramètres DNS sont corrects.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure en faisant appel au [Support de la communauté Azure](https://azure.microsoft.com/support/community/).

* Connectez-vous à [@AzureSupport](https://twitter.com/azuresupport), le compte Microsoft Azure officiel pour améliorer l’expérience client. Connexion de la communauté Azure aux ressources appropriées : réponses, support technique et experts.

* Si vous avez besoin d’une aide supplémentaire, vous pouvez envoyer une requête de support à partir du [Portail Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Sélectionnez **Support** dans la barre de menus, ou ouvrez le hub **Aide + Support**. Pour plus d’informations, consultez [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L’accès au support relatif à la gestion et à la facturation des abonnements est inclus avec votre abonnement Microsoft Azure. En outre, le support technique est fourni avec l’un des [plans de support Azure](https://azure.microsoft.com/support/plans/).
