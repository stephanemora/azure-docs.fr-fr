---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: df7a63c57674417bbee5bf04a154cf64bcdc474f
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910581"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

Avec notre SDK Android, vous pouvez vous abonner à la plupart des propriétés et collections pour être averti quand des valeurs changent.

## <a name="properties"></a>Propriétés
Pour vous abonner à des événements `property changed` :

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```
Quand vous utilisez des écouteurs d’événements qui sont définis dans la même classe, liez l’écouteur à une variable. Passez la variable en tant qu’argument pour ajouter et supprimer des méthodes d’écouteur.

Si vous essayez de passer l’écouteur directement comme argument, vous perdrez la référence à cet écouteur. Java crée de nouvelles instances de ces écouteurs et ne fait pas référence à celles créées précédemment. Elles se déclencheront toujours correctement, mais ne pourront pas être supprimées, car vous n’aurez plus de référence à celles-ci.


## <a name="collections"></a>Collections
Pour vous abonner à des événements `collection updated` :

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
