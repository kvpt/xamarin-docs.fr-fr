---
titre : « Xamarin.Forms ProgressBar » Description : « Xamarin.Forms ProgressBar est un contrôle qui représente visuellement la progression sous la forme d’une barre horizontale qui est remplie en fonction d’une propriété de type float ».
ms. Prod : xamarin ms. assetId : C2F85FED-797C-466B-A0FD-E73CFB79B267 ms. Technology : xamarin-Forms Author : profexorgeek ms. Author : jusjohns ms. Date : 07/09/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-progressbar"></a>Xamarin.FormsProgressBar
[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)

Le Xamarin.Forms [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) contrôle représente visuellement la progression sous la forme d’une barre horizontale qui est remplie à un pourcentage représenté par une `float` valeur. La `ProgressBar` classe hérite de [`View`](xref:Xamarin.Forms.View) .

Les captures d’écran suivantes montrent une `ProgressBar` sur iOS et Android :

![Capture d’écran de ProgressBar sur iOS et Android](progressbar-images/progressbars-default.png "ProgressBar sur iOS et Android")

Le `ProgressBar` contrôle définit deux propriétés :

* [`Progress`](xref:Xamarin.Forms.ProgressBar.Progress)est une `float` valeur qui représente la progression actuelle sous la forme d’une valeur comprise entre 0 et 1. `Progress`les valeurs inférieures à 0 seront ancrées à 0, les valeurs supérieures à 1 seront ancrées à 1.
* [`ProgressColor`](xref:Xamarin.Forms.ProgressBar.ProgressColor)est un `Color` qui affecte la couleur de la barre intérieure représentant la progression actuelle.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que `ProgressBar` peut être stylisé et être la cible des liaisons de données.

Le `ProgressBar` contrôle définit également une `ProgressTo` méthode qui anime la barre de sa valeur actuelle à une valeur spécifiée. Pour plus d’informations, consultez [animer un ProgressBar](#animate-a-progressbar).

> [!NOTE]
> L' `ProgressBar` option n’accepte pas la manipulation de l’utilisateur. elle est donc ignorée lors de l’utilisation de la touche Tab pour sélectionner des contrôles.

## <a name="create-a-progressbar"></a>Créer un ProgressBar

Un `ProgressBar` peut être instancié en XAML. Sa `Progress` propriété détermine le pourcentage de remplissage de la barre de couleur interne. La valeur par défaut de la `Progress` propriété est 0. L’exemple suivant montre comment instancier un `ProgressBar` en XAML avec le `Progress` jeu de propriétés facultatif :

```xaml
<ProgressBar Progress="0.5" />
```

Un `ProgressBar` peut également être créé dans le code :

```csharp
ProgressBar progressBar = new ProgressBar { Progress = 0.5f };
```

> [!WARNING]
> N’utilisez pas d’options de disposition horizontale sans contrainte, telles que `Center` , `Start` ou `End` avec `ProgressBar` . Sur UWP, le `ProgressBar` réduit à une barre de zéro largeur. Conservez la `HorizontalOptions` valeur par défaut `Fill` et n’utilisez pas de largeur `Auto` pour placer un `ProgressBar` dans une `Grid` disposition.

## <a name="progressbar-appearance-properties"></a>Propriétés d’apparence de ProgressBar

La `ProgressColor` propriété définit la couleur de la barre intérieure lorsque la `Progress` propriété est supérieure à zéro. L’exemple suivant montre comment instancier un `ProgressBar` en XAML avec le `ProgressColor` jeu de propriétés :

```xaml
<ProgressBar ProgressColor="Orange" />
```

La `ProgressColor` propriété peut également être définie lors de la création `ProgressBar` d’un dans le code :

```csharp
ProgressBar progressBar = new ProgressBar { ProgressColor = Color.Orange };
```

Les captures d’écran suivantes montrent le `ProgressBar` avec la `ProgressColor` propriété définie `Color.Orange` sur iOS et Android :

![Capture d’écran d’un style ProgressBar sur iOS et Android](progressbar-images/progressbars-styled.png "ProgressBar stylisé sur iOS et Android")

## <a name="animate-a-progressbar"></a>Animer un ProgressBar

La `ProgressTo` méthode anime à `ProgressBar` partir de sa `Progress` valeur actuelle jusqu’à une valeur fournie dans le temps. La méthode accepte une `float` valeur de progression, une `uint` durée en millisecondes, une `Easing` valeur enum et retourne un `Task<bool>` . Le code suivant montre comment animer un `ProgressBar` :

```csharp
// animate to 75% progress over 500 milliseconds with linear easing
await progressBar.ProgressTo(0.75, 500, Easing.Linear);
```

Pour plus d’informations sur l' `Easing` énumération, consultez [fonctions Xamarin.Forms d’accélération dans ](~/xamarin-forms/user-interface/animation/easing.md).

## <a name="related-links"></a>Liens connexes

* [Démonstrations de ProgressBar](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-progressbardemos/)
