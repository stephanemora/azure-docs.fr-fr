---
title: Utiliser des Azure Data Science Virtual Machines
description: Apprenez à vous connecter à une machine Azure DSVM (Data Science Virtual Machine) pour étendre la puissance de calcul disponible à Azure Notebooks (préversion).
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: 291f1ac093568f50ad6146b70fa1ef69263b7b5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85830302"
---
# <a name="use-azure-data-science-virtual-machines"></a>Utiliser des Azure Data Science Virtual Machines

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Par défaut, les projets se trouvent au niveau de **calcul Gratuit**, limité à 4 Go de mémoire et à 1 Go de données pour éviter les abus. Vous pouvez contourner ces limitations en utilisant une autre machine virtuelle configurée dans un abonnement Azure. La meilleure solution à cela est une Data Science Virtual Machine (DSVM) utilisant l’image **Data Science Virtual Machine pour Linux (Ubuntu)** . Une DSVM est préconfigurée avec tout ce dont vous avez besoin pour Azure Notebooks et s’affiche automatiquement dans la liste déroulante **Exécuter** d’Azure Notebooks.

> [!Note]
> Azure Notebooks est pris uniquement en charge sur les DSVM créées sur lesquelles l’image Linux Ubuntu est active. Les Notebooks ne sont pas pris en charge sur les images Windows 2012, Windows 2016 ou Linux CentOS.

## <a name="create-a-dsvm-instance"></a>Créer une instance DSVM

Pour créer une instance DSVM, suivez les instructions de la section [Créer une machine virtuelle Data Science Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Pour plus d’informations, notamment des détails de tarification, consultez la page [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="connect-to-the-dsvm"></a>Se connecter à la machine DSVM

Lorsque vous avez créé la DSVM, sélectionnez la liste déroulante **Exécuter** sur le tableau de bord du projet Azure Notebooks, puis sélectionnez l’instance DSVM appropriée. La liste déroulante affiche des instances DSVM si les conditions suivantes sont remplies :

- Vous avez ouvert une session Azure Notebooks avec un compte qui utilise Azure Active Directory (AAD), par exemple un compte professionnel.
- Votre compte est connecté à un abonnement Azure.
- Vous disposez d’au moins une machine virtuelle dans cet abonnement, avec au minimum un accès en lecture, qui utilise l’image Data Science Virtual Machine pour Linux (Ubuntu).

![Instances Data Science Virtual Machine dans la liste déroulante sur le tableau de bord du projet](media/project-compute-tier-dsvm.png)

Lorsque vous sélectionnez une instance DSVM, Azure Notebooks peut vous demander les informations d’identification de l’ordinateur utilisé lors de la création de la machine virtuelle.

> [!Important]
> Le nom d’utilisateur doit être en minuscules pour pouvoir être utilisé avec JupyterHub.

Si les conditions ne sont pas remplies, vous pouvez toujours vous connecter à la DSVM. Dans la liste déroulante, sélectionnez l’option **Calcul direct**. Vous êtes alors invité à entrer un nom (à afficher dans la liste), l’adresse IP et le port (généralement le port 8000, qui est le port par défaut sur lequel JupyterHub écoute) de la machine virtuelle, ainsi que les informations d’identification de cette dernière :

![Invite à collecter des informations sur le serveur pour l’option Calcul direct](media/project-compute-tier-direct.png)

Ces valeurs sont disponibles dans la page DSVM du portail Azure.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Accès aux fichiers Azure Notebooks à partir de la DSVM

L’accès au système de fichiers est pris en charge pour les versions DSVM 19.06.15 ou ultérieures. Pour vérifier la version, commencez par vous connecter à votre DSVM via SSH, puis exécutez la commande suivante : `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` (vous devez utiliser l’adresse IP exacte indiquée ici). Le numéro de version est indiqué dans la sortie de « version ».

Pour que la parité des chemins de fichiers reste conforme au niveau de **calcul gratuit**, vous ne pouvez ouvrir qu’un seul projet à la fois sur une DSVM. Pour ouvrir un nouveau projet, vous devez tout d’abord arrêter le projet ouvert.

Lorsqu’un projet est exécuté sur une machine virtuelle, les fichiers sont montés sur le répertoire racine du serveur Jupyter (le répertoire indiqué dans JupyterHub), en remplaçant les fichiers Azure Notebooks par défaut. Lorsque vous arrêtez la machine virtuelle à l’aide du bouton **Arrêter** de l’interface utilisateur du notebook, Azure Notebooks restaure les fichiers par défaut.

![Bouton d’arrêt dans Azure Notebooks](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Créer de nouveaux utilisateurs de DSVM

Si plusieurs utilisateurs partagent une DSVM, vous pouvez éviter qu’ils ne se bloquent entre eux en créant et en utilisant un utilisateur de DSVM pour chaque utilisateur de notebook :

1. Sur le [portail Azure](https://portal.azure.com), accédez à votre machine virtuelle.
1. Sous **Support et dépannage** dans la marge de gauche, sélectionnez **Réinitialiser le mot de passe**.
1. Entrez un nouveau **nom d’utilisateur**. Le nom d’utilisateur doit être en minuscules pour pouvoir être utilisé avec JupyterHub. Entrez un mot de passe. Sélectionnez ensuite **Mettre à jour**. (Les noms d’utilisateur existants ne sont pas affectés.)
1. Répétez l’étape précédente pour les autres utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les DSVM dans [Présentation d’Azure Data Science Virtual Machine](/azure/machine-learning/data-science-virtual-machine/overview).
