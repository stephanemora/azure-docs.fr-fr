---
title: Fichier Include
description: Fichier Include
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008499"
---
Si vous envisagez de passer à l’article recommandé suivant, vous pouvez conserver les ressources et configurations que vous avez créées afin de les réutiliser.

Sinon, vous pouvez supprimer les ressources Azure et les configurations locales que vous avez créées dans cet article pour éviter les frais. 

> [!IMPORTANT]
> La suppression des ressources et des groupes de ressources Azure est irréversible. Une fois supprimés, le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement les mauvaises ressources ou le mauvais groupe de ressources. Si vous avez créé l’IoT Hub à l’intérieur d’un groupe de ressources existant qui contient des ressources que vous souhaitez conserver, supprimez uniquement la ressource de l’IoT Hub au lieu de supprimer le groupe de ressources.
>

Exécutez la commande suivante pour supprimer uniquement l’IoT Hub. Remplacez \<YourIoTHub > par le nom de votre IoT Hub et \<TestResources > par le nom de votre groupe de ressources :

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Pour supprimer un groupe de ressources entier par nom :

1. Connectez-vous au [Portail Azure](https://portal.azure.com), puis sélectionnez **Groupes de ressources**.

2. Dans la zone de texte **Filtrer par nom...**, saisissez le nom du groupe de ressources contenant votre IoT Hub. 

3. À droite de votre groupe de ressources, dans la liste des résultats, sélectionnez les points de suspension (**...**), puis **Supprimer le groupe de ressources**.

    ![Supprimer un groupe de ressources](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Vous êtes invité à confirmer la suppression du groupe de ressources. Saisissez à nouveau le nom de votre groupe de ressources pour confirmer, puis sélectionnez **Supprimer**. Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.






