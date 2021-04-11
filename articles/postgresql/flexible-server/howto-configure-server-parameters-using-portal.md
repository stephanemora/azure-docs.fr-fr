---
title: Configurer les paramètres de serveur - Portail Azure - Azure Database pour PostgreSQL - Serveur flexible
description: Cet article décrit comment configurer les paramètres Postgres dans Azure Database pour PostgreSQL - Serveur flexible par le biais du portail Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 29d85cc33dc55f3819bba6898a265c46b7e7ef85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607437"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Configurer les paramètres de serveur dans Azure Database pour PostgreSQL - Serveur flexible par le biais du portail Azure 

Vous pouvez lister, afficher et mettre à jour les paramètres de configuration d’un serveur flexible Azure Database pour PostgreSQL par le biais du portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour parcourir ce guide pratique, vous avez besoin des éléments suivants :
- [Azure Database pour PostgreSQL - Serveur flexible](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>Affichage et modification des paramètres

1. Ouvrez le [portail Azure](https://portal.azure.com).

2. Sélectionnez votre serveur flexible.

3. Dans la section **Paramètres**, sélectionnez **Paramètres du serveur**. La page affiche une liste de paramètres avec leurs valeurs et leurs descriptions.
![Page de vue d’ensemble des paramètres](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Sélectionnez le bouton **déroulant** pour afficher les valeurs possibles des paramètres de type énuméré comme client_min_messages.
![Bouton déroulant Énumérer](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Sélectionnez ou placez le curseur sur le bouton **i** (informations) pour afficher la plage des valeurs possibles des paramètres numériques comme cpu_index_tuple_cost.
![Bouton Informations](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. Si nécessaire, utilisez la **zone de recherche** pour limiter la recherche à un paramètre spécifique. La recherche s’effectue sur le nom et la description des paramètres.
![Résultats de la recherche](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Modifiez les valeurs de paramètres souhaitées. Toutes les modifications que vous apportez dans une session sont surlignées en violet. Une fois que vous avez modifié les valeurs, vous pouvez sélectionner **Enregistrer**. Vous pouvez également **Abandonner** vos modifications.
![Enregistrer ou annuler les modifications](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Si vous avez enregistré de nouvelles valeurs pour les paramètres, vous pouvez toujours rétablir toutes les valeurs par défaut en sélectionnant **Rétablir toutes les valeurs par défaut**.
![Rétablir toutes les valeurs par défaut](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Étapes suivantes

Vous en saurez plus sur :

- [Vue d’ensemble des paramètres de serveur dans Azure Database pour PostgreSQL](concepts-servers.md)
- [Configuration des paramètres à l’aide d’Azure CLI](howto-configure-server-parameters-using-cli.md)
