---
title: Xamarin. Android RatingBar
description: Comment ajouter un widget RatingBar à une activité Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 529fecb4e24e83ef7b783815843e132347d99262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029155"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin. Android RatingBar

Un RatingBar est un widget d’interface utilisateur qui affiche une évaluation d’une à cinq étoiles. L’utilisateur peut sélectionner une évaluation par touchant sur une étoile dans cette section, vous allez créer un widget qui permet à l’utilisateur de fournir une évaluation, avec le widget [`RatingBar`](xref:Android.Widget.RatingBar) .

![Exemple de RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="creating-a-ratingbar"></a>Création d’un RatingBar

1. Ouvrez le fichier **Resource/layout/main. AXML** et ajoutez le [`RatingBar`](xref:Android.Widget.RatingBar)
   élément (à l’intérieur du [`LinearLayout`](xref:Android.Widget.LinearLayout)) :

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   L’attribut `android:numStars` définit le nombre d’étoiles à afficher pour la barre d’évaluation. L’attribut `android:stepSize` définit la granularité de chaque étoile (par exemple, une valeur de `0.5` autorisera les évaluations de demi-étoile).

2. Pour effectuer une opération quand une nouvelle évaluation a été définie, ajoutez le code suivant à la fin de l' [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   méthode

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Cette opération capture le widget de [`RatingBar`](xref:Android.Widget.RatingBar) à partir de la disposition avec [`FindViewById`](xref:Android.App.Activity.FindViewById*) puis définit une méthode d’événement, puis définit l’action à effectuer lorsque l’utilisateur définit une évaluation. Dans ce cas, un message d' [`Toast`](xref:Android.Widget.Toast) simple affiche la nouvelle évaluation.

3. Exécutez l'application.
