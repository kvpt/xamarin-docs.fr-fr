---
titre : ' Xamarin.Forms ScrollView’Description : 'cet article explique comment utiliser la Xamarin.Forms classe ScrollView pour présenter des mises en page qui ne tiennent pas sur un seul écran, et qui ont du contenu faire de la place pour le clavier.
ms. Prod : ms. AssetID : ms. Technology : Auteur : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView)contient des dispositions et leur permet de faire défiler l’écran. `ScrollView`est également utilisé pour permettre aux affichages de se déplacer automatiquement vers la partie visible de l’écran lorsque le clavier est affiché.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Objectif

[`ScrollView`](xref:Xamarin.Forms.ScrollView)peut être utilisé pour s’assurer que les affichages les plus grands s’affichent correctement sur des téléphones plus petits. Par exemple, une disposition qui fonctionne sur un iPhone 6S peut être coupée sur un iPhone. L’utilisation d’un `ScrollView` autorise l’affichage des parties découpées de la disposition sur l’écran plus petit.

## <a name="usage"></a>Utilisation

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)les objets ne doivent pas être imbriqués. En outre, les objets ne `ScrollView` doivent pas être imbriqués avec d’autres contrôles qui permettent de faire défiler, comme `ListView` et `WebView` .

[`ScrollView`](xref:Xamarin.Forms.ScrollView)expose une `Content` propriété, qui peut être définie sur une vue ou une disposition unique. Prenons l’exemple d’une disposition avec un très grand boxView, suivi d’un `Entry` :

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

En C# :

```csharp
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Avant de faire défiler l’utilisateur, seul le `BoxView` est visible :

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Notez que lorsque l’utilisateur commence à entrer du texte dans le `Entry` , la vue défile pour rester visible à l’écran :

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propriétés

[`ScrollView`](xref:Xamarin.Forms.ScrollView)définit les propriétés suivantes :

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty)Obtient une [`Size`](xref:Xamarin.Forms.Size) valeur qui représente la taille du contenu.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty)Obtient ou définit une [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) valeur d’énumération qui représente la direction de défilement du `ScrollView` .
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty)Obtient un `double` qui représente la position de défilement X actuelle.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty)Obtient un `double` qui représente la position de défilement Y actuelle.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty)Obtient ou définit une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur qui représente le moment où la barre de défilement horizontale est visible.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty)Obtient ou définit une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur qui représente le moment où la barre de défilement verticale est visible.

> [!NOTE]
> Le défilement peut être désactivé en affectant [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) à la propriété la valeur `Neither` .

## <a name="methods"></a>Méthodes

[`ScrollView`](xref:Xamarin.Forms.ScrollView)fournit une `ScrollToAsync` méthode, qui peut être utilisée pour faire défiler la vue soit à l’aide de coordonnées, soit en spécifiant une vue particulière qui doit être rendue visible.

Lorsque vous utilisez des coordonnées, spécifiez les `x` `y` coordonnées et, ainsi qu’une valeur booléenne indiquant si le défilement doit être animé :

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> La `ScrollToAsync` méthode ne provoque pas de défilement lorsque la [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) propriété a la valeur `Neither` .

Lors du défilement vers un élément particulier, l' `ScrollToPosition` énumération spécifie l’emplacement où l’élément apparaîtra dans la vue :

- **Centre** &ndash; fait défiler l’élément jusqu’au centre de la partie visible de la vue.
- **Fin** &ndash; fait défiler l’élément jusqu’à la fin de la partie visible de la vue.
- **MakeVisible** &ndash; fait défiler l’élément pour qu’il soit visible dans la vue.
- **Démarrer** &ndash; fait défiler l’élément jusqu’au début de la partie visible de la vue.

La `IsAnimated` propriété spécifie le mode de défilement de la vue. Lorsque la valeur `true` est, une animation lisse est utilisée, au lieu de déplacer instantanément le contenu dans la vue.

## <a name="events"></a>Événements

[`ScrollView`](xref:Xamarin.Forms.ScrollView)définit un seul événement, `Scrolled` . `Scrolled`est déclenché lorsque la vue a fini de faire défiler. Le gestionnaire d’événements pour `Scrolled` accepte `ScrolledEventArgs` , qui a `ScrollX` les `ScrollY` Propriétés et. L’exemple suivant montre comment mettre à jour une étiquette avec la position de défilement actuelle d’un `ScrollView` :

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Notez que les positions de défilement peuvent être négatives, en raison de l’effet de rebond lors du défilement à la fin d’une liste.

## <a name="related-links"></a>Liens connexes

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
