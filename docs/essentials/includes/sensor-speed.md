---
ms.topic: include
ms.openlocfilehash: b82ebeaeb28195e3ec0f5a0200c0448b6b76196a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61259474"
---
## <a name="sensor-speed"></a>[Vitesse de capteur](xref:Xamarin.Essentials.SensorSpeed)

- **Le plus rapide** : obtenir les données de capteur aussi rapidement que possible (aucune garantie de retour sur le thread d’interface utilisateur).
- **Jeu** : débit adapté aux jeux (aucune garantie de retour sur le thread d’interface utilisateur).
- **Par défaut** - Taux par défaut adapté aux changements d’orientation de l’écran.
- **UI** : débit adapté à une interface utilisateur général.

Si votre gestionnaire d’événement n’est pas garanti d’exécuter sur le thread d’interface utilisateur, et si le gestionnaire d’événements doit accéder aux éléments d’interface utilisateur, utilisez la [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) méthode pour exécuter ce code sur le thread d’interface utilisateur.
