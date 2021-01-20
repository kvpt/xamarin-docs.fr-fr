---
ms.openlocfilehash: 62706082496732f60136e4283d5ff7a087488dd3
ms.sourcegitcommit: b75c369adb8e02a429b6c0fed8ba4a855099bf01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557155"
---
Avant de tenter ce didacticiel, vous devez avoir suivi les guides et didacticiels suivants :

- Guide de démarrage rapide [Générer votre première application Xamarin.Forms](~/get-started/first-app/index.md).
- Didacticiel [StackLayout](~/get-started/tutorials/stacklayout/index.yml).
- Didacticiel [Button](~/get-started/tutorials/button/index.yml).
- Didacticiel [Entry](~/get-started/tutorials/entry/index.yml).
- Tutoriel [CollectionView](~/get-started/tutorials/collectionview/index.yml).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Utiliser le Gestionnaire de package NuGet pour ajouter un package SQLite.NET à un projet Xamarin.Forms.
> - Créer les classes d’accès aux données.
> - Utiliser les classes d’accès aux données.

Vous allez utiliser Visual Studio 2019 ou Visual Studio pour Mac pour créer une application simple qui montre comment stocker des données dans une base de données SQLite.NET locale. Les captures d’écran suivantes montrent l’application finale :

[![Capture d’écran montrant la persistance des données dans une base de données SQLite.NET locale, sur iOS et Android](../images/consume-data-access-classes-reduced.png "Persistance des données dans une base de données locale")](../images/consume-data-access-classes-large.png#lightbox "Persistance des données dans une base de données locale")
