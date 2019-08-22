---
title: Xamarin. Android RatingBar
description: Comment ajouter un widget RatingBar à une activité Android.
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: de63a0f3f6564671a50594c66b55ed095329c95c
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887636"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin. Android RatingBar

Un RatingBar est un widget d’interface utilisateur qui affiche une évaluation d’une à cinq étoiles. L’utilisateur peut sélectionner une évaluation par touchant sur une étoile dans cette section, vous allez créer un widget qui permet à l’utilisateur de fournir une évaluation, avec [`RatingBar`](xref:Android.Widget.RatingBar) le widget.

![Exemple de RatingBar](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>Création d’un RatingBar

1. Ouvrez le fichier **Resource/layout/main. AXML** et ajoutez le[`RatingBar`](xref:Android.Widget.RatingBar)
   élément (à l' [`LinearLayout`](xref:Android.Widget.LinearLayout)intérieur de):

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   L' `android:numStars` attribut définit le nombre d’étoiles à afficher pour la barre d’évaluation. L' `android:stepSize` attribut définit la granularité pour chaque étoile (par exemple, une valeur de `0.5` autorise les évaluations de demi-étoile).

2. Pour effectuer une opération quand une nouvelle évaluation a été définie, ajoutez le code suivant à la fin de la[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   méthode

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    Cela permet de capturer [`RatingBar`](xref:Android.Widget.RatingBar) le widget à partir de [`FindViewById`](xref:Android.App.Activity.FindViewById*) la disposition avec, puis de définir une méthode d’événement, puis de définir l’action à effectuer lorsque l’utilisateur définit une évaluation. Dans ce cas, un message [`Toast`](xref:Android.Widget.Toast) simple affiche la nouvelle évaluation.

3. Exécutez l'application.

