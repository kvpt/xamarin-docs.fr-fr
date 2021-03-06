---
ms.openlocfilehash: cae1ec70880fe694584e14b732608725bcbb1d91
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "71059648"
---
Avant de tenter ce didacticiel, vous devez avoir suivi les guides et didacticiels suivants :

- Guide de démarrage rapide [Générer votre première application Xamarin.Forms](~/get-started/first-app/index.md).
- Didacticiel [StackLayout](~/get-started/tutorials/stacklayout/index.yml).
- Didacticiel [Entry](~/get-started/tutorials/entry/index.yml).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Répondre au démarrage, à la mise en veille ou à la sortie de veille d’une application.
> - Conserver les données quelle que soit l’évolution de leur état pendant leur cycle de vie.

Vous allez utiliser Visual Studio 2019 ou Visual Studio pour Mac pour créer une application simple qui montre comment conserver les données à travers les changements d’état du cycle de vie. Les captures d’écran suivantes montrent l’application finale :

[![Capture d’écran d’une entrée dont la propriété Text est conservée après les changements d’état du cycle de vie, sur iOS et Android](../images/persist-data.png "Entrée dont la propriété Text est conservée après les changements d’état du cycle de vie")](../images/persist-data-large.png#lightbox "Entrée dont la propriété Text est conservée après les changements d’état du cycle de vie")
